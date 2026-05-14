---
name: launch-review
description: >
  Launch review completa contra seu framework e calibração de risco. Use quando
  o usuário disser "revise este lançamento", "revisão jurídica de [feature]",
  "podemos lançar isto", "quais os issues jurídicos de [produto]" ou referenciar
  ticket de launch tracker / PRD que precisa de memo de revisão categoria-por-
  categoria.
argument-hint: "[arquivo PRD | link Drive | ID de ticket no tracker]"
---

# /launch-review

> **Adaptação BR.** Calibrado para LGPD (Lei 13.709/2018), Marco Civil da Internet (Lei 12.965/2014), CDC (Lei 8.078/1990), ECA (Lei 8.069/90), LBI (Lei 13.146/2015), LPI (Lei 9.279/96), Lei do Software (Lei 9.609/98), CONAR, e regulação setorial (BCB, CVM — Lei 14.478/2022 e Res. CVM 175 para cripto; ANVISA; ANATEL — Res. 632/2014; SUSEP). Atenção ao RE 1.037.396 STF (Marco Civil art. 19) — pode mudar. Veja `references/terminology-us-to-br.md`.

1. Carregue `~/.claude/plugins/config/claude-for-legal/produto/CLAUDE.md` → framework + calibração. Pare se houver placeholders.
2. Pegue PRD + docs relacionados. Se tracker conectado, puxe ticket e comentários.
3. Caminhe por toda categoria do framework usando o workflow abaixo.
4. Calibre cada finding contra a tabela. Inédito = sinalize explicitamente.
5. Output: memo de revisão no formato da casa. Poste resumo no ticket se conectado.
6. Handoff: marketing-claims-review se houver componente substancial de marketing; feature-risk-assessment se uma finding precisa de profundidade.

```
/produto:launch-review PROJ-1234
```

---

## Matter context

**Matter context.** Cheque `## Matter workspaces` no CLAUDE.md de practice-level. Se `Enabled` é `✗` (default para in-house), pule o resto deste parágrafo — skills usam contexto practice-level e o esquema de matter fica invisível. Se habilitado e não há matter ativa, pergunte: "Para qual matéria é? Rode `/produto:matter-workspace switch <slug>` ou diga `practice-level`." Carregue o `matter.md` da matter ativa para contexto e overrides. Escreva outputs na pasta da matter em `~/.claude/plugins/config/claude-for-legal/produto/matters/<matter-slug>/`. Nunca leia arquivos de outra matter salvo se `Cross-matter context` está `on`.

---

## Checagem de destinatário

Antes de produzir output, cheque para onde vai. Se o usuário nomeou destino (canal, lista de distribuição, contraparte, "todo mundo"), pergunte se está dentro do círculo do sigilo (EOAB art. 7º, II e XIX). Canais públicos, listas company-wide, contraparte/advogado(a) da outra parte, fornecedores e clientes (para material de trabalho) quebram a proteção. Quando o destino parece fora do círculo, sinalize e ofereça (a) versão privilegiada só para jurídico, (b) versão sanitizada para canal mais amplo, ou (c) ambas — não aplique silenciosamente cabeçalho privilegiado e depois ajude a colar em lugar onde o cabeçalho não protege. Veja `## Guardrails compartilhadas → Checagem de destinatário` no CLAUDE.md deste plugin.

## Propósito

Leia o PRD, cheque toda categoria do framework deste time, calibre contra o que efetivamente bloqueia aqui (conforme `~/.claude/plugins/config/claude-for-legal/produto/CLAUDE.md`), e produza revisão no formato da casa. Meta: um(a) PM lê e sabe exatamente o que tem de acontecer antes de lançar.

## Carregue calibração

Leia `~/.claude/plugins/config/claude-for-legal/produto/CLAUDE.md`:
- `## Framework de revisão` — as categorias a checar
- `## Calibração de risco` — o que bloqueia vs. o que é FYI *nesta empresa*
- `## Processo de launch review` — formato de output
- `## Escalação` — quando rotear para cima

