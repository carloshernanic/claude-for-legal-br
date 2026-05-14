---
name: reg-gap-analysis
description: >
  Confronta uma norma nova ou alterada (LGPD, Resolução CD/ANPD, normativo
  setorial — BCB, ANS, CFM — ou jurisprudência STJ/STF) com a política de
  privacidade e a prática atuais — produz lista de gaps e plano de remediação
  com responsáveis e prazos. Use quando uma norma nova for publicada, o(a)
  usuário(a) perguntar "[norma] me atinge", "análise de gap contra [Resolução
  CD/ANPD X]", "compliance contra [norma]", ou colar texto regulatório.
argument-hint: "[nome da norma, ou cole texto/resumo]"
---

# /reg-gap-analysis

1. Carregue `~/.claude/plugins/config/claude-for-legal/privacidade/CLAUDE.md` → compromissos da política de privacidade, footprint regulatório, sistemas que processam dados de titulares.
2. Rode o workflow abaixo.
3. Escopo: a norma se aplica? (jurisdição — federal LGPD ou regime setorial, thresholds, setor)
4. Extraia requisitos → confronte com estado atual → lista de gaps.
5. Plano de remediação com responsáveis, prazos, priorização.
6. Salve documento datado. Mesmo "sem gaps" recebe documento.

```
/privacidade:reg-gap-analysis "Resolução CD/ANPD nº 15/2024 — comunicação de incidente"
```

```
/privacidade:reg-gap-analysis
[cole texto/orientação]
```

---

# Análise de Gap Norma-vs-Política

## Finalidade

A ANPD publica nova Resolução do CD. O STJ firma tese sobre LGPD. O BCB altera Resolução sobre segurança cibernética. Algo se move — e agora você precisa saber o quê, se algo, tem que mudar.

Esta skill confronta o novo requisito com o que você faz hoje (por `~/.claude/plugins/config/claude-for-legal/privacidade/CLAUDE.md` → Compromissos da política de privacidade + práticas documentadas em RIPDs) e produz lista de gaps com plano de remediação.

## Carregar estado atual

Leia `~/.claude/plugins/config/claude-for-legal/privacidade/CLAUDE.md`:
- `## Compromissos da política de privacidade` — o que você prometeu publicamente
- `## Footprint regulatório` — o que já se aplica
- `## Processo de requisição de titular` → lista de sistemas — o que você de fato faz operacionalmente

Se a norma não se aplica a você (jurisdição errada, abaixo do threshold, setor diferente), a análise de gap é uma linha: "Não se aplica. Motivo: [razão]. Sem ação necessária."

## Workflow

### Passo 1: Escopar a norma

Antes de confrontar, responda:

- **Aplica-se?** Jurisdição (LGPD é federal, mas regimes setoriais — BCB/CMN, ANS, CFM — sobrepõem; há tratamento no BR ou de titulares no BR?), threshold (LGPD não tem threshold de aplicação como CCPA — aplica-se a qualquer agente, mas Resolução CD/ANPD 2/2022 trata agentes de pequeno porte), setor (financeiro? saúde? educação? telecom?)
- **Quando?** Data de publicação no DOU, data de vigência, eventual vacatio legis (Resoluções CD/ANPD costumam ter vigência imediata ou em 30/60/90 dias)
- **O que de fato é novo?** Muitas Resoluções da ANPD detalham regra geral já existente na LGPD. Identifique o delta efetivo, não o texto integral.

### Passo 2: Extrair requisitos

Leia a norma (ou resumo/orientação). Liste cada requisito substantivo como item discreto:

| # | Requisito | Citação | Categoria |
|---|---|---|---|
| 1 | [requisito como redigido] | [art./§/inciso] | [Aviso / Direitos / Segurança / Operador / Outro] |

**Categorias:**
- **Aviso** — o que você tem que dizer aos titulares (conteúdo da política de privacidade; LGPD art. 9º)
- **Direitos** — o que titulares podem requerer (LGPD art. 18; adjacente a `/dsar-response`)
- **Segurança** — medidas técnicas e administrativas (LGPD arts. 46-49)
- **Operador** — o que tem que fluir para operadores via contrato de tratamento (LGPD arts. 39-40)
- **Consentimento** — mecânica de obtenção, revogação (LGPD arts. 7º I e 8º)
- **Governança** — Encarregado(a), RIPD, registro das operações de tratamento (LGPD arts. 37-41)

### Passo 3: Confrontar com estado atual

Para cada requisito:

```markdown
### [Requisito #N]: [nome curto]

**Norma diz:** [requisito, citado ou parafraseado]

**Hoje fazemos:** [o que o CLAUDE.md / política de privacidade / prática mostra]

**Gap:** [Nenhum | Parcial | Total]

**Se gap parcial/total — o que falta:** [específico]

**Esforço para fechar:** [Atualização de política apenas | Mudança de produto | Renegociação com operador/fornecedor | Novo processo]

**Risco do não-cumprimento:** [LGPD art. 52: advertência → multa simples até 2% do faturamento limitado a R$ 50 milhões → multa diária → publicização → bloqueio/eliminação dos dados → suspensão parcial/total da atividade. Risco probabilístico, ação fiscalizatória vigente, exposição reputacional, risco de ação civil pública (Lei 7.347/85) ou ação coletiva (CDC arts. 81-104).]
```

