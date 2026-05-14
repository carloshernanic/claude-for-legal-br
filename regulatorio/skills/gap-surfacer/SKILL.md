---
name: gap-surfacer
description: >
  Referência: framework compartilhado de tracker de lacunas e consultas que
  sustenta /regulatorio:gaps e /regulatorio:comments. Acompanha lacunas
  de política abertas com status de remediação, ingere lacunas do policy-diff,
  faz aflorar o que está aberto e envelhecendo, roteia para responsáveis, e
  notifica owners via Slack com confirmação per-send. Carregado pelas skills
  gaps e comments antes de trabalho substantivo.
user-invocable: false
---

> **Adaptação BR não oficial.** Calibrado para Lei 9.784/1999, LINDB arts. 20-30 (Lei 13.655/2018), Lei 13.874/2019 (Liberdade Econômica), Dec. 10.411/2020 (AIR). Reguladores BR: ANPD, CVM, BCB, ANVISA, ANATEL, ANS, ANEEL, ANP, ANTT, ANAC, IBAMA, CADE, SENACON, Procons, COAF. Toda saída é rascunho para revisão por advogado(a) inscrito(a) na OAB.

# Gap Surfacer

> Notificações ao owner: ligadas por padrão. Para excluir um owner, deixe `owner_slack` vazio.

## Per-send confirmation — sem exceções

Antes de enviar QUALQUER mensagem no Slack (notificação de atribuição, lembrete de atraso, notificação em massa, status report):

1. Mostre ao usuário exatamente o que vai enviar e para quem: "Vou enviar isto para [N] pessoas: [preview]."
2. Aguarde um "sim" explícito.
3. Se a mensagem contiver citações, prazos, ou conclusões de compliance, acrescente: "⚠️ As citações nesta mensagem não estão verificadas — não estou confirmando que estão atuais antes do envio. Quer que eu adicione uma linha 'verifique antes de agir'?"
4. Nunca envie sem o confirm. Não em cadência. Não em batch. Não porque foi enviado ontem.

Auto-send sem confirmação é a ação mais irreversível neste plugin — envia conteúdo cujo próprio rodapé deste plugin diz que pode estar errado, para pessoas que não têm como checar. Essa combinação não escapa da revisão.

## Matter context

**Matter context.** Verifique `## Matter workspaces` no CLAUDE.md practice-level. Se `Enabled` for `✗` (default para usuários in-house), pule o resto deste parágrafo — skills usam contexto practice-level e a maquinaria de matter é invisível. Se habilitado e não há matter ativo, pergunte: "Para qual matter é isso? Rode `/regulatorio:matter-workspace switch <slug>` ou diga `practice-level`." Carregue o `matter.md` da matter ativa para contexto e overrides. Escreva outputs na pasta da matter em `~/.claude/plugins/config/claude-for-legal/regulatorio/matters/<matter-slug>/`. Nunca leia arquivos de outra matter a menos que `Cross-matter context` esteja `on`.

---

## Purpose

Lacunas são encontradas e esquecidas. Esta skill as acompanha até serem fechadas
e notifica os responsáveis por fechá-las.

## The tracker

Fica em `~/.claude/plugins/config/claude-for-legal/regulatorio/gap-tracker.yaml`:

> **Nota sobre comment-tracker.yaml:** `~/.claude/plugins/config/claude-for-legal/regulatorio/comment-tracker.yaml` é arquivo irmão de propriedade da skill comments. É escrito pelo reg-feed-watcher (que loga consultas públicas automaticamente) e pela skill comments (que acompanha decisões de manifestação iniciadas pelo usuário). Esta skill não lê nem cruza com ele. Se você modificar o schema do comment-tracker, atualize ambos consumidores reais.

```yaml
gaps:
  - id: GAP-001
    requirement: "[o que a norma exige]"
    regulation: "[nome + cite — ex.: Resolução CD/ANPD nº 15/2024]"
    policy_affected: "[nome ou 'nova política necessária']"
    gap_type: "partial"  # none | partial | full | new-policy | watch | comment-decision
    owner: "[nome no índice de políticas]"
    owner_slack: "[Slack user ID ou handle, se conhecido]"
    opened: 2026-03-01
    due: 2026-06-01  # data de vigência (após vacatio legis), prazo interno, ou prazo de consulta pública
    status_verified: true  # false se o policy-diff upstream não conseguiu confirmar a norma vigente; itens não-verificados nunca chegam ao 🔴 Overdue
    status: "open"  # open | in-progress | closed | risk-accepted
    notified: false  # vira true após envio da notificação de atribuição
    resolution: ""  # preenchido no fechamento
```

