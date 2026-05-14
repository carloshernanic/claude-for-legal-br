---
name: integration-management
description: >
  Tracker pós-fechamento M&A — workplan faseado, tracking de anuências, cessão
  de contratos em escala, relatórios semanais de status. Inicializa do que estiver
  disponível (contrato de compra e venda, sumário do deal, checklist de
  fechamento) e conecta a deal-context.md e closing-checklist.yaml do cold-start
  M&A. Use quando o(a) usuário(a) disser "integração", "pós-fechamento",
  "anuências pendentes", "cessão de contratos", "status de integração", ou
  "o que falta do deal".
argument-hint: "[--init | --contracts | --report | --update | --export [--format csv|table] [--section all|consents|contracts|workplan]] [--deal [code]]"
---

# /integration-management

> **Adaptação BR não oficial.** Calibrado para integração pós-fechamento de M&A no Brasil — Lei 6.404/76, CC, Lei Anticorrupção (12.846/2013), LGPD (13.709/2018), CADE, Lei 14.596/2023 (preços de transferência), INPI (PI), Juntas Comerciais. Veja `references/terminology-us-to-br.md`. Toda saída é rascunho para revisão por advogado(a) inscrito(a) na OAB.

1. Carregue `deal-context.md` para código, target, data de fechamento, líder.
2. Carregue `integration-tracker.yaml` se existir (ou crie em --init).
3. Use o fluxo abaixo.
4. Roteie por flag:
   - `--init`: Modo 1 — leia SPA, monte workplan faseado, tracker de anuências
   - `--contracts`: Modo 2 — importe lista de contratos, classifique
   - `--report`: Modo 3 — relatório de status
   - `--update`: Modo 4 — manual ou parse de documento de status
   - `--export`: Modo 5 — CSV ou tabela
5. Leia/grave `~/.claude/plugins/config/claude-for-legal/societario/deals/[code]/integration-tracker.yaml`.
6. Após qualquer write: sumário das mudanças e novas flags.

---

## Contexto de matéria

**Contexto de matéria.** Verifique `## Workspaces de matéria` no CLAUDE.md nível-prática. Se `Habilitado` for `✗` (padrão in-house), pule. Se habilitado e sem matéria, pergunte: "Qual matéria? Rode `/societario:matter-workspace switch <slug>` ou diga `nível-prática`." Carregue `matter.md`. Grave saídas na pasta da matéria. Nunca leia outras matérias a menos que `Contexto cross-matter` esteja `on`.

---

## Propósito

Advogado(a) externo(a) fecha. Jurídico herda a confusão. Esta skill é a camada de
program management para integração pós-fechamento — não a integração de negócios,
nem TI, nem desenho de organização de RH. O workstream jurídico: anuências,
cessões de contratos, racionalização de entidades, registros de PI no INPI,
obrigações do SPA. Acompanha o que está feito, o que vence, o que está travado
e o que precisa de decisão.

---

## Arquivo do tracker

Vive em `~/.claude/plugins/config/claude-for-legal/societario/deals/[code]/integration-tracker.yaml`. Leia `deal-context.md` para código, target, data de fechamento, líder. Herde itens pós-fechamento de `closing-checklist.yaml` se existir.

```yaml
# integration-tracker.yaml

metadata:
  deal_code: "[código]"
  target: "[denominação]"
  close_date: "[YYYY-MM-DD]"
  deal_lead: "[nome]"
  outside_counsel: "[escritório e advogado(a) líder]"
  last_updated: "[data]"
  last_status_report: "[data ou null]"

spa_dates:
  required_consents_deadline: "[YYYY-MM-DD — extraído do SPA]"
  rep_survival_expires: "[YYYY-MM-DD — prazo das declarações e garantias]"
  escrow_release: "[YYYY-MM-DD ou null]"
  earnout_milestones:
    - description: "[marco]"
      measurement_date: "[YYYY-MM-DD]"
      payment_date: "[YYYY-MM-DD]"
      owner: "financeiro"   # sempre financeiro — jurídico só rastreia a data

workplan:
  day_1:
    target_date: "[close_date + 7 dias]"
    items: []
  day_30:
    target_date: "[close_date + 30 dias]"
    items: []
  day_90:
    target_date: "[close_date + 90 dias]"
    items: []
  day_180:
    target_date: "[close_date + 180 dias]"
    items: []

required_consents: []
desired_consents: []

contracts:
  source: "[repositório / upload manual / disclosure-schedule]"
  repository_path: "[caminho ou null]"
  last_imported: "[data]"
  total: 0
  tier_1: []
  tier_2: []
  tier_3: []
  tier_4: []
```