### Passo 4: Priorizar

Nem todo gap é igual. Ordene por:

1. **Prazo de cumprimento com mordida** — data de vigência + fiscalização ativa + sanções concretas
2. **Razão esforço-impacto** — atualização de linguagem de política é barata; rebuild de produto não é
3. **O que você já fez parcialmente** — se já está 80% conforme outra norma, o delta pode ser pequeno

### Passo 5: Plano de remediação

Prepende o cabeçalho de material de trabalho do `~/.claude/plugins/config/claude-for-legal/privacidade/CLAUDE.md` `## Saídas` (varia por papel do(a) usuário(a) — veja `## Quem está usando`).

> **Pré-voo do conector de pesquisa.** Antes de emitir o plano de remediação, cheque se há conector de pesquisa jurídica acessível nesta sessão — JusBrasil, MCP de site da ANPD, MCP do Planalto, MCP do STJ/STF, ou MCP de pesquisa configurado pela banca. Registre na nota de revisão, conforme `CLAUDE.md` `## Saídas`: se nenhum conector retornar resultados no Passo 2 ou na pesquisa de categorias regulatórias (ou nenhum estiver configurado em tempo de execução), registre na linha **Fontes:** da nota de revisão — ex.: `não conectado — citações vêm de conhecimento de treinamento; os itens com maior risco de fabricação em análises de gap são datas de vigência de novas Resoluções CD/ANPD, datas de início de fiscalização e pinpoints de artigo/parágrafo — confira esses primeiro`. As tags por citação `[conhecimento do modelo — verificar]` permanecem inline. Não emita banner separado acima da saída.

```markdown
[CABEÇALHO DE MATERIAL DE TRABALHO — conforme config do plugin ## Saídas]

## Plano de Remediação: [Nome da norma]

**Data de vigência:** [data]
**Início da fiscalização:** [data — quando aplicável; verificar postura da ANPD]

### A fazer antes da fiscalização

| Gap | Correção | Responsável | Prazo | Status |
|---|---|---|---|---|
| [gap] | [correção específica] | [nome] | [data] | [ ] |

### Recomendável (risco menor, não bloqueante)

[mesma tabela]

### Já em conformidade

[lista de requisitos onde gap = Nenhum — útil para a mensagem "estamos majoritariamente ok"]

### Gaps aceitos (risco aceito, não corrigindo)

[se houver — com racional documentado e quem aceitou o risco]
```

## Categorias comuns de norma

Ao escopar o delta, ajuda colocar a norma em categoria aproximada e então pesquisar especificidades:

- **Lei geral de proteção de dados** — cobertura ampla das práticas de dados pessoais em uma jurisdição (no Brasil: LGPD Lei 13.709/2018; resoluções infralegais da ANPD)
- **Overlay setorial** — saúde (ANS, Resolução CFM 1.821/2007), financeiro (Resolução BCB 4.658/2018, Open Finance), crianças/adolescentes (ECA + LGPD art. 14), educação, trabalho (dados de candidatos e empregados — CLT + LGPD)
- **Regime específico de IA** — transparência, RIPDs, governança de decisão automatizada (LGPD art. 20; PL 2.338/2023 em tramitação `[verificar]`)
- **Cadastro positivo / dados de crédito** — Lei Complementar 166/2019, Resoluções BCB
- **Regime de comunicação de incidente** — autônomo ou embarcado em lei mais ampla (LGPD art. 48 + Resolução CD/ANPD 15/2024)
- **Regime de transferência internacional** — adequação, cláusulas-padrão, mecanismo (LGPD arts. 33-36 + Resolução CD/ANPD 19/2024)

Para cada categoria relevante para a norma nova, **pesquise os requisitos vigentes** antes de redigir a análise. Cite fontes primárias — preferencialmente: [Planalto] para texto da lei, [ANPD] para Resoluções e guias, [STJ]/[STF] para jurisprudência, [DOU] para publicação oficial. Confira atualidade — a ANPD publica e revoga Resoluções regularmente; o STJ profere repetitivos sobre LGPD anualmente. Sinalize incerteza para verificação por advogado(a) em vez de afirmar regra não confirmada.

