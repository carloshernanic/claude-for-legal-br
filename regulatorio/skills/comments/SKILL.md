---
name: comments
description: Revise janelas de consulta pública / tomada de subsídios abertas, registre decisões, acompanhe prazos. Use quando uma consulta pública (ANPD, CVM, BCB, ANVISA, ANATEL, ANS, ANEEL, ANP, ANTT, ANAC, IBAMA, CADE, SENACON) estiver com janela aberta e for preciso fazer aflorar prazos, decidir filing/não-filing, ou registrar uma decisão (--decide CMT-ID).
argument-hint: "[opcional: --decide CMT-ID]"
---

> **Adaptação BR não oficial.** Calibrado para Lei 9.784/1999 (consulta pública / tomada de subsídios), LINDB arts. 20-30 (Lei 13.655/2018), Decreto 10.411/2020 (AIR), e os reguladores BR (ANPD, CVM, BCB, ANVISA, ANATEL, ANS, ANEEL, ANP, ANTT, ANAC, IBAMA, CADE, SENACON). Toda saída deste plugin é rascunho para revisão por advogado(a) inscrito(a) na OAB.

# /comments

## Purpose

Consultas públicas têm prazo. A decisão de manifestar-se (ou não) é decisão
de advogado(a) — mas o prazo evaporando sem decisão registrada é o risco.
Esta skill faz aflorar janelas abertas e registra decisões.

## Load context

`~/.claude/plugins/config/claude-for-legal/regulatorio/comment-tracker.yaml` → todas as consultas públicas acompanhadas e seu status.
`~/.claude/plugins/config/claude-for-legal/regulatorio/CLAUDE.md` → responsável padrão pela decisão.

## Default view — consultas públicas abertas

```markdown
## Comment Period Tracker — [data]

### ⏰ Prazo em <14 dias

| ID | Norma / Consulta | Prazo | Dias restantes | Decisão | Responsável |
|---|---|---|---|---|---|
| CMT-001 | [nome] | [data] | [N] | Não decidida | [responsável] |

### 🟡 Aberta (>14 dias)

[mesma tabela]

### Decididas recentemente

| ID | Norma / Consulta | Decisão | Justificativa |
|---|---|---|---|
| CMT-002 | [nome] | Não manifestar | [motivo] |

---

**Total abertas:** [N]  **Não decididas com prazo <30 dias:** [N]
```

## Log a decision

```
/regulatorio:comments --decide CMT-001
Decisão: [manifestar / não manifestar / dispensada]
Justificativa: "[breve — ex.: 'Regra não se aplica ao nosso modelo' ou 'Manifestação na Seção 3 da AIR']"
```

Atualiza o tracker. Se decisão for "manifestar": ofereça lembrete de prazo interno
(prazo da consulta menos 5 dias úteis para revisão).

## Notifications

Na primeira detecção de uma consulta pública (populada pelo reg-feed-watcher): DM via Slack
para o responsável pela decisão se Slack MCP estiver configurado e `owner_slack` setado.

Lembrete 14 dias antes do prazo se decisão ainda "não decidida".
Lembrete 3 dias antes do prazo se ainda não decidida — urgência elevada.

## Consequential-action gate (manifestar em consulta pública / responder a ofício de agência)

**Antes de registrar decisão como "manifestar" — e sempre antes de produzir minuta de manifestação ou resposta a regulador para protocolo:** Leia `## Who's using this` em ~/.claude/plugins/config/claude-for-legal/regulatorio/CLAUDE.md. Se Role é **Não-advogado(a)**:

> Manifestar-se em consulta pública ou responder a regulador tem consequências jurídicas. É manifestação pública da posição da empresa, fica nos autos do processo administrativo de normatização ou no PAS, e posições aqui assumidas vinculam a empresa e podem ser usadas contra ela em procedimentos subsequentes. Você revisou isso com advogado(a) inscrito(a) na OAB? Se sim, prossiga. Se não, eis um briefing para levar:
>
> - A normatização ou consulta (regulador, número da consulta, prazo)
> - O que a manifestação proposta diz e em quais seções
> - Perguntas em aberto e o que está irresolvido
> - O que pode dar errado (admissões adversas, posições inconsistentes com manifestações pretéritas, coordenação com associações setoriais — ABBC, ABRANET, ABMI, etc.)
> - O que perguntar ao(à) advogado(a) (manifestar ou não; manifestar conjuntamente via associação; há posições que não devemos defender)
>
> Se você precisa de advogado(a): OAB seccional do seu estado oferece consulta a inscritos; CESA e IASP também têm canais de indicação.

Não registre decisão "manifestar" nem produza minuta-pronta-para-protocolo passando por este gate sem um "sim" explícito. Views de acompanhamento, lembretes de prazo, e decisões "não manifestar / dispensada" não exigem o gate.

---

## What this skill does not do

- Redige a manifestação. Isso é tarefa de advogado(a) em separado.
- Toma a decisão de manifestar. Ela registra a decisão; o(a) advogado(a) decide.
- Monitora atividade pós-manifestação. Uma vez protocolada, o trabalho desta skill termina —
  acompanhe a normatização via `/regulatorio:reg-feed-watcher`.

> A semântica de `gap_type` `comment-decision`, a regra de confirmação per-send no Slack, e o schema de comment-tracker.yaml ficam na skill de referência **gap-surfacer** — carregue-a antes de trabalhar substantivamente.