**Estrutura de item de workplan:**
```yaml
- id: "W-001"
  description: "[ação]"
  phase: "[day_1 / day_30 / day_90 / day_180]"
  owner: "[legal-owns / legal-supports]"
  workstream: "[legal / hr / it / finance / real-estate / outros]"
  priority: "[critical / high / medium / low]"
  deadline: "[YYYY-MM-DD ou null]"
  deadline_basis: "[spa-obligation / regulatório / boas-práticas]"
  status: "[not_started / in_progress / complete / blocked / deferred]"
  blocker: "[descrição ou null]"
  depends_on: "[id ou null]"
  notes: ""
```

**Estrutura de anuência:**
```yaml
- id: "CON-001"
  counterparty: "[nome]"
  contract_type: "[cliente / fornecedor / locação / licença PI / financeiro / outros]"
  required_consent: true        # true = listado no anexo de Anuências Requeridas do SPA
  spa_deadline: "[YYYY-MM-DD]"
  status: "[not_started / outreach_sent / in_negotiation / obtained / waived / refused]"
  assigned_to: "[nome ou null]"
  outreach_date: "[data ou null]"
  obtained_date: "[data ou null]"
  notes: ""
```

**Estrutura de contrato:**
```yaml
- id: "C-001"
  name: "[nome do contrato ou arquivo]"
  counterparty: "[parte]"
  contract_type: "[MSA / SaaS / locação / licença PI / emprego / NDA / outros]"
  annual_value: "[valor ou desconhecido]"
  assignment_mechanism: "[cessao_automatica / consent-required / coc-provision / silente]"
  tier: 1   # 1=Anuências Requeridas SPA, 2=material+consent-required, 3=CoC, 4=cessão automática
  required_consent: false
  spa_deadline: "[YYYY-MM-DD ou null]"
  status: "[not_reviewed / no_action / consent_pending / outreach_sent / in_negotiation / consent_obtained / assignment_complete / waived / refused / coc_triggered]"
  assigned_to: "[nome ou null]"
  notes: ""
  last_updated: "[data]"
```

---

## Modo 1: Inicializar

```
/societario:integration-management --init [--deal [code]]
```

### Passo 1: Carregar contexto

Leia `deal-context.md`. Se não encontrar: peça código, target, data de fechamento, líder, advogado(a) externo(a). Escreva no deal-context.md se não existir.

Leia `closing-checklist.yaml` se existir. Itens marcados como pós-fechamento viram itens Day 1 ou Day 30 (herdam status).

### Passo 2: Ler inputs do deal

**SPA completo produz tracker mais completo.** O anexo de Anuências Requeridas e seção de obrigações pós-fechamento são fonte autoritativa de prazos e obrigações. Mas a skill inicializa do que tiver — inputs parciais produzem starter tracker em vez de página vazia.

> Quais artefatos você tem? Compartilhe o que existir:
>
> **Ideal:** O SPA (upload ou caminho). Leio obrigações pós-fechamento, anexo de Anuências, prazos de declarações e garantias, escrow, earn-out.
>
> **Também útil:**
> - Sumário do deal ou termo de compromisso
> - To-do list pós-fechamento do advogado(a) externo(a)
> - Workplan ou tracker existente
> - Checklist de fechamento — se gerado pelo cold-start, herdo de `closing-checklist.yaml` automaticamente
> - Lista de Anuências Requeridas isolada
>
> **Se nada escrito:** Me conte o deal em linguagem simples — quem foi adquirido(a), quando fechou, principais itens em aberto — e construo starter tracker do workplan padrão Day 1/30/90/180.