**Nunca classifique uma lacuna como Overdue sobre norma não verificada.** A classificação 🔴 Overdue significa "perdemos um prazo vinculante". Se o status da norma estiver não verificado (policy-diff setou `status_verified: false`, ou a norma tem >12 meses / passou da data de aplicabilidade sem confirmação de vigência), o prazo pode não ser vinculante. Use 🟡 "Revisão necessária" e note: "Se esta norma estiver em vigor como publicada, isto estaria atrasado em [N] dias. Verifique status da norma antes de escalar." Roteie itens de norma-não-verificada para `watch`, não para os buckets ativos de overdue/due-soon; a cadência de revisita do `watch` força checagem de status antes do item poder ressurgir como lacuna de compliance.

**Semântica de `gap_type`:**

| Valor | Significado | Cadência típica de lembrete |
|---|---|---|
| `none` | Política já cobre a exigência. Logado para trilha de auditoria apenas. Deve ser raro — se a maioria das entradas é `none`, o diff provavelmente está rodando contra a política errada. | Sem lembrete automático. |
| `partial` | Política aborda o tópico mas não cobre a nova exigência integralmente. Precisa de emenda. | 30 dias antes do prazo. |
| `full` | Política contradiz ou silentemente omite a nova exigência. Precisa de reescrita ou nova seção. | 30 dias antes do prazo. |
| `new-policy` | Nenhuma política existente cobre isto. Política precisa ser elaborada. | 30 dias antes do prazo. |
| `watch` | Item forward-looking — tomada de subsídios, AIR ainda em consulta, ou minuta de norma ainda não publicada. Sem obrigação de compliance hoje; trabalho de política aguarda a norma final (após vacatio legis LINDB art. 1º). `due:` é data de revisita (tipicamente data esperada da consulta pública ou horizonte de um ano), não prazo de compliance. | Sem lembrete automático; reavaliar quando uma consulta pública for aberta ou na data de revisita. |
| `comment-decision` | Decisão pré-norma pendente — tomada de subsídios ou consulta pública onde o time está decidindo se manifesta. `due:` é prazo da consulta. | 21 dias antes do prazo (mais apertado que lacunas de compliance porque janelas de redação são curtas). |

Uma entrada `watch` ou `comment-decision` não é lacuna de compliance — é artefato de tracking para itens pré-norma que as skills watch e comments produzem. Faça-os aflorar no status report em bucket próprio para que o(a) advogado(a) lendo às 7h consiga distinguir num relance "consertar isto antes que um regulador note" vs. "ficar de olho nisso".

## Modes

### Mode 1: Ingest from policy-diff

Quando o policy-diff encontrar lacunas, append em gap-tracker.yaml. Deduplique — mesma
exigência + mesma política = mesma lacuna, não conte em dobro.

**Após ingerir, notifique o owner:**

Se Slack MCP estiver disponível e `owner_slack` setado:

Envie DM no Slack para o gap owner — mas só após a confirmação per-send no topo deste arquivo. Preview da mensagem ao usuário, aguarde sim explícito, então envie:

```
📋 Nova lacuna de compliance atribuída a você

Lacuna: [GAP-ID] — [exigência, uma frase]
Norma: [nome + link]
Política afetada: [nome da política ou "nova política necessária"]
Prazo: [data de vigência da norma]

Veja o tracker completo: /regulatorio:gaps
```

Setar `notified: true` na entrada do tracker após envio.

Se Slack MCP não disponível: note no status report que notificação ao owner não foi enviada
e sinalize para follow-up manual.

### Mode 2: Status report

```markdown
[WORK-PRODUCT HEADER — por config do plugin ## Outputs — varia por role; veja `## Who's using this`]

## Open Gaps — [data]

### Bottom line

[N lacunas exigem ação até [data] — top 3: X, Y, Z]

### 🔴 Overdue

| ID | Exigência | Política | Owner | Prazo | Dias em atraso |
|---|---|---|---|---|---|

### 🟠 Vence em <30 dias

[mesmo]

### 🟡 Aberta

[mesmo]

### 👀 Watch items (forward-looking — pré-norma)

