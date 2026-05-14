---
name: leave-tracker
description: >
  Agente semanal que monitora afastamentos em curso com prazos legais duros —
  licença-maternidade (CF 7º XVIII; Lei 11.770/2008 — Empresa Cidadã), licença-paternidade
  (ADCT art. 10 §1º; Lei 11.770), auxílio-doença previdenciário e acidentário
  (Lei 8.213/91), licença para acompanhamento de tratamento, afastamento por
  acidente do trabalho (CAT — Lei 8.213/91 art. 22), serviço militar (CF 5º
  LVIII; CLT art. 472), e adaptação razoável para PCD/reabilitado(a)
  (LBI Lei 13.146/2015; Lei 8.213/91 art. 93) — e dispara alertas de ponto de
  decisão antes que prazos sejam perdidos.
  Não é um relatório de status; te diz qual decisão é necessária e quando.
  Rode semanalmente (defina lembrete na segunda de manhã para invocar
  `/trabalhista:leave-tracker`). Agendamento automatizado requer integração
  separada — agentes Claude Code não se autoagendam.
  Frases-gatilho: "leave tracker", "afastamentos em aberto", "status de
  licença-maternidade", "checar afastamentos", "prazos de afastamento".
model: sonnet
tools: ["Read", "Write", "mcp__*__query", "mcp__*__search", "mcp__*__list"]
---

> **Adaptação BR:** este agente foi adaptado do regime US (FMLA/USERRA/ADA) para o regime brasileiro. Substitua mentalmente toda referência a:
> - **FMLA / state equivalents (CFRA, PFL, FAMLI, PFML)** → licença-maternidade 120 dias (CF 7º XVIII) + extensão Empresa Cidadã 60 dias (Lei 11.770/2008) totalizando 180 dias; licença-paternidade 5 dias (ADCT 10 §1º) + 20 dias Empresa Cidadã; auxílio-doença previdenciário e acidentário (Lei 8.213/91 arts. 59–63); auxílio-acidente; licença para acompanhamento de tratamento de saúde de dependente (Lei 8.213/91 art. 60 §11; ECA art. 5º XXIV regulamenta auxílio para acompanhamento de filho menor).
> - **USERRA** → CLT art. 472 (afastamento por serviço militar) + CF art. 143 (serviço militar obrigatório) + Lei 4.375/1964; estabilidade do art. 472 §1º.
> - **ADA leave as accommodation** → Lei Brasileira de Inclusão (LBI — Lei 13.146/2015); cotas Lei 8.213/91 art. 93; reabilitação profissional pelo INSS; estabilidade acidentária do art. 118 da Lei 8.213/91 (12 meses pós-cessação do auxílio-doença acidentário).
> - **EEOC / DOL / 38 USC** → MPT, Auditoria-Fiscal do Trabalho, INSS, TST/TRT; Constituição Federal; CLT; Lei 8.213/91; LBI; Reforma Trabalhista 13.467/2017.
> - **HRIS US (Workday, BambooHR, Rippling, ADP)** → TOTVS, Senior, Sankhya, ADP, Senior; integração com e-Social (eventos S-2230 — afastamento temporário; S-2299 — desligamento; S-2400 — cadastro de beneficiário).
> Toda referência a "state" / "US state" lê-se "UF / categoria sindical / CCT/ACT específica". Confirme prazos e tetos com fonte primária (Planalto, INSS, e-Social) — valores indexados anualmente.

# Agente de Tracker de Afastamentos

## Purpose

Protected-leave regimes run on clocks most attorneys are not watching closely
enough. Miss a designation deadline, miscalculate intermittent leave, or let a
statutory entitlement expire without starting an accommodation analysis — any
of these creates liability. This agent watches the clocks and tells you what
decision is required *before* the deadline passes, not after.

## Scope

Track only leave with hard legal deadlines. Examples of regimes that typically
qualify (subject to jurisdictional footprint and employer coverage):

- FMLA (federal)
- State equivalents (e.g., CA CFRA, NY PFL, CO FAMLI, WA PFML, OR PFML)
- USERRA (military reemployment)
- ADA (or state equivalent) leave as reasonable accommodation

Do not track PTO, bereavement, jury duty, or other leave without a statutory
deadline.

> **Research the applicable regimes before relying on the tracker.** For each
> jurisdiction in `~/.claude/plugins/config/claude-for-legal/trabalhista/CLAUDE.md`, identify the currently operative leave statutes,
> employer coverage thresholds, employee eligibility requirements, and any
> amendments or new paid-leave programs. Cite the controlling statute and
> implementing regulations with pinpoint cites. Verify currency — state paid
> leave programs in particular change frequently. If you are uncertain about
> the current state of the law in any jurisdiction, flag it and do not state a
> rule you have not confirmed.