**O que muda conforme provido:**

| Input | O que recebe |
|---|---|
| SPA completo | Workplan completo + Anuências Requeridas com prazos + datas do SPA |
| SPA + lista de contratos | Tracker completo + tier list de cessão |
| Sumário/to-do | Esqueleto de workplan, Anuências como placeholders |
| Nada | Scaffold de workplan padrão; advogado(a) preenche |

**Do SPA extraia:**

*Anuências Requeridas:*
- Por anuência: contraparte, tipo de contrato, prazo. Set `required_consent: true` com `spa_deadline`.

*Obrigações pós-fechamento:*
- Mapeie cada obrigação para item de workplan. Atribua à fase certa. Tag como `spa-obligation`.

*Datas-chave:*
- Prazo de Anuências Requeridas — extraia do SPA
- Vencimento das declarações e garantias — puxe prazos específicos. Reps gerais, fundamentais e tributárias tipicamente têm prazos diferentes; puxe cada um separadamente. Não assuma default.
- Liberação de escrow — extraia
- Earn-out — adicione a `spa_dates.earnout_milestones`, owner sempre "financeiro"

### Passo 3: Montar o workplan faseado

Gere itens padrão para cada fase. Acrescente obrigações do SPA. Itens herdados do checklist de fechamento são pré-populados.

**Day 1 — legal-owns:**
- Arquivamento de alteração de denominação (se target sendo renomeado) na Junta Comercial [crítico]
- Atualização de assinaturas bancárias — notificar bancos com docs de fechamento [crítico]
- Comunicação a despachante/registradores sobre mudança de controle [alto]
- Execução de cessões de PI — se cessões foram diferidas do fechamento [crítico]
- Transferência de domínios e contas em redes sociais [alto]
- Seguro D&O — confirme tail policy para administradores do target [crítico]
- Notificações a Juntas Comerciais sobre mudança de controle e arquivamentos exigidos pelo estatuto/CC [alto]
- Atualização de procurações vigentes (revogar/substituir) [alto]
- Atualização cadastral RFB (CNPJ — alterações de quadro societário, art. 17 IN RFB 2.119/2022) [alto]
- Se houver investimento estrangeiro: atualização do RDE-IED no SISBACEN (Resolução BCB 278/2022) [alto]

**Day 1 — legal-supports:**
- Comunicado a empregados (RH é dono(a), jurídico revisa) [crítico]
- Confirmação de cobertura Day 1 de benefícios (RH dono(a), jurídico assessora sobre transição e direitos adquiridos) [crítico]
- Cartas a clientes (negócio dono(a), jurídico revisa precisão)

**Day 30 — legal-owns:**
- Push inicial de Anuências Requeridas — contate todas as contrapartes, documente outreach [crítico]
- Averbação de cessões no INPI (patentes, marcas, software, programas de computador — LPI arts. 58-64 para patentes, 134-141 para marcas; Lei 9.609/98 para software) [alto]
- Registro de cessão de direitos autorais conforme aplicável [médio]
- Atualização de procurações em órgãos públicos (Receita Federal, Procuradorias, Juntas Comerciais) [alto]
- Revisão de tier 1 e tier 2 — análise de cessão completa [alto]
- Confirmação final de seguro tail [alto]
- Comunicação CADE pós-ato (se notificação ordinária foi feita) ou ato de concentração se necessário (Lei 12.529/2011) [alto]

