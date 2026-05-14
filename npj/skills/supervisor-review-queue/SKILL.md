---
name: supervisor-review-queue
description: >
  Fila de revisão do(a) professor(a) — output do(a) estagiário(a) aguarda
  aprovação do(a) professor(a) advogado(a) OAB antes de ir ao(à) assistido(a)
  ou ao juízo. Ativa apenas se "fila formal de revisão" foi escolhida como
  modelo de supervisão no setup; senão dormente. Use quando professor(a) quer
  ver o que está em fila, aprovar, editar-e-aprovar, ou devolver item.
argument-hint: "[--aprovar ID | --devolver ID 'nota' | --editar ID]"
---

> **Adaptação BR (não oficial).** Skill para o(a) professor(a) supervisor(a) do NPJ — fila formal de revisão. Base normativa: EOAB (Lei 8.906/94) art. 3º §2º (estagiário(a) atua sob responsabilidade do(a) advogado(a) supervisor(a) inscrito(a) na OAB); Provimento CFOAB 11/2007; Resolução CNE/CES 5/2018; Provimento CFOAB 218/2023 (uso de IA na advocacia); CED-OAB. Petições assinadas pelo(a) estagiário(a) só são protocoladas em conjunto com o(a) advogado(a) inscrito(a) — daí a fila ser o ponto de controle obrigatório antes de ir ao juízo. Cartas substantivas ao(à) assistido(a), petições, ofícios, requerimentos administrativos, notificações extrajudiciais, manifestações em DEAM/CONARE/INSS exigem revisão. Sigilo: EOAB art. 7º XIX. Etiqueta `[RASCUNHO ASSISTIDO POR IA — exige análise do(a) estagiário(a) e revisão do(a) professor(a) advogado(a) OAB]` é removida apenas após aprovação. Tags: [JusBrasil], [STJ], [STF], [TST], [Planalto].

# /supervisor-review-queue

1. Check `~/.claude/plugins/config/claude-for-legal/npj/CLAUDE.md` → supervision style. If NOT "formal review queue": explain the clinic is set up for [flags/lighter-touch], no formal queue exists, and how to switch.
2. Use the workflow below.
3. Default: show what's waiting, by urgency, by student.
4. Actions: approve / edit-then-approve / return with note. All logged.

```
/npj:supervisor-review-queue
```

```
/npj:supervisor-review-queue --approve Q-003
```

```
/npj:supervisor-review-queue --return Q-004 "Check the service requirement — local rules changed"
```

---

# Supervisor Review Queue (Optional)

## Purpose

Some clinics want a formal gate: student drafts, professor reviews, output releases. Others find that too prescriptive — they supervise through case rounds and one-on-ones, not through a queue.

**This skill is only active if `~/.claude/plugins/config/claude-for-legal/npj/CLAUDE.md` → Supervision style is "formal review queue."** Otherwise it's dormant — the cold-start interview asks the professor which model they want, and this is one of three options.

Whether to use a formal review workflow is genuinely an open question for clinic adoption. It depends on student experience level, caseload, and how the professor already runs supervision. The professor decides at setup and can change it later.

## Load context

`~/.claude/plugins/config/claude-for-legal/npj/CLAUDE.md` → supervision style. If NOT "formal review queue": respond with "The clinic is set up for [flags/lighter-touch] supervision — there's no formal queue. [Professor] reviews through [the clinic's existing structure]. To switch to a formal queue, edit CLAUDE.md → Supervision style."

If formal queue IS enabled → read flag triggers and proceed.

## The queue

Lives at `references/review-queue.yaml`. Each entry:

```yaml
- id: Q-001
  type: "draft"  # intake | draft | memo | status | client-letter
  client: "[name or ID]"
  student: "[name]"
  submitted: [timestamp]
  flags:
    - rule: "Court filing"
      detail: "Eviction answer — always queued"
  content_path: "[path to the document]"
  status: "pending"  # pending | approved | edited-approved | returned
```

## Modes

### What's waiting

```markdown
## Review Queue — [date]

**Pending:** [N] | **Oldest:** [N] hours

### 🔴 Deadline-sensitive
| ID | Type | Client | Student | Why flagged | Waiting |
|---|---|---|---|---|---|

### Standard
[same table]

### By student
[Breakdown — spot patterns: who's queueing a lot, who might need a check-in]
```

### Review an item

Show full content + why it was flagged + student notes.

### Approve / edit-then-approve / return

- **Approve:** Status → approved, student notified, logged.
- **Edit then approve:** Professor edits inline, approved version is the edited one, original preserved in log so student sees the diff (teaching moment).
- **Return:** With a note. Student revises and resubmits.

## Logging

Every action logged. Approval logs are clinic records — they document that a licensed attorney, solicitor, barrister, or other authorised legal professional in the clinic's jurisdiction reviewed student work before it went to a client or court. That matters for the clinic's own compliance and for student evaluation.

## Teaching signal

The queue is also data. Pattern in returns ("Student X keeps missing the service requirement") is a coaching conversation. Pattern in edits ("Everyone's demand letters are too long") is a `/ramp` update for next semester.

## What this skill does NOT do

- **Run unless the professor chose it.** It's one of three supervision models, not the only one.
- **Auto-approve.** The professor approves.
- **Replace the clinic's existing supervision structure.** It's a gate for work product, not a substitute for case rounds, one-on-ones, or watching students in action.
