---
name: reg-feed-watcher
description: Cheque feeds regulatórios agora e reporte o que é novo desde o último check, filtrado pelo seu limiar de materialidade. Use quando o usuário disser "cheque os feeds", "o que tem de novo", "atualização regulatória", quando rodando a partir do agente agendado, ou quando colando manualmente um desenvolvimento regulatório para classificação e diff.
argument-hint: "[opcional: --since DATA]"
---

> **Adaptação BR não oficial.** Calibrado para DOU (in.gov.br), planalto.gov.br, e sítios oficiais de ANPD, CVM, BCB, ANVISA, ANATEL, ANS, ANEEL, ANP, ANTT, ANAC, IBAMA, CADE, SENACON, Procons, COAF. Lei 9.784/1999, LINDB, Dec. 10.411/2020 (AIR).

# /reg-feed-watcher

1. Carregue `~/.claude/plugins/config/claude-for-legal/regulatorio/CLAUDE.md` → watchlist, limiar de materialidade, config de feeds.
2. Use o workflow abaixo.
3. Puxe cada feed. Filtre por materialidade.
4. Saída: o que é novo, categorizado por tier de materialidade.

---

## Purpose

Puxe os feeds. Filtre por materialidade. Produza o que sobrar. O filtro é o
valor — feeds não filtrados são ruído.

## Load context

`~/.claude/plugins/config/claude-for-legal/regulatorio/CLAUDE.md` → watchlist, limiar de materialidade, config de feeds, path de saída do digest (se setado).

`references/source-catalog.md` (na pasta desta skill) → catálogo curado de fontes RSS/JSON/HTML do DOU, agências federais (ANPD, CVM, BCB, ANVISA, ANATEL, ANS, ANEEL, ANP, ANTT, ANAC, IBAMA, CADE, SENACON, COAF), diários estaduais, tribunais (STF, STJ, TRFs), e fontes secundárias. Use ao configurar novas fontes ou quando a watchlist tiver lacunas de cobertura (veja Step 0).

## Workflow

### Step 0: Coverage check (antes de puxar)

Antes de rodar o pull, compare a watchlist + config de feeds em CLAUDE.md contra `references/source-catalog.md`:

- Quais categorias (federal / estadual / setorial / autorregulação) o usuário se importa per watchlist?
- Quais dessas categorias têm zero ou pouquíssimas fontes configuradas?

Se houver lacuna óbvia — ex.: usuário monitora "agências federais" mas só tem `anpd.gov.br` configurado, faltando CVM, BCB — sinalize uma vez no topo do digest:

> **Coverage gap notada:** Sua watchlist inclui [categoria], mas apenas [N] feeds configurados. O source catalog lista [X] opções nesta categoria (ex.: [top 2-3 nomes]). Quer sugestões? Rode `/regulatorio:cold-start-interview --redo` para atualizar, ou edite `~/.claude/plugins/config/claude-for-legal/regulatorio/CLAUDE.md` direto.

Não importune com a mesma lacuna repetidamente — se o usuário disse "skip Procons estaduais por ora", respeite. Note isso em CLAUDE.md para que cole.

### Step 1: Pull

Puxe de todos os tiers configurados. Toda instalação tem Tier 1. Tiers 2 e 3 são aditivos — use se configurados, pule se não.

**Tier 1 — Feeds gratuitos (sempre ativos)**

Para cada regulador na watchlist:

- **API do DOU (Imprensa Nacional)** (`https://www.in.gov.br/leiturajornal`) — sítio oficial do Diário Oficial da União, queries por seção (1: atos oficiais e normativos; 2: atos militares; 3: contratos e editais; extra: atos especiais), data e palavra-chave. Cobre todas as agências federais. A query é restrita; combine com sítios das agências para enriquecimento.
- **Sítio oficial das agências** — fetch e parse de páginas/feeds RSS configuradas em ~/.claude/plugins/config/claude-for-legal/regulatorio/CLAUDE.md:
  - **ANPD** — gov.br/anpd (notícias, consultas públicas, normas)
  - **CVM** — gov.br/cvm (deliberações, consultas, processos administrativos sancionadores)
  - **BCB** — bcb.gov.br (resoluções, circulares, comunicados; Open Finance: Resolução Conjunta 1/2020; PIX)
  - **ANVISA** — gov.br/anvisa (RDC, comunicados)
  - **ANATEL** — gov.br/anatel (resoluções, consultas)
  - **ANS** — gov.br/ans (RN, súmulas normativas)
  - **CADE** — gov.br/cade (atos de concentração, sanções)
  - **SENACON** — gov.br/mj (atos consumeristas) + Procons estaduais