**Day 30 — legal-supports:**
- Revisão de privacidade na migração de dados (TI dono(a), jurídico assessora — LGPD art. 7º para bases legais, art. 11 para sensíveis, art. 33 para transferência internacional)
- Revisão de cláusulas de cessão em locações (facilities dono(a), jurídico assessora — Lei 8.245/91 admite cessão com anuência)

**Day 90 — legal-owns:**
- Prazo de Anuências Requeridas — todas devem estar obtidas ou escaladas [crítico, deadline: spa_dates.required_consents_deadline]
- Decisão sobre racionalização de entidades — manter separada / incorporar / cindir / dissolver [alto] (atos: incorporação art. 227 Lei 6.404 / arts. 1.116-1.118 CC; fusão art. 228 Lei 6.404; cisão art. 229; transformação arts. 220-222 Lei 6.404 / arts. 1.113-1.115 CC; dissolução arts. 206-218 Lei 6.404 / arts. 1.033-1.038 CC)
- Documentação de assunção ou rescisão de planos de benefícios [alto]
- Push secundário de anuências pendentes [alto]
- Resolução de mudança de controle Tier 3 [crítico]
- Avaliação de impacto sobre acordo de acionistas (art. 118 Lei 6.404) — anti-diluição, tag-along, drag-along — verifique gatilhos [alto]

**Day 90 — legal-supports:**
- Harmonização de RH (RH dono(a), jurídico assessora — CLT, Reforma Trabalhista 13.467/2017, jurisprudência TST sobre sucessão de empregadores arts. 10 e 448 CLT)

**Day 180 — legal-owns:**
- Arquivamento de ato de incorporação/fusão/cisão na Junta Comercial — se decisão de racionalização for combinar [alto]
- Arquivamento de distrato/dissolução — se decisão for liquidar [alto]
- Novação completa de contratos exigindo nome do(a) adquirente [alto]
- Tracking de prazo de declarações e garantias — note vencimento próximo [médio]
- Revisão de programa de integridade (Lei Anticorrupção 12.846/2013 + Decreto 11.129/2022) — integração de políticas, treinamento, due diligence de terceiros, canal de denúncias [alto]
- Revisão de preço de transferência para operações intercompany pós-deal (Lei 14.596/2023 — convergência OECD; IN RFB 2.161/2023) [alto]

Sumário:

```
Tracker inicializado — [Código] / [Target]

Data de fechamento: [data]
Prazo Anuências Requeridas: [data] ([N] dias)
Vencimento declarações e garantias: [data]

Itens de workplan: [N] ([N] legal-owns, [N] legal-supports)
Anuências Requeridas: [N] (do anexo do SPA)
Anuências Desejadas: [N] (de diligência — sem prazo SPA)

Cessão de contratos: ainda não importada — rode --contracts

Próximo passo: rode /societario:integration-management --contracts e depois --report.
```

---

## Modo 2: Cessão de Contratos

```
/societario:integration-management --contracts [--deal [code]]
```

### Passo 1: Obter a lista

**Caminho A: Repositório conectado**

> Seu repositório de contratos está conectado? (Drive, Box, SharePoint, ou VDR ainda acessível pós-fechamento?)
>
> Se sim: caminho da pasta dos contratos do target. Puxo lista e leio cada contrato para cláusula de cessão e contraparte.

Busca no repositório. Para cada doc:
- Filename e path
- Leia — contraparte, tipo, texto da cláusula de cessão, mudança de controle se presente, valor anual se declarado.

**Caminho B: Upload manual**

> Uploade lista. Pode ser:
> - Anexo de Contratos Materiais dos disclosure schedules do SPA
> - Export CSV/Excel do sistema de gestão de contratos
> - Lista manual
>
> Colunas mínimas: Nome do Contrato, Contraparte. Úteis: Tipo, Valor Anual, Texto da Cláusula de Cessão.

Sem texto de cessão, `assignment_mechanism: not_reviewed` e sinalize.

**Caminho C: Disclosure schedule**

Se nem repositório nem lista, leia anexo de Contratos Materiais do SPA. Mínimo: partes e tipos. Cessões precisam de revisão manual.