A tabela de calibração é a diferença entre esta skill e um checklist genérico. Se a tabela diz "nova coleta de dado → RIPD, vai ao ar em 1-2 dias", não escreva "isto pode exigir RIPD completo (LGPD art. 38) e consulta ANPD". Bata com a prática real do time.

## Workflow

### Passo 1: Pegue os inputs

- **PRD** — de arquivo, Drive, ou do ticket no launch tracker
- **Spec/design doc** — se separado
- **Plano de marketing** — se existe (handoff para marketing-claims-review se substancial)
- **Data de lançamento** — para calibrar urgência
- **Ticket no launch tracker** — se conectado, puxe para contexto e comentários

Se Jira/Linear MCP está conectado, puxe o histórico do ticket — frequentemente há contexto em comentários anteriores que o PRD não captura.

### Passo 2: Entenda o que está lançando

Antes do checklist, responda em português claro:

- O que essa coisa faz?
- Quem usa — usuários existentes, novos usuários, novo segmento?
- O que é novo vs. extensão de algo já revisado?
- Algum dado novo, fornecedor novo, claim novo, jurisdição nova?

**Detecção de IA — rode antes do walk do framework.** Cheque se este lançamento usa IA em qualquer forma: modelo de terceiro, modelo construído internamente, feature de fornecedor com IA, score ou classificação automatizada, conteúdo generativo, recomendação, predição. Procure mesmo se o PRD não rotular como "IA" — palavras como "inteligente", "automatizado", "personalizado", "gerado", "sugerido" são indícios.

Se componente de IA detectado → sinalize, então rode `/governanca-ia:use-case-triage [feature]` em paralelo ao walk do framework. A Categoria 12 abaixo cuida do detalhe (LGPD art. 20 — decisão automatizada; PL 2338/2023 em tramitação); esta flag garante que nunca é pulada mesmo se o PRD é vago.

### Passo 3: Caminhe pelo framework

Para cada categoria em `~/.claude/plugins/config/claude-for-legal/produto/CLAUDE.md` → Framework de revisão. Se o time não tem um, use o default de 12 categorias abaixo. As categorias são conceitos estáveis de enquadramento; dentro de cada, pesquise os regimes regulatórios aplicáveis ao setor, público e jurisdição do produto antes de calibrar severidade. O que bloqueia numa jurisdição ou setor pode ser rotineiro em outro — `~/.claude/plugins/config/claude-for-legal/produto/CLAUDE.md` captura a calibração do time.

| # | Categoria | Pergunta-chave | Auto-skip se |
|---|---|---|---|
| 1 | **Compromissos contratuais** | Conflita com promessa cliente-facing (Termos de Uso, SLA, marketing)? Mudança unilateral em ToS exige observar CC art. 122 e CDC art. 51 X-XI (cláusula que permite variação unilateral é abusiva em consumo). | Sem mudanças cliente-facing |
| 2 | **Privacidade (LGPD)** | Nova coleta? Nova finalidade? Novo compartilhamento? Base legal (LGPD art. 7º ou art. 11 para sensíveis) identificada? Direitos do titular (art. 18) preservados? Cookies seguem Guia ANPD 2023? RIPD (art. 38) necessário? Transferência internacional com cláusulas-padrão ANPD (Res. CD/ANPD 19/2024)? | Sem mudanças em dados |
| 3 | **Segurança** | Nova superfície de ataque, novo dado em repouso, novos padrões de acesso? Incidente teria de ser comunicado à ANPD (LGPD art. 48; Res. CD/ANPD 15/2024)? | UI-only, sem mudança de backend |
| 4 | **PI** | Código/conteúdo de terceiro? Check de licença open-source (LPI Lei 9.279/96; software Lei 9.609/98; autorais Lei 9.610/98)? Outputs que podem infringir marca registrada no INPI? | Sem novas dependências, sem UGC |
| 5 | **Terceiros** | Novo fornecedor, parceiro ou integração? Contrato de tratamento de dados controlador-operador (LGPD arts. 39 e 42)? Cláusulas de auditoria? | Sem novas partes externas |
| 6 | **Regulatório setorial** | Toca setor, público ou jurisdição regulada? Pesquise os regimes aplicáveis. | Mesmos usuários, mesmos setores, mesmas jurisdições do produto existente |

