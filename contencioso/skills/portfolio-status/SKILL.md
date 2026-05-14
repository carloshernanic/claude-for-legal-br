---
name: portfolio-status
description: Consolida a carteira a partir de _log.yaml — distribuição de risco, próximos prazos, matérias paradas, totais de materialidade, distribuição por fase processual, e anomalias sinalizadas. Use quando o(a) usuário(a) perguntar "como estamos", "quantas matérias abertas", ou quiser rollup ou status da carteira de matérias ativas.
argument-hint: "[--all | --risk=high | --stale]"
---

# /portfolio-status

> **Adaptação BR não oficial.** "Portfolio" = **carteira de matérias** in-house ou de banca. Fases processuais BR: pré-litigiosa, inicial protocolada (CPC 319), citação (CPC 246), contestação (CPC 335), saneamento (CPC 357), instrução (CPC 358), sentença (CPC 489), recurso (apelação CPC 1009; agravo CPC 1015; embargos CPC 1022; especial/extraordinário CPC 1029), execução / cumprimento de sentença (CPC 513-538). Materialidade: provisão contábil (CPC 25 — perda **provável e estimável**); divulgação CVM via Resolução CVM 80 (fato relevante) e Resolução CVM 44 (companhias abertas). Prazos típicos: contestação 15 dias úteis (CPC 335); recursos 15 dias úteis (CPC 1003); JT (CLT) muitas vezes em dias corridos — verificar caso a caso. Materialidade trabalhista calculada por reclamada e por tema (verbas rescisórias, horas extras, equiparação, dano moral) — TST. Andamento processual extraído de PJe / e-SAJ / e-Proc / Projudi / DataJud. "Materiality totals" em R$ — não USD.

1. Load `~/.claude/plugins/config/claude-for-legal/contencioso/CLAUDE.md` → risk calibration (defines how to read the `risk:` field).
2. Follow the workflow and reference below.
3. Parse `~/.claude/plugins/config/claude-for-legal/contencioso/matters/_log.yaml`. Filter closed matters by default (include with `--all`).
4. Produce rollup: risk distribution, deadlines in next 14/30/60 days, matters with no update in >30 days, materiality totals, stage distribution.
5. Flag anomalies — everything marked critical, overdue next_deadline, matters without outside counsel assigned where risk is medium or high.

---

# Portfolio Status

## Purpose

One read that answers: what do I own right now, what needs attention, and what's slipping? Output is scannable — designed for a counsel who has three minutes before their next call.

## Load context

- `~/.claude/plugins/config/claude-for-legal/contencioso/matters/_log.yaml` — source of truth
- `~/.claude/plugins/config/claude-for-legal/contencioso/CLAUDE.md` — risk calibration (to interpret risk/materiality fields correctly)

## Flags & filters

Default: active matters only (exclude `status: closed`).

Flags:
- `--all` — include closed
- `--risk=high` (or `critical` / `medium` / `low`) — filter by risk band
- `--stale` — only matters with `last_updated` > 30 days
- `--type=employment` — filter by matter type
- `--owner=[name]` — filter by business/HR/comms owner

## The rollup

```markdown
[WORK-PRODUCT HEADER — per plugin config ## Outputs — differs by role; see `## Who's using this`]

# Portfolio Status — [today]

**Active matters:** [N]
**Closed (ytd):** [N] *(shown only with --all)*

---

## By risk

| Risk | Count | Matters |
|---|---|---|
| Critical | [N] | [slugs] |
| High | [N] | [slugs] |
| Medium | [N] | [count only — expand with `--risk=medium`] |
| Low | [N] | [count only] |

## Upcoming deadlines

| Within | Matters |
|---|---|
| 14 days | [slug — deadline — brief] |
| 15–30 days | [...] |
| 31–60 days | [...] |

*Overdue `next_deadline` flagged separately below.*

## Materiality

| Category | Count | Total exposure (midpoint) |
|---|---|---|
| Reserved | [N] | [$X] |
| Disclosed | [N] | [$X] |
| Monitored | [N] | — |
| None | [N] | — |

## By stage

[table: pleadings / discovery / dispositive motions / trial prep / settlement / appeal]

---

## ⚠️ Anomalies & flags

- **Overdue deadlines:** [list slugs where next_deadline has passed]
- **Stale (>30d no update):** [list]
- **Conflicts unresolved:** [list slugs with `conflicts.status in [pending, not-run]`]
- **Conflicts bypassed (override active):** [list slugs where `conflicts.override.by` is populated — permanent flag until manually cleared]
- **High/critical risk without outside counsel:** [list]
- **Reserved without last_updated in >60d:** [list] — reserve recalibration likely overdue
- **Hold not issued on active litigation:** [list]
- **Missing fields:** [slug → field]

---

## Closing advice

[One or two sentences on what to look at first, if anything stands out. Not boilerplate — only if something truly stands out.]
```

## Anomaly rules

These are the checks that make the skill useful rather than decorative:

1. **Overdue deadline:** `next_deadline < today` and `status != closed`
2. **Stale:** `last_updated < today - 30d` and `status != closed`
3. **Conflicts unresolved:** `conflicts.status in [pending, not-run]` and `status != closed`
3b. **Conflicts override active:** `conflicts.override.by != null` (never auto-clears)
4. **High-risk uncovered:** `risk in [high, critical]` and `outside_counsel.firm == null`
5. **Stale reserve:** `materiality == reserved` and `last_updated < today - 60d`
6. **Hold gap:** `status in [threatened, active, discovery, trial, appeal]` and `legal_hold.issued == false` — preservation duty attaches at reasonable anticipation, so `threatened` matters are in scope.
7. **Missing fields:** any required field null — `risk`, `materiality`, `status`, `opened`, `conflicts.status`

## Close with the next-steps decision tree

End with the next-steps decision tree per CLAUDE.md `## Outputs`. Customize the options to what this skill just produced — the five default branches (draft the X, escalate, get more facts, watch and wait, something else) are a starting point, not a lock-in. The tree is the output; the lawyer picks.

If the portfolio has more than ~10 matters, or any time the user asks: offer the dashboard (see CLAUDE.md `## Outputs → Dashboard offer for data-heavy outputs`). Shape the offer for this output — counts by risk tier, a timeline of upcoming deadlines, and a sortable matter ledger with status, conflicts check, and last-touched date.

## What this skill does not do

- Make decisions. It surfaces what needs attention; the user decides priority.
- Pretend precision it doesn't have. Exposure midpoints are rough and should be labeled so.
- Replace a real MMS. This is a working-memory rollup, not a system of record.