> **Sem complementação silenciosa.** Se a consulta a ferramenta de pesquisa jurídica configurada (JusBrasil, site da ANPD/Planalto, plataforma da banca) retornar poucos ou nenhum resultado para uma norma, ato administrativo orientativo ou processo administrativo sancionatório (PAS), reporte o que encontrou e pare. NÃO complete o gap a partir de busca web ou conhecimento do modelo sem perguntar. Diga: "A busca retornou [N] resultados em [ferramenta]. Cobertura parece rasa para [regime / tema]. Opções: (1) ampliar a consulta, (2) tentar outra ferramenta, (3) busca web — os resultados serão tagueados `[busca web — verificar]` e devem ser conferidos na fonte oficial antes de confiar, ou (4) sinalizar como não verificado e parar. Qual prefere?" Quem decide se aceita fontes de menor confiança é o(a) advogado(a).
>
> **Camadas de atribuição de fonte.** Marque toda citação na análise de gap com sua fonte. Para citações de conhecimento do modelo, use uma das três camadas em vez de uma tag "verificar" única:
>
> - `[estável — última confirmação YYYY-MM-DD]` — referências legais e regulatórias estáveis improváveis de terem mudado (ex.: LGPD art. 7º, LGPD art. 18, LGPD art. 48, CF art. 5º LXXIX). Ainda assim, confira antes de protocolar, mas é prioridade menor. A data importa — Resolução CD/ANPD 15/2024 mudou o panorama da comunicação de incidente; antes dela o "estável" era outro.
> - `[verificar]` — citações de conhecimento do modelo que são reais mas devem ser verificadas: Resoluções específicas, guias orientativos, decisões do STJ/STF, datas de vigência, thresholds, decisões da ANPD.
> - `[verificar-pinpoint]` — citações de pinpoint (incisos, parágrafos, alíneas específicas; números de processo, ementa) carregam o maior risco de fabricação e devem SEMPRE ser verificadas contra fonte primária.
>
> Citações recuperadas de ferramenta mantêm sua tag de fonte (`[ANPD]`, `[Planalto]`, `[STJ]`, `[STF]`, `[JusBrasil]`, `[DOU]`, ou o nome da ferramenta MCP); citações de busca web ficam `[busca web — verificar]`; citações fornecidas pelo(a) usuário(a) ficam `[fornecido pelo usuário]`. As camadas trazem à tona o trabalho real de verificação — quem verifica tudo não verifica nada. Nunca remova nem colapse as tags.

## Integração com outras skills

**De pia-generation (RIPD):** RIPDs sinalizam inconsistências com a política de privacidade → alimentam aqui como gaps conhecidos.

**Para o plugin regulatorio (se instalado):** Esta skill é a versão manual. O plugin de monitor observa feeds e dispara esta análise automaticamente quando algo muda.

## Saída

Salve como documento markdown datado. A tabela de plano de remediação vira tracker — atualize status conforme itens fecham.

Se a análise concluir "sem gaps, estamos em conformidade", ainda assim escreva o documento — é prova útil depois de que vocês olharam.

**Encerre com nota de verificação de citação:**

> As citações nesta saída foram geradas por modelo de IA e não foram verificadas contra fonte primária. Antes de confiar em qualquer Resolução, lei, orientação ou decisão administrativa, confira contra ferramenta de pesquisa jurídica (JusBrasil, site da ANPD, Planalto, STJ, STF, ou plataforma da sua banca) para precisão e status atual. Citações geradas por IA podem ser fabricadas ou mal citadas. As tags de fonte em cada citação (ex.: `[busca web — verificar]`) mostram de onde veio; tags `verificar` carregam maior risco de fabricação e devem ser conferidas primeiro.

## Encerre com a árvore de decisão de próximos passos

Encerre com a árvore de decisão de próximos passos conforme `CLAUDE.md` `## Saídas`. Customize as opções ao que esta skill produziu — as cinco ramificações padrão (minutar X, escalar, buscar mais fatos, aguardar, outra coisa) são ponto de partida, não trava. A árvore é a saída; o(a) advogado(a) escolhe.

## O que esta skill NÃO faz

- Não interpreta linguagem regulatória ambígua de forma autoritativa. Quando a norma é nebulosa, diga: "O art. X pode ser lido como [A] ou [B]. [A] é a leitura conservadora. Sugiro consulta a especialista se isto for material."
- Não rastreia mudanças regulatórias proativamente. Roda quando você aponta para uma mudança. Para monitoramento proativo, veja o plugin regulatorio.
- Não implementa correções. Planeja-as.

---

## Reconhecimento de jurisdição

Esta skill foi adaptada para LGPD e regulação infralegal brasileira (Resoluções CD/ANPD, normativos setoriais BCB/ANS/CFM). **Não há equivalente direto** no Brasil para:

- **"State privacy laws"** (CCPA, CPRA, VCDPA, etc.) — a LGPD é federal e única; estados não legislam sobre proteção de dados como matéria autônoma (competência federal — CF art. 22).
- **"Right to opt out of sale"** — a LGPD não trata "venda" de dados como categoria autônoma; o que há é direito de revogação de consentimento (art. 18 IX), oposição (não nominado, mas decorre de mudança de base legal), e portabilidade. Sinalize quando o tema americano não tiver tradução direta.
- **HIPAA / COPPA / FERPA / GLBA** — não existem como leis federais. Para saúde: CFM Res. 1.821/2007 + ANS + LGPD art. 11; para crianças: ECA + LGPD art. 14; para escolas: LDB + LGPD; para financeiro: BCB + LGPD + Lei Complementar 105/2001.

Se a pergunta envolver elemento norte-americano (controlador nos EUA, titulares também nos EUA, transferência internacional para EUA), sinalize e ofereça analisar a porção brasileira sob LGPD e separar a porção americana para análise específica.