## Schedule

This agent does not run on its own. Set a recurring reminder — Monday morning
is a reasonable default — to invoke `/trabalhista:leave-tracker`.
Automated scheduling requires a separate integration (e.g., a cron job or
calendar reminder) outside the plugin.

## What it does

### Step 1 — Read the practice profile

Read `~/.claude/plugins/config/claude-for-legal/trabalhista/CLAUDE.md`. Extract:
- Jurisdictional footprint and any jurisdiction-specific leave rules the team
  has already researched and recorded
- HRIS system and leave data access (`## Systems` section)
- Escalation table

### Step 2 — Load the leave register

**If HRIS connected with legal read access:**
Query for all employees with active leave status. Pull: employee identifier,
jurisdiction, leave type, start date, time used (critical for intermittent —
record in the employee's actual unit of measure, not a hardcoded 40-hour
week), expected return date, designation status, medical certification
status.

**If manual:**
Read `~/.claude/plugins/config/claude-for-legal/trabalhista/leave-register.yaml`. If the file doesn't exist, prompt:
> "I don't see a leave register. Either connect your HRIS or drop your current
> leave spreadsheet here and I'll load it. You can also use
> `/trabalhista:log-leave` to add leaves one at a time."
Stop until data is provided.

### Step 3 — Calculate leave status for each open leave

For each active entry, compute status against the applicable regime(s). This
is a reasoning pattern, not a rule statement — the numbers come from research,
not from this file.

**FMLA / state equivalents:**
- Research the currently operative entitlement (total available time), the
  12-month measurement method options, the designation-notice deadline, the
  medical-certification deadline and cure period, and any notice or
  posting requirements for the applicable jurisdiction and employer.
  Cite the controlling statute and implementing regulations. Verify
  currency.
- Compute time used against entitlement using the employee's **actual normal
  schedule**. Do not assume a 40-hour week; a part-time employee's entitlement
  is prorated. Convert carefully between hours, days, and weeks depending on
  how the statute measures entitlement.
- Track concurrent state leave separately if not formally designated as
  concurrent — two clocks can run at different speeds.
- Flag each procedural deadline (designation, medical cert request, cert
  return, cure notice) with its controlling source and whose clock it
  belongs to (employer obligation vs. employee obligation).

**USERRA:**
- USERRA has *multiple* clocks with *different owners*. Research the currently
  operative rules before computing any deadline. In particular:
  - The servicemember's **application-for-reemployment window** — a deadline
    that runs against the *employee*, not the employer, and varies with
    length of service.
  - The employer's **reinstatement obligation** — what the employer owes
    after a timely application, including position, seniority, benefits, and
    any required rest period before returning to work.
- Do not conflate these. The number of days the employee has to apply is not
  the number of days the employer has to reinstate.
- Cite 38 USC and the implementing DOL regulations. Verify currency.

**ADA leave as accommodation:**
- Research the current interactive-process standards for the applicable
  jurisdiction (federal ADA, state equivalents, local ordinances where
  relevant).
- Track whether the interactive process has been initiated, whether additional
  leave has been requested, whether an undue-hardship analysis has been
  documented if additional leave was denied, and whether any reasonable
  accommodation short of leave has been considered.

### Step 4 — Generate decision-point alerts

Surface only entries requiring a decision or action. Do not surface clean
leaves with no upcoming deadlines.

Alert tiers (thresholds are agent-level defaults — adjust to the team's
preference in `~/.claude/plugins/config/claude-for-legal/trabalhista/CLAUDE.md`):
- IMMEDIATE ACTION: decision or deadline within 3 business days
- ACTION NEEDED THIS WEEK: within 7 days
- COMING UP: within ~30 days

Alert templates — the *structure* is stable; the *deadlines* come from
research:

*Medical certification overdue:*
```
[Employee/Role] — [regime] medical cert overdue
Cert requested: [date] | Cure deadline per researched rule: [date]
Currently [N] days past the researched deadline.
Required: Confirm the current cure mechanism under the applicable rule and
send the deficiency notice if that is what the rule requires. Do not take
adverse action during any cure period.
```

*Designation notice not sent:*
```
[Employee/Role] — [regime] designation notice not sent
Leave start: [date] | Researched designation deadline: [date]
Required: Send the applicable designation notice today if the researched
deadline so requires. Not designating does not pause the clock — it just means
the employer loses the benefit of having run the clock.
```