> **Sem complementação silenciosa.** Se query à ferramenta de pesquisa configurada (JusBrasil, Escavador, LexML, Planalto, sites de regulador — ANPD, BCB, CVM, ANVISA, ANATEL, CADE, SENACON — ou plataforma do escritório) retorna poucos ou nenhum resultado para um regime, precedente regulatório (TAC, processo administrativo) ou guia, reporte o que foi encontrado e pare. NÃO preencha lacuna com busca web ou conhecimento de modelo sem perguntar. Diga: "A busca retornou [N] resultados de [ferramenta]. Cobertura parece fina para [regime / tópico]. Opções: (1) ampliar a query, (2) tentar outra ferramenta, (3) buscar web — resultados marcados `[busca web — verificar]` e devem ser checados contra autoridade emissora antes de confiar, ou (4) sinalizar como não verificado e parar. Qual você prefere?" Um(a) advogado(a) decide.
>
> **Tiering de atribuição de fonte.** Marque toda citação na revisão com sua fonte. Para citações de conhecimento de modelo, use um de três níveis em vez de tag "verificar" genérica:
>
> - `[estável]` — referências estatutárias e regulatórias estáveis e conhecidas, improváveis de ter mudado (ex.: CDC art. 6º, LGPD art. 7º, CF art. 5º LXIX). Verifique antes de confiar para liberar um lançamento, mas prioridade menor.
> - `[verificar]` — citações de conhecimento de modelo que são reais mas devem ser verificadas: resoluções específicas da ANPD/BCB/CVM, instruções normativas, autos administrativos, súmulas, Temas, thresholds, datas de vigência, alterações pós-2023.
> - `[verificar-pinpoint]` — pinpoints (artigos, incisos, alíneas, parágrafos específicos; números de processo) carregam o maior risco de fabricação e devem SEMPRE ser verificados contra fonte primária.
>
> Citações recuperadas de ferramenta mantêm sua tag de fonte (`[JusBrasil]`, `[Escavador]`, `[LexML]`, `[Planalto]`, `[STF]`, `[STJ]`, `[TST]`, `[site de regulador]`, ou nome da MCP); citações de busca web ficam `[busca web — verificar]`; citações fornecidas pelo usuário (do PRD ou material seed) ficam `[usuário forneceu]`. O tiering trazque à tona o trabalho de verificação real — leitor que verifica tudo verifica nada. Nunca remova ou compacte as tags.
>
> `[política de plataforma — verificar contra docs ao vivo]` — regras de plataforma (Apple App Store Review Guidelines, Google Play Policies, Meta/TikTok/Kwai, Classificação Indicativa da SAJ/Ministério da Justiça — Portaria MJ 1.220/2007, bandeiras de cartão, PIX/Open Finance — Res. Conjunta BCB/CMN 1/2020) citadas sem buscar a página viva. Nunca use `[estável]` para política de plataforma — mudam sem aviso e o snapshot do modelo quase sempre é stale. Se o lançamento depende de regra de plataforma, busque a página atual em sessão antes de confiar.
| 7 | **Marketing e claims** | Algum claim precisa de substanciação (CDC art. 36 §único — ônus de quem patrocina)? Comparativo (CONAR — admitido com lealdade e dado verificável)? Influenciador (CONAR Resolução + CDC art. 36)? | Sem componente de marketing |
| 8 | **Consumidor (CDC)** | Direito de arrependimento (CDC art. 49) em e-commerce + Dec. 7.962/2013? Cláusulas potencialmente abusivas (CDC art. 51, especialmente IV, X-XI, XV)? Informação clara e adequada (CDC arts. 6º, 30, 31)? Marketplace — Súmula 633 STJ (solidariedade)? Aplicação de CDC em B2B só se hipossuficiência (Súmulas 297, 381, 643 STJ)? | Sem relação de consumo |
| 9 | **Acessibilidade (LBI)** | Aplicáveis: LBI Lei 13.146/2015 + Decreto 9.296/2018 (regulamento de acessibilidade digital) + eMAG (modelo do governo, com WCAG 2.1 como referência)? | Não é interface ao público |
| 10 | **Crianças e adolescentes** | Público infanto-juvenil ou alcance previsível? ECA Lei 8.069/90 + LGPD art. 14 (dados de crianças exigem consentimento específico de um dos pais; só usar dados de adolescente em interesse legítimo qualificado) + CONANDA Res. 163/2014 (publicidade abusiva dirigida a criança) + Lei 9.294/96 se tabaco/álcool/medicamento? Classificação Indicativa (Portaria MJ 1.220/2007)? | Sem alcance infanto-juvenil |
| 11 | **Conteúdo de usuário (Marco Civil)** | Plataforma armazena/dissemina conteúdo de terceiro? Regra geral: provedor não responde por conteúdo de terceiro sem ordem judicial específica (Marco Civil art. 19). Exceção: nudez não consentida exige remoção extrajudicial após notificação (Marco Civil art. 21). **Atenção:** o art. 19 está sob exame do STF (RE 1.037.396) — entendimento pode mudar `[conhecimento de modelo — verificar]`. Guarda de logs (arts. 13–15): conexão 1 ano, acesso a aplicações 6 meses. | Sem UGC |
| 12 | **Governança de IA** | Usa IA em alguma forma? Caso de uso no registro? AIA feito? Termos de IA do fornecedor revistos? Atenção: LGPD art. 20 (direito à revisão de decisão automatizada que afete interesse do titular); PL 2338/2023 em tramitação (Marco da IA); Res. CD/ANPD sobre IA quando publicada. | Sem componente de IA detectado no Passo 2 |