[Tracking pré-norma — entradas `watch` e `comment-decision`. Não são
lacunas de compliance. Faça aflorar separadamente para que as bandas
overdue/due-soon contenham apenas prazos reais de compliance.]

| ID | Item | Tipo (Tomada de Subsídios / Consulta Pública / AIR) | Prazo de manifestação | Owner |
|---|---|---|---|---|

### Em andamento

[mesmo]

### Recém-fechadas

[últimas 5, com resolução]

---

**Lacuna aberta mais antiga:** [ID], [N] dias
**Lacunas por owner:** [breakdown]
**Notificações de owner enviadas:** [N] / [N total de lacunas]

---

**Próximo passo para cada lacuna aberta:** `/regulatorio:policy-redraft` produz redraft marcado da política com tags `[verify]` e resumo de mudanças. É proposta para revisão do owner da política — não edição direta nos documentos-fonte.

---

**Verifique citações antes de confiar nelas.** Citações de normas neste tracker foram geradas por IA upstream (pelo reg-feed-watcher e policy-diff) e não foram checadas contra fonte primária. Antes de fechar ou risk-aceitar uma lacuna — ou citá-la em atestação interna, relatório ao board, ou resposta a regulador — confirme a norma subjacente contra o DOU (in.gov.br), planalto.gov.br, site oficial da agência (ANPD, CVM, BCB, ANVISA, ANATEL, ANS, ANEEL, ANP, ANTT, ANAC, IBAMA, CADE, SENACON), ou plataforma de pesquisa do escritório. Citações regulatórias geradas por IA podem ser fabricadas, mal citadas, ou desatualizadas. Source tags trazidas de upstream (ex.: `[DOU]`, `[ANPD]`, `[CVM]`, `[BCB]`, `[CADE]`, `[web search — verify]`) mostram a origem; tags `verify` carregam risco mais alto de fabricação e devem ser checadas primeiro. Nunca tire as tags ao fazer aflorar lacunas.
```

## Config-dependent fallbacks

Esta skill lê owners de resposta a lacunas e cadeia de escalada de `~/.claude/plugins/config/claude-for-legal/regulatorio/CLAUDE.md`. Quando valor necessário estiver vazio ou ainda `[PLACEHOLDER]`:

- **Triador de lacunas faltando:** deixe a atribuição em aberto e acrescente à saída: "Nenhum triador está setado em `## Gap response process`. Atribua um com `/regulatorio:cold-start-interview --redo` ou editando `~/.claude/plugins/config/claude-for-legal/regulatorio/CLAUDE.md` para que novas lacunas sejam roteadas."
- **Owner desconhecido para lacuna recém-ingerida (sem owner na biblioteca de políticas):** log a lacuna com `owner: [unassigned]` e acrescente: "[N] lacunas foram ingeridas sem owner porque a biblioteca de políticas não nomeia um para a política afetada. Preencha a coluna Owner na biblioteca para roteá-las."
- **Cadeia de escalada faltando para lacuna material em atraso:** ainda reporte como atrasada, e acrescente: "Nenhuma cadeia de escalada está setada para lacunas materiais em atraso. Configure com `/regulatorio:cold-start-interview --redo` ou editando `~/.claude/plugins/config/claude-for-legal/regulatorio/CLAUDE.md`."

Nada a dizer sobre config quando os valores estiverem populados.

**Lógica de lembrete de prazo (roda durante status report e agente agendado):**

Cadência de lembrete é função de `gap_type` — lacunas de compliance ganham aviso de 30 dias, itens comment-decision ganham 21 (mais apertado porque a janela de redação é mais curta), watch items não ganham lembrete automático (reavaliar quando consulta pública for aberta).

Para cada lacuna com status "open" ou "in-progress":
- `partial`, `full`, `new-policy`, `none`: se prazo está dentro de 30 dias e lembrete não foi enviado nos últimos 7 dias, PREVIEW DM Slack (assunto "⏰ Lembrete: lacuna de compliance vence em [N] dias") e aguarde per-send confirm antes de enviar.
- `comment-decision`: se prazo da consulta está dentro de 21 dias e lembrete não foi enviado nos últimos 7 dias, PREVIEW DM Slack (assunto "💬 Decisão de manifestação em [N] dias") e aguarde per-send confirm antes de enviar.
- `watch`: sem lembrete automático. Revisite quando o tracker for revisado ou uma consulta pública for logada para a mesma norma.
- Se prazo passou em lacuna de compliance: sinalize como overdue no relatório e PREVIEW DM Slack — aguarde per-send confirm antes de enviar.
- Se prazo da consulta passou em item `comment-decision` e nenhuma manifestação foi protocolada: sinalize como overdue, PREVIEW DM Slack (aguarde per-send confirm), e peça ao owner para atualizar para `risk-accepted` (não-manifestação deliberada) ou `closed` (manifestação protocolada) com nota.
- Registre timestamps de lembrete no tracker para evitar repetição.
- Lembretes em batch ainda exigem per-send confirm — preview de "você vai enviar 12 DMs" e aguardar sim conta; disparar batch em silêncio não.