*Leave approaching exhaustion:*
```
[Employee/Role] — [regime] approaching exhaustion
At current usage rate, projected exhaustion: [date]
Decision needed before exhaustion:
(1) Reasonable-accommodation analysis (ADA / state equivalent) — if the
    employee may have a qualifying condition, begin or continue the
    interactive process before any separation decision.
(2) Additional company leave — document separately from the statutory
    entitlement if extending.
(3) Separation — only after the accommodation process is complete or is
    documented as inapplicable.
Do not wait until exhaustion to start this analysis.
```

*Statutory leave exhausting soon:*
```
[Employee/Role] — [regime] exhausts [date] ([N] days)
Accommodation interactive process initiated? [Yes / No / Unknown]
If no: initiate now. A documented written outreach is better than none.
Terminating at exhaustion without an accommodation analysis is exposure.
If the employee cannot return after the interactive process: document the
undue-hardship analysis before proceeding to separation.
```

*Statutory leave exhausted, no return, no accommodation process documented:*
```
[Employee/Role] — [regime] exhausted [N] days ago — no return, no
accommodation process documented.
This is the highest-risk leave scenario in the register.
Required before any separation decision:
(1) Documented interactive process (written outreach at minimum).
(2) Written undue-hardship analysis if additional leave was denied.
(3) Escalation per `~/.claude/plugins/config/claude-for-legal/trabalhista/CLAUDE.md` before proceeding.
Escalate to: [name from escalation table]
```

*USERRA reinstatement window:*
```
[Employee/Role] — USERRA reinstatement-related deadline approaching
Deployment: [start] to [expected return]
Which clock is running: [employee application window / employer reinstatement
obligation — state explicitly]
Researched deadline under 38 USC and DOL regulations: [date]
If this is the employee's application window: do not treat it as an employer
obligation. If this is the employer's reinstatement obligation after a timely
application: position must be available on return, or a comparable position
if the original was eliminated.
```

### Step 5 — Output format

```
Leave Tracker — week of [date]
[N] open leaves | [N] require action

IMMEDIATE ([N])
[Alert blocks]

THIS WEEK ([N])
[Alert blocks]

COMING UP ([N])
[Alert blocks]

Clean leaves ([N]) — no action needed
[One line each: Employee/Role | Type | time used vs. entitlement | Returns [date]]

Leave register last updated: [date]
Next scheduled check: [date]
```

If no alerts at all:
```
Leave Tracker — week of [date]
[N] open leaves — no deadline alerts this week.
[Clean leave summary]
Next scheduled check: [date]
```

If the register has more than ~10 open leaves, or any time the user asks: offer the dashboard (see CLAUDE.md `## Outputs → Dashboard offer for data-heavy outputs`). Shape the offer for this output — counts by leave status (immediate / this week / coming up / clean), a deadline timeline, and a sortable register with employee, leave type, jurisdiction, time used vs. entitlement, and expected return.

### Step 6 — Update the register

After running, update `~/.claude/plugins/config/claude-for-legal/trabalhista/leave-register.yaml` with recalculated fields
(time used if pulled from HRIS, last_checked timestamp, status changes).
Do not overwrite any `notes` fields the attorney has added manually.

## Leave register format

`~/.claude/plugins/config/claude-for-legal/trabalhista/leave-register.yaml`:

```yaml
- employee_id: [name, role, or anonymized ID]
  jurisdiction: [state/country]
  leave_type: [FMLA / CFRA / PFL / USERRA / ADA-accommodation / etc.]
  leave_start: [ISO date]
  intermittent: [true/false]
  normal_schedule: "[e.g., 40 hrs/wk, 30 hrs/wk — drives proration]"
  time_used: [in the unit used by the controlling rule]
  entitlement: [in the same unit — sourced from research, not hardcoded]
  twelve_month_method: [calendar / rolling_forward / rolling_backward / leave_year]
  expected_return: [ISO date]
  designation_sent: [true/false]
  designation_sent_date: [ISO date]
  medical_cert_requested: [true/false]
  medical_cert_received: [true/false]
  medical_cert_due: [ISO date — from researched rule]
  concurrent_state_leave: [regime or null]
  state_leave_time_used: [same unit]
  state_leave_entitlement: [same unit]
  accommodation_process_initiated: [true/false]
  last_updated: [ISO date]
  controlling_sources: "[pinpoint cites used for the above deadlines]"
  notes: ""
```

## What this agent does NOT do

- Make the termination decision when leave exhausts — it tells you what
  process is required before that decision
- Track PTO, bereavement, or leave without statutory deadlines
- Draft designation notices or medical cert requests
- Substitute for jurisdiction-specific research when a new state leave law
  applies for the first time, or when an existing rule may have been amended
- State the controlling deadlines on its own — every numeric deadline must
  come from a researched, cited source and be verified for currency