**Para cada categoria, output:**

```markdown
### [N]. [Categoria]

**Checado:** [o que olhei]
**Finding:** [Limpo | Precisa trabalho | Bloqueante | Pulado]
**Detalhe:** [qual o issue, se algum — específico ao PRD, não genérico]
**Calibração:** [conforme CLAUDE.md de config — geralmente FYI / costuma precisar de X / costuma bloquear]
**Ação:** [o que tem de acontecer, quem é dono, até quando]
```

**Auto-skip honesto.** Se uma categoria não se aplica, diga com uma linha de razão. Não enrole.

**Pistas de setor.** O framework de 12 categorias acima é modelado para SaaS / produto digital. Se o lançamento envolve setores abaixo, adicione overlay: pergunte a questão de overlay ao lado da pergunta do framework base para cada categoria afetada, e traga à tona o regime setorial antes de calibrar severidade. Lançamento que marca as 12 caixas mas pula o regime setorial vai ao ar com buraco.

| Setor | Regimes de overlay a trazer à tona |
|---|---|
| **Crianças / adolescentes** | ECA Lei 8.069/90 (todo); LGPD art. 14; CONANDA Res. 163/2014 (publicidade abusiva); Classificação Indicativa (Portaria MJ 1.220/2007); plataforma (Apple/Google idade); CONAR — Anexo H sobre crianças |
| **Jogos / loot box / moeda em jogo** | Lei das Apostas 14.790/2023 para "bets" (apostas de quota fixa); divisão entre jogo de azar (proibido — Decreto-Lei 9.215/46 e Lei 8.176/91), sorteio (autorização específica), e jogo de habilidade; Classificação Indicativa; CONANDA Res. 163/2014 se público infantil; CDC arts. 6º e 39 (publicidade enganosa de chance); regras de plataforma; menores não podem apostar (Lei 14.790/2023 art. 25) |
| **Fintech** | BCB + Lei 12.865/2013 (arranjos de pagamento) + Open Finance (Res. Conjunta BCB/CMN 1/2020) + PIX (Res. BCB 1/2020 e seguintes); CVM se valor mobiliário; LGPD art. 11 (dados financeiros — sensíveis na maioria dos contextos); Resolução CMN sobre crédito; cadastro positivo (Lei 12.414/2011); SUSEP se seguro |
| **Cripto / tokens** | Lei 14.478/2022 (Marco Cripto) + regulação BCB sobre VASPs; CVM Res. 175 para tokens com características de valor mobiliário; Receita Federal IN 1888/2019 (informações); PLD (Lei 9.613/98 e Lei 12.683/2012) + COAF |
| **Saúde** | ANVISA (RDC sobre dispositivos médicos — RDC 657/2022; software como dispositivo médico — RDC 751/2022); Lei 13.787/2018 (prontuário eletrônico); Resolução CFM 2.314/2022 (telemedicina); LGPD art. 11 (dado de saúde — sensível); Lei 14.510/2022 (telessaúde) |
| **Educação** | LDB Lei 9.394/96; LGPD art. 14 (dados de criança/adolescente); MEC para sistemas formais; ECA |
| **Emprego / HR tech** | CLT + Reforma Trabalhista 13.467/2017; LGPD art. 11 (dados sensíveis em recrutamento); Lei 9.029/1995 (não-discriminação); biometria (LGPD art. 11); Súmula 568 TST (antecedentes — vínculo + função); Lei 14.611/2023 (transparência salarial) |
| **Governo / setor público** | Lei 14.133/2021 (Licitações); LGPD Capítulo IV (tratamento pelo Poder Público); Lei 12.527/2011 (LAI); compliance Lei 12.846/2013 |
| **Consumo / varejo / marketing** | CDC (todo); Dec. 7.962/2013 (e-commerce); CONAR; Lei 14.181/2021 (superendividamento); ANATEL Res. 632/2014 (telemarketing); LGPD para perfilamento (art. 20); auto-renovação — analogia a CDC art. 39 (prática abusiva) e exigências de informação CDC arts. 30 e 31 |