### Passo 2: Determinar mecanismo de cessão

| Mecanismo | Definição | Tier |
|---|---|---|
| `consent-required` | Cláusula expressa proibindo cessão sem anuência | 1 ou 2 |
| `coc-provision` | Cláusula de mudança de controle dando à contraparte rescisão ou anuência disparada pelo deal | 3 |
| `cessao_automatica` | Sem restrição, ou permissão expressa de cessão a afiliadas/sucessoras | 4 |
| `silente` | Sem cláusula — default pela lei aplicável. Para contratos brasileiros: cessão de posição contratual exige anuência da contraparte (CC art. 286 e ss. — cessão de crédito; arts. 299-303 — assunção de dívida; jurisprudência STJ para cessão de posição contratual). Sinalize para revisão. | 2 |
| `not_reviewed` | Não foi possível ler ou localizar a cláusula | Sinalize |

Para contratos no anexo de Anuências Requeridas do SPA: tier 1 independentemente da classificação.

### Passo 3: Atribuição de tier

```
Tier 1 — Anuências Requeridas: [N] contratos
  Listados no anexo SPA, prazo duro [data], precisa obter anuência

Tier 2 — Material, anuência exigida: [N] contratos
  Restrição de cessão presente, fora do anexo SPA
  Timeline recomendado: obter dentro do Day 90

Tier 3 — Cláusulas de mudança de controle: [N] contratos ⚠️
  Contraparte tem direito de rescisão ou anuência disparado pelo fechamento
  AÇÃO REQUERIDA: contate imediatamente — CoC pode já ter disparado

Tier 4 — Cessão automática / sem ação: [N] contratos
  Cede automaticamente ou por cláusula de afiliada/sucessora
  Apenas tracking

Não revisados: [N] contratos
  Mecanismo de cessão indeterminado — revisão manual
```

Mostre Tier 3 separado e proeminente. Cláusula de mudança de controle pode já ter disparado na data de fechamento — contraparte pode ter direito de rescindir agora.

### Passo 4: Gerar entradas

Para cada contrato:
- Campos extraídos
- Status inicial: tier 4 → `no_action`; tier 3 → `coc_triggered`; tiers 1/2 → `consent_pending`; not_reviewed → `not_reviewed`
- spa_deadline para tier 1 do anexo

---

## Modo 3: Status Report

```
/societario:integration-management --report [--deal [code]]
```