- **STF / STJ** — busca em portal.stf.jus.br e stj.jus.br para temas regulatórios (ADIs, ADCs, Súmulas Vinculantes, Temas de Repercussão Geral, Temas Repetitivos relevantes).
- **planalto.gov.br** — texto consolidado de leis federais e decretos.

Slug de agência / link de referência para watchlists comuns:
| Regulador | Sítio oficial |
|---|---|
| ANPD | gov.br/anpd |
| CVM | gov.br/cvm |
| BCB | bcb.gov.br |
| ANVISA | gov.br/anvisa |
| ANATEL | gov.br/anatel |
| ANS | gov.br/ans |
| ANEEL | gov.br/aneel |
| ANP | gov.br/anp |
| ANTT | gov.br/antt |
| ANAC | gov.br/anac |
| CADE | gov.br/cade |
| IBAMA | gov.br/ibama |
| SENACON | gov.br/mj/pt-br/assuntos/seus-direitos/consumidor |
| COAF | gov.br/coaf |

Para regulador não nesta lista: cheque gov.br/<sigla> ou faça fallback para RSS direto.

**Tier 2 — Feeds pagos (se configurados)**

- **Thomson Reuters Regulatory Intelligence MCP** (Brasil): query por atualizações desde último check, filtradas à watchlist.
- **Aurum / JusBrasil / LexML MCP** (se disponível): mesmo.

Deduplique entre tiers — o mesmo documento pode aparecer em múltiplas fontes. Prefira a fonte mais rica para o output enriquecido.

**No silent supplement.** Se o pull retornar poucos ou nenhum resultado para regulador na watchlist, reporte o achado e pare. NÃO preencha por web search ou conhecimento do modelo sem perguntar. Diga: "O check de feed retornou [N] itens de [reguladores acertados]. Cobertura aparenta fina para [regulador / tópico]. Opções: (1) ampliar a janela de data, (2) tentar feed ou MCP diferente, (3) buscar na web — resultados marcados `[web search — verify]` e que devem ser checados contra o site oficial antes de confiar, ou (4) pare aqui. Qual você prefere?" O(a) advogado(a) decide se aceita fontes de menor confiança; Claude não decide por eles.

**Source attribution.** Tag cada citação e item regulatório com a origem: `[DOU]`, `[ANPD]`, `[CVM]`, `[BCB]`, `[ANVISA]`, `[ANATEL]`, `[ANS]`, `[ANEEL]`, `[CADE]`, `[STF]`, `[STJ]`, `[TR]`, ou o nome específico da ferramenta MCP para itens recuperados via conector; `[web search — verify]` para itens de web search; `[model knowledge — verify]` para itens recordados do training; `[user provided]` para itens colados. Itens marcados `verify` carregam risco mais alto de fabricação que itens recuperados por ferramenta e devem ser checados primeiro. Nunca tire ou consolide as tags — são o sinal mais rápido para o usuário sobre o que verificar.

**Secondary sources.** Algumas entradas do catálogo (Migalhas, JOTA, Conjur, Valor Econômico, escritórios — Mattos Filho, Pinheiro Neto, Machado Meyer, Veirano, BMA, Demarest etc.) reportam ação regulatória primária mas não são a fonte primária. Tag qualquer item puxado destes feeds como `[secondary source]` além do feed-name tag — ex.: `[JOTA] [secondary source]`. No digest, quando item de fonte secundária descrever ação de regulador, acrescente: "→ Trace ao primário: [link ao site oficial da agência se conhecido, senão 'ache em [sítio do regulador].gov.br antes de confiar']." Não classifique item de fonte secundária como "Sempre material" sozinho — bump para tier abaixo até localizar a fonte primária.

**Tier 3 — Entrada manual**

Se o usuário colou texto regulatório ou sumário em vez de invocar de check agendado: trate o conteúdo colado como item único, pule para Step 2 para classificação, e registre fonte como "manual entry". Sem pull de feed. Este caminho funciona independentemente de status de assinatura.

Registre o timestamp do check após pull. Próximo run agendado puxa daqui adiante.

### Step 2: Classify

Cada item ganha tier de materialidade per `~/.claude/plugins/config/claude-for-legal/regulatorio/CLAUDE.md`:

| Tipo de item | Match contra threshold |
|---|---|
| Norma final (Resolução, RDC, Circular, Instrução Normativa, Portaria) | Em geral "sempre material" |
| Minuta de norma / Consulta pública | Em geral "review-worthy" — e sempre logar prazo de manifestação |
| Tomada de subsídios (pré-consulta) | Review-worthy para **estratégia**, não compliance — ainda sem exigências, mas sinaliza direção e tem prazo real de manifestação. Logue o prazo. Roteie para `/regulatorio:policy-diff` apenas como pré-posicionamento, não diff de fechamento. |
| AIR (Análise de Impacto Regulatório, Dec. 10.411/2020) | Mesmo que tomada de subsídios — pré-norma, sem obrigação ainda, mas valor é a direção sinalizada. |
| Processo administrativo sancionador (PAS) com decisão final | Match setorial → material; prática-relacionada → review-worthy; nenhum → FYI ou skip |
| Nota técnica / parecer / guidance | Review-worthy |
| Discurso / blog / declaração de diretor(a) de agência | FYI ou skip per threshold |
| TAC (MP), termo de compromisso (CVM Lei 6.385 art. 11 §5º), acordo de leniência (CADE Lei 12.529 art. 86; Anticorrupção Lei 12.846 art. 16) | Depende — teoria nova ou número grande → review-worthy; rotineiro → skip |

**Tratamento de Tomada de Subsídios / AIR — específico.** Itens pré-norma são distintos de consultas públicas em ponto importante: não mudam o direito, mas têm prazos de manifestação e sinalizam direção do(a) regulador(a). Trate como branch separado:

- **Não** classifique como "sempre material" — impacto de compliance é zero até a norma sair.
- **Sim** classifique como review-worthy se qualquer das áreas tocar categorias "sempre material" da watchlist (ex.: tomada de subsídios sobre Open Finance numa watchlist fintech).
- **Sim** logue o prazo da manifestação em `~/.claude/plugins/config/claude-for-legal/regulatorio/comment-tracker.yaml` com `item_type: Tomada-Subsídios` ou `item_type: AIR` para que o tracker downstream distinga de lacunas de compliance.
- **Sim** inclua na entrada do digest linha explícita: "Pré-norma. Prazo de manifestação [data]. Roteie a `/regulatorio:policy-diff` apenas como pré-posicionamento (sem lacuna de compliance ainda)." Isso prima a skill policy-diff a usar seu branch comprimido de pré-posicionamento.
- **Roteie para o comment-tracker, não para o gap-tracker.** Itens comment-decision não são lacunas de compliance; pertencem ao comment tracker, e `gap-surfacer` usa o `gap_type` `comment-decision` (ou recusa ingestão, se o time roteia separado).

**Tratamento de prazo de consulta pública:**

Para toda consulta pública classificada em tier acima de "skip":
- Extraia prazo (sítio do regulador retorna como data estruturada na maioria das vezes)
- Se comment tracking habilitado em ~/.claude/plugins/config/claude-for-legal/regulatorio/CLAUDE.md: append em `~/.claude/plugins/config/claude-for-legal/regulatorio/comment-tracker.yaml` com status "undecided" e owner padrão de decisão de manifestação do CLAUDE.md
- Inclua prazo na entrada de saída

### Step 3: Enrich

Para cada item acima de tier FYI:

- Sumário de uma linha (o que mudou)
- Por que pode importar aqui (o hook de relevância — "isso é sobre [prática que vocês fazem]")
- Link para fonte
- Data de vigência (após vacatio legis — LINDB art. 1º) ou prazo de manifestação se aplicável

Não sumarize itens FYI individualmente — só conte.

## Output

O digest vai no chat por padrão. **Também escreva em arquivo compartilhável** sempre que a saída contiver um ou mais itens acima de FYI, a menos que o CLAUDE.md explicitamente sete `Digest output → chat only`.

**Comportamento de file output:**

1. Procure `Digest output path` em `~/.claude/plugins/config/claude-for-legal/regulatorio/CLAUDE.md`. Se setado, escreva lá. Default se não setado: `~/regulatorio-digests/reg-digest-YYYY-MM-DD.md`.
2. Crie diretórios pais se necessário.
3. Escreva o digest completo como Markdown (mesmo conteúdo do output de chat, incluindo work-product header, source tags, e o rodapé verify-citations).
4. Se já existir arquivo no path para hoje, acrescente nova seção com subheader timestamped em vez de sobrescrever — o mesmo dia pode ver múltiplas runs.
5. Após escrever, diga: "Digest escrito em `<path>`. Compartilhe como está, ou converta para .docx com Pandoc: `pandoc <path> -o <path>.docx`."
6. Se write falhar (permissão, diretório faltando que o usuário não autorizou criar, disco), faça fallback para chat-only e diga — não dropr silenciosamente.

Formato em disco match o formato de chat exatamente (abaixo).