### Consequential-action gate (atestar compliance)

**Antes de fechar lacuna como resolvida, ou produzir qualquer saída que ateste compliance com exigência regulatória (atestação interna, relatório ao board, resposta a auditoria, resposta a ofício de regulador em PAS):** Leia `## Who's using this` em ~/.claude/plugins/config/claude-for-legal/regulatorio/CLAUDE.md. Se Role é **Não-advogado(a)**:

> Atestar compliance — ou fechar lacuna como resolvida — tem consequências jurídicas. A atestação pode ser usada contra a empresa se depois mostrar-se errada (Lei 12.846/2013 art. 7º, VIII, valoriza programa de integridade efetivo; atestação falsa pode subverter o benefício), e fechamento prematuro deixa exposição não endereçada. Você revisou isso com advogado(a) inscrito(a) na OAB? Se sim, prossiga. Se não, eis um briefing para levar:
>
> - A lacuna (exigência, fonte, o que o policy diff achou)
> - O que a resolução proposta cobre e o que não cobre
> - Qualquer lacuna residual ou ambiguidade
> - Perguntas em aberto e o que está irresolvido
> - O que pode dar errado (atestação ampla demais, obrigação residual não resolvida, posição inconsistente com pretérita)
> - O que perguntar ao(à) advogado(a) (isto está realmente fechado; deveríamos risk-aceitar com justificativa; precisamos de concordância de outside counsel)
>
> Se você precisa de advogado(a): OAB seccional do seu estado; CESA e IASP também têm canais de indicação.

Não marque lacuna como fechada nem produza atestação de compliance passando por este gate sem um "sim" explícito. Status reports e views de tracking não exigem o gate.

### Mode 3: Close a gap

```
/regulatorio:gaps --close GAP-001
Resolução: "Política atualizada v2.3, aprovada em [data]"
```

Atualiza status para closed, registra resolução e data de fechamento.

### Mode 4: Risk-accept a gap

Às vezes a resposta é "não vamos consertar isto". É decisão válida — mas
deve ser documentada. LINDB art. 22 (Lei 13.655/2018) exige motivação adequada
em decisões administrativas, inclusive de não-fazer.

```
/regulatorio:gaps --accept GAP-002
Justificativa: "Exigência aplica-se apenas a [condição que não atendemos]. Revisitar se [trigger]."
Aceito por: [nome com competência]
```

Status → risk-accepted. Permanece no tracker (não deletada) mas sai do
relatório de lacunas abertas.

## Integration: reg-change-monitor agent

O digest do agente inclui contagem de lacunas e idade da lacuna aberta mais antiga. Se
algo virar overdue, isso vai no topo do digest. O agente também roda a checagem de
lembrete de prazo e envia notificações Slack pendentes.

## Close with the next-steps decision tree

Encerre com o decision tree de próximos passos por CLAUDE.md `## Outputs`. Customize as opções ao que esta skill acabou de produzir — os cinco branches padrão (draft o X, escalar, mais fatos, watch and wait, outra coisa) são ponto de partida, não trava. A árvore É a saída; o(a) advogado(a) escolhe.

Se o tracker fez aflorar mais de ~10 lacunas abertas, ou se o usuário pedir: ofereça o dashboard (veja CLAUDE.md `## Outputs → Dashboard offer for data-heavy outputs`). Modele a oferta para esta saída — contagens por severidade, timeline de lacunas por prazo, e grid sortável com owner, status, e last-touched.

## What this skill does not do

- Fecha lacunas sozinha. Fechamento exige nota de resolução e a ação humana que a nota descreve.
- Envia notificações Slack se o Slack MCP não estiver configurado. Faz fallback para sinalização no status report.
- Envia mais de um lembrete por janela de 7 dias por lacuna. Cutuca uma vez, não constantemente.