Se uma pista de setor disparar e nenhuma categoria dedicada cobre, insira como categoria (ex.: "6a. Overlay setorial — crianças / ECA + LGPD 14 + CONANDA 163"). Não deixe sumir na Categoria 6 Regulatório como rodapé; o regime setorial frequentemente fornece o piso controlador, não nota de rodapé.

### Passo 4: Calibre severidade

Para cada finding, cheque contra a tabela de calibração em `~/.claude/plugins/config/claude-for-legal/produto/CLAUDE.md`:

- Se bate com padrão "costuma ser FYI" → anote, não bloqueie
- Se bate com "costuma exigir trabalho" → especifique o trabalho, estime timeline da tabela
- Se bate com "costuma bloquear" → sinalize proeminentemente, roteie pela tabela de escalação
- Se é **inédito** (não está na tabela) → diga explicitamente: "Isto não bate com nenhum padrão na calibração — precisa de chamada humana"

### Passo 5: Monte a revisão

Formate conforme `~/.claude/plugins/config/claude-for-legal/produto/CLAUDE.md` → Processo de launch review → formato de output. Anexe o cabeçalho de material de trabalho de `~/.claude/plugins/config/claude-for-legal/produto/CLAUDE.md` `## Outputs` (varia por papel — veja `## Quem está usando isto`). Se não há formato da casa especificado:

```markdown
[CABEÇALHO DE MATERIAL DE TRABALHO — conforme config do plugin ## Outputs]

# Launch Review: [Nome da feature]

**Revisado:** [data] | **Data de lançamento:** [data] | **Revisor(a):** [nome]
**PRD:** [link] | **Ticket:** [link se conectado]

---

## Bottom line

[Um parágrafo: isto pode lançar? O que tem de acontecer antes?]

**Decisão:** [Liberado | Lançar com condições | Bloqueado pendente X | Precisa escalação]

> **Antes de emitir "Liberado" ou "Lançar com condições":** Leia `## Quem está usando isto` em `~/.claude/plugins/config/claude-for-legal/produto/CLAUDE.md`. Se o Papel é Não-advogado:
>
> > Liberar lançamento é ato jurídico — uma vez lançado, a empresa fica comprometida com a postura jurídica documentada. Você revisou isto com advogado(a) inscrito(a) na OAB? Se sim, prossiga. Se não, eis um brief para levar a ele(a):
> >
> > [Gere resumo de 1 página: o lançamento, findings por categoria, perguntas em aberto, risco residual após condições, e as três coisas a perguntar ao(à) advogado(a) antes de lançar.]
> >
> > Para achar advogado(a): a OAB de seu estado (seccional) tem serviço de indicação; o(a) consulente pode ainda procurar a Defensoria Pública (se hipossuficiente) ou um Núcleo de Prática Jurídica (NPJ) de faculdade.
>
> Não prossiga para "Liberado" ou "Lançar com condições" sem um "sim" explícito. "Bloqueado pendente X" e "Precisa escalação" não exigem este gate — são decisões de revisão, não liberações.

---

## Findings por categoria

[Todos os blocos de categoria do Passo 3 — categorias puladas no rodapé]

---

## Itens de ação

| # | Item | Dono | Prazo | Bloqueante? |
|---|---|---|---|---|
| 1 | [específico] | [PM/eng/legal] | [data] | Sim/Não |

---

## Escalações

[Se houver — quem, por quê, redigido conforme skill de escalação]

---

## Notas para a próxima

[Se este lançamento trouxe à tona padrão que deve atualizar a tabela de calibração]

---

## Citation check

Qualquer acórdão, lei, regulamento ou ação de enforcement referenciados nesta revisão foram gerados por modelo de IA e não foram verificados contra fonte primária. Antes de confiar numa citação para decisão de lançamento, verifique-a contra ferramenta de pesquisa jurídica (JusBrasil, Escavador, LexML, Planalto, base do STF/STJ/TST, ou a plataforma do escritório) para precisão, vigência ("lei ainda em vigor?" — ANPD edita Resoluções com frequência; CVM tem RCVMs novas; CONAR atualiza códigos), e postura atual de enforcement. Citações fabricadas ou mal-citadas em launch reviews podem mandar o negócio para o lado errado. Tags de fonte em cada citação (ex.: `[JusBrasil]`, `[busca web — verificar]`) mostram de onde veio; tags `verificar` carregam maior risco de fabricação e devem ser checadas primeiro.
```

### Passo 6: Produza AMBOS os outputs — o memo sigiloso E o comentário redigido para o ticket

⚠️ **Aviso de sigilo:** Postar o memo sigiloso completo num ticket Jira/Linear amplamente compartilhado com engenharia, PM e outros papéis não jurídicos pode quebrar o sigilo profissional (EOAB art. 7º, II e XIX). Não cole o memo completo em ticket de ampla distribuição.

**Ambos os seguintes são outputs OBRIGATÓRIOS desta skill.** Nenhum é opcional. Imprima-os na ordem abaixo, com divisor claro entre eles para que o usuário não perca o bloco redigido.

**Output 1 — Memo de launch review sigiloso.** A análise completa montada no Passo 5: cabeçalho de material de trabalho, bottom line, findings por categoria com racional de risco, itens de ação, escalações, notas para a próxima, citation check. É material de trabalho jurídico interno. Mantenha no arquivo da matter (Drive, DMS, ou onde `~/.claude/plugins/config/claude-for-legal/produto/CLAUDE.md` diz). Distribua só a pessoas dentro do círculo do sigilo.

**Output 2 — Bloco redigido para o ticket — SEGURO PARA POSTAR NO TRACKER.** Após o memo, com divisor `---` claro e título `## SEGURO PARA POSTAR NO TRACKER (não-sigiloso)`, produza um bloco curto contendo APENAS:

- **Status do lançamento:** verde / amarelo / vermelho (i.e., Liberado / Lançar com condições / Bloqueado pendente X / Precisa escalação)
- **Condições como itens de ação:** cada condição é um bullet, escrito como instrução para PM/eng ("anexar RIPD no ticket antes de lançar", "remover claim 'mais preciso do mercado' da copy da home"). Sem racional jurídico.
- **Prazo por condição.**
- **Dono por condição.**

O bloco redigido contém ZERO cabeçalho de material de trabalho / sigilo, ZERO racional de risco, ZERO discussão jurídica interna, ZERO citações regulatórias, ZERO notas de escalação. Se a redação de uma condição vazaria a teoria jurídica subjacente ("risco de retaliação trabalhista", "exposição à ANPD"), reescreva como ação ("rotear para GC antes da data X").

Exemplo de divisor e bloco:

```markdown
---

## SEGURO PARA POSTAR NO TRACKER (não-sigiloso)

**Status do lançamento:** Bloqueado pendente condições abaixo.

**Condições:**
- [ ] Anexar RIPD concluído ao ticket — Dono: [PM] — Prazo: [data]
- [ ] Remover copy "mais preciso do mercado" do draft da home — Dono: [Marketing] — Prazo: [data]
- [ ] Confirmar com GC antes de mudar janela de retenção — Dono: [PM] — Prazo: [data]
```

Cole o Output 2 (e SÓ o Output 2) no tracker. Linkar o Output 1 só para pessoas dentro do círculo de sigilo que precisam ler a análise completa.

## Handoffs

- **Para marketing-claims-review:** Se há componente substancial de marketing, faça handoff da seção de claims.
- **Para feature-risk-assessment:** Se uma finding é complexa o bastante para merecer doc próprio (ex.: feature de IA inédita, produto infanto-juvenil), dispare assessment mais profunda.
- **Para privacy:** Se o lançamento toca dado pessoal, rode `/privacidade:use-case-triage [feature]`. Se a triagem retorna RIPD OBRIGATÓRIO, rode `/privacidade:ripd-generation [feature]`. Não só anote "RIPD necessário" — dispare.
- **Para AI governance:** Se um componente de IA foi detectado no Passo 2, rode `/governanca-ia:use-case-triage [feature]`. Se a triagem retorna CONDICIONAL, rode `/governanca-ia:aia-generation [feature]`. Se novo fornecedor de IA está envolvido, rode `/governanca-ia:vendor-ai-review [contrato]`.

## Encerre com a árvore de decisão de próximos passos

Encerre com a árvore de decisão conforme CLAUDE.md `## Outputs`. Customize as opções para o que esta skill acabou de produzir — os cinco branches default (redigir o X, escalar, buscar mais fatos, observar e esperar, outra coisa) são ponto de partida, não trava. A árvore É o output; o(a) advogado(a) escolhe.

## O que esta skill NÃO faz

- Não substitui conversa com o(a) PM. Frequentemente o PRD está errado ou desatualizado — a revisão traz à tona perguntas, um humano faz.
- Não aprova o lançamento. Informa a aprovação.
- Não recalibra retroativamente. Se este lançamento sair bem (ou mal) de forma que deveria atualizar a tabela de calibração, um humano atualiza `~/.claude/plugins/config/claude-for-legal/produto/CLAUDE.md`.