```markdown
[WORK-PRODUCT HEADER — por config do plugin ## Outputs — varia por role; veja `## Who's using this`]

## Regulatory Feed Check — [data]

**Período:** [último check] a [agora]
**Feeds checados:** [liste tiers ativos — ex.: "DOU API, ANPD site, CVM site, TR"]
**Itens encontrados:** [N] total

### Bottom line

[N lacunas exigem ação até [data] — top 3: X, Y, Z]

### 🔴 Sempre material

**[Regulador] — [Título]**
[Sumário de uma linha]. [Hook de relevância]. Vigência [data].
[Link]
→ Recomendado: rode policy-diff contra [provável política afetada]

[repetir para cada]

### 🟡 Review-worthy

**[Regulador] — [Título]**
[Uma linha]. [Relevância]. [Prazo se houver].
[Link]

[Consultas públicas: incluir "💬 Prazo de manifestação: [data] — decisão pendente" se comment tracking habilitado]

[repetir]

### 📝 FYI

[N] itens — [lista expansível de títulos + links, sem sumários]

---

**Último check atualizado para:** [timestamp]
**Comment tracker:** [N] consultas públicas com decisões abertas — rode /regulatorio:comments para revisar

---

**Verifique citações antes de confiar nelas.** Citações regulatórias aqui foram geradas por IA e não foram checadas contra fonte primária. Antes de agir sobre qualquer norma, guidance, ou PAS acima, confirme contra o DOU (in.gov.br), planalto.gov.br, sítio oficial da agência (ANPD, CVM, BCB, ANVISA, ANATEL, ANS, ANEEL, ANP, ANTT, ANAC, IBAMA, CADE, SENACON), STF, STJ, ou plataforma de pesquisa do escritório — cheque precisão, data de vigência, e status atual. Citações regulatórias geradas por IA podem ser fabricadas, mal citadas, ou desatualizadas. Source tags em cada item (ex.: `[DOU]`, `[ANPD]`, `[CVM]`, `[web search — verify]`) mostram a origem; tags `verify` carregam risco mais alto de fabricação e devem ser checadas primeiro.
```

## Config-dependent fallbacks

Esta skill lê watchlist, limiar de materialidade, e config de feeds de `~/.claude/plugins/config/claude-for-legal/regulatorio/CLAUDE.md`. Quando valor obrigatório ainda for `[PLACEHOLDER]` ou vazio, diga na saída — específica, não genericamente:

- **Watchlist vazia:** pare e diga "A watchlist na sua configuração está vazia. Não posso puxar feeds sem saber quais reguladores monitorar. Rode `/regulatorio:cold-start-interview --redo` ou edite `~/.claude/plugins/config/claude-for-legal/regulatorio/CLAUDE.md` e acrescente ao menos um regulador."
- **Limiar de materialidade vazio:** faça fallback para tiers padrão e acrescente: "Esta saída usou os tiers padrão porque sua configuração não tem thresholds customizados. Ajuste com `/regulatorio:cold-start-interview --redo` ou editando `~/.claude/plugins/config/claude-for-legal/regulatorio/CLAUDE.md`."
- **Config de feed vazia:** rode apenas API do DOU e acrescente: "Esta saída usou apenas a API gratuita do DOU porque sua configuração não lista RSS direto nem feeds pagos. Acrescente feeds com `/regulatorio:cold-start-interview --redo` ou editando `~/.claude/plugins/config/claude-for-legal/regulatorio/CLAUDE.md`."

Nada a dizer sobre config quando valores relevantes populados.

Se nada acima de FYI: "Tudo quieto. [N] itens FYI, nada exigindo atenção."

## Handoff

- **Para policy-diff:** Qualquer item "sempre material" com provável impacto em política → ofereça rodar o diff.
- **Para gap-surfacer:** Se diff achar lacuna → tracked.
- **Para comment-tracker:** Qualquer consulta pública classificada acima de "skip" → prazo de manifestação logado automaticamente se tracking habilitado.

## Close with the next-steps decision tree

Encerre com o decision tree de próximos passos per CLAUDE.md `## Outputs`. Customize as opções ao que esta skill acabou de produzir.

## What this skill does not do

- Lê cada item na íntegra. Classifica e enriquece; leitura profunda é para itens que sobreviverem ao filtro.
- Muda o limiar de materialidade. Se o filtro está errado, edite ~/.claude/plugins/config/claude-for-legal/regulatorio/CLAUDE.md.
- Exige TR. Feeds gratuitos (DOU, sítios das agências) são baseline; feeds pagos acrescentam profundidade.