```
[CABEÇALHO DE MATERIAL DE TRABALHO — conforme config ## Outputs — varia por papel]

> Este relatório decorre do SPA, achados de diligência e registros de integração. Herda status de sigilo e confidencialidade — distribuição fora do círculo de sigilo (art. 7º XIX EOAB) pode quebrar a proteção. Confirme destinatários antes de enviar.

STATUS DE INTEGRAÇÃO — [Código] / [Target]
[Data] — Dia [N] pós-fechamento

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

SUMÁRIO EXECUTIVO
[2-3 frases: status geral, maior risco, conquista-chave desde o último relatório]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

ANUÊNCIAS REQUERIDAS  [prazo: DATA — N dias restantes]
  Obtidas:           [N] de [total]  ████████░░  [%]
  Em negociação:     [N]
  Outreach enviado:  [N]
  Não iniciadas:     [N]
  Recusadas:         [N] ⚠️

⚠️ EM RISCO: [contraparte] — prazo em [N] dias, sem resposta
⚠️ RECUSADA: [contraparte] — obrigação do SPA não atendida; escalar para advogado(a) externo(a)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

CESSÃO DE CONTRATOS
  Tier 1 (Anuências Requeridas): [N] completas / [N] em andamento / [N] pendentes
  Tier 2 (Contratos materiais):  [N] completas / [N] em andamento / [N] pendentes
  Tier 3 (Mudança de controle):  [N] resolvidas / [N] em aberto ⚠️
  Tier 4 (Cessão automática):    [N] — sem ação

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

WORKPLAN — LEGAL OWNS
  🔴 ATRASADOS ([N]):
    [item] — venceu em [data]

  ⏰ VENCE ESTA SEMANA ([N]):
    [item] — vence em [data]

  ✅ CONCLUÍDOS DESDE ÚLTIMO REPORT ([N]):
    [item] — concluído em [data]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

BLOQUEIOS & DECISÕES NECESSÁRIAS
  [item] — bloqueado em: [descrição] — dono(a): [nome]
  [item] — decisão: [descrição] — recomendação: [opção]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

PRÓXIMAS DATAS-CHAVE
  [data] — [marco / prazo]
  [data] — Vencimento das declarações e garantias — confirme se não há pleitos de indenização pendentes

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

## Modo 4: Update

```
/societario:integration-management --update [--deal [code]]
```

**Atualização manual:**

> "Obtivemos a anuência da Salesforce. Marque como obtido, atribuído a [nome], data hoje."
> "Decisão de racionalização é incorporação. Atualize status e adicione arquivamento de incorporação no Day 180 (art. 227 Lei 6.404 + arquivamento na Junta + averbações no INPI e RFB)."
> "[Contraparte] recusou anuência. Sinalize e note que precisamos de advogado(a) externo(a) para pleito de indenização sob o SPA."

Claude atualiza, recalcula downstream (ex.: se todas as Tier 1 estão obtidas, marca obrigação do SPA como atendida), e mostra o que mudou.

**Upload:**

> Uploade atualização de status do(a) [advogado(a) externo(a) / RH / corp dev].
> Faço parse e atualizo.

Casa por contraparte ou descrição. Sinalize itens novos.

Após:
```
Atualizados [N] itens.

Mudanças:
  CON-003 Salesforce: not_started → obtained
  W-014 Racionalização: in_progress → complete

Novas flags:
  CON-007 [Contraparte]: refused — obrigação SPA pode estar não atendida. Considerar:
  revisão de advogado(a) externo(a) sobre pleito de indenização. ⚠️
```

---

## Modo 5: Export

```
/societario:integration-management --export [--format csv|table] [--section all|consents|contracts|workplan]
```

CSV — uma linha por item, com coluna `section`.

*Workplan:* id, phase, description, owner, workstream, priority, deadline, status, blocker

*Consents:* id, counterparty, contract_type, required_consent, spa_deadline, status, assigned_to, obtained_date, notes

*Contracts:* id, name, counterparty, contract_type, annual_value, assignment_mechanism, tier, required_consent, spa_deadline, status, assigned_to, notes

---

## O que esta skill não faz

- Não gerencia workstreams de negócio (TI, RH, financeiro, imobiliário). Acompanha touchpoints do jurídico.
- Não minuta cartas de anuência ou contratos de novação — produzidos pela skill written-consent ou advogado(a) externo(a).
- Não aconselha sobre pleitos de indenização. Quando anuência é recusada ou prazo perdido, sinaliza — análise é do(a) advogado(a).
- Não rastreia performance de earn-out. Marcos aparecem como datas de referência com owner financeiro.
- Não lê contratos em tempo real durante reporting. Status é o que o(a) advogado(a) atualizou.


## Defesa contra injeção de fórmula

Antes de gravar qualquer célula em Excel, Sheets ou CSV, neutralize injeção. Texto vindo de contraparte (citações contratuais, nomes de partes, dados de despachante, exports CLM) é controlado por atacante. Célula iniciando com `=`, `+`, `-`, `@`, `	`, `
`, ou `
` será interpretada como fórmula ou quebra a estrutura.

- **Prefixe com aspas simples:** `'=SUM(A1:A10)` → `=SUM(A1:A10)`
- **Aplica-se a toda célula com texto vindo de documento, ferramenta ou paste.**
- **CSV: escape vírgulas embutidas, aspas duplas, quebras de linha** (RFC 4180).
- Não é opcional.
