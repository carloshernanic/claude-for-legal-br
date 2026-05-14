---
name: status
description: >
  Resumo de status do caso por audiência — voltado ao(à) assistido(a)
  (linguagem simples), interno (para o(a) professor(a)) ou pronto para o
  juízo (cabeçalho formal conforme regimento do tribunal). Mesmos fatos,
  enquadramento e profundidade diferentes. Use quando estagiário(a) precisa
  atualizar o(a) assistido(a), informar o(a) professor(a) ou preparar
  petição de informação processual.
argument-hint: "[assistido | interno | tribunal]"
---

> **Adaptação BR (não oficial).** Skill para NPJ. Três modos:
> 1. **assistido(a)** — linguagem simples (ensino fundamental II), sem juridiquês ("outrossim", "destarte", "vossa excelência" banidos), elementos obrigatórios (o que aconteceu, o que vem, o que fazer, como falar com o NPJ).
> 2. **interno** — para o(a) professor(a) advogado(a) OAB, com tags inline `[verificar]` / `[revisar]` / `[conhecimento do modelo — verificar]`; etiqueta `[RASCUNHO ASSISTIDO POR IA]`.
> 3. **tribunal** — cabeçalho formal "Excelentíssimo(a) Senhor(a) Juiz(a) de Direito da [vara] da [comarca]"; assinatura sob responsabilidade do(a) professor(a) advogado(a) OAB (estagiário(a) subscreve em conjunto, EOAB 3º §2º); formatação conforme regimento do tribunal (PJe/e-SAJ/e-Proc/Projudi). Considere recesso CPC 220 (20/dez a 20/jan) ao falar de prazos. Sigilo: EOAB art. 7º XIX. Segredo de justiça: CPC 189, ECA 143, Maria da Penha, Lei 9.474/97 art. 23. Encaminhamentos: Defensoria (LC 80/94), Procon, CRAS/CREAS, DEAM, Conselho Tutelar, MP, CONARE.

# /status

1. Load `~/.claude/plugins/config/claude-for-legal/npj/CLAUDE.md` → supervision style, plain-language standards, jurisdiction.
2. Use the workflow below. Read case notes.
3. Generate for the specified audience:
   - `client` — plain language, what happened/next/you do/reach us
   - `internal` — procedural posture, done since last check-in, upcoming, needs professor input, student's assessment
   - `court` — formal status report in caption format per local rules
4. Supervision routing per audience (client-facing and court-ready usually flag).

```
/npj:status client
```

```
/npj:status internal
```

```
/npj:status court
```

---

# Status: Audience-Aware Case Summaries

## Purpose

Clinics generate enormous numbers of status updates — to clients, to professors, to co-counsel, to courts. Same case, same facts, completely different documents. This skill takes the case notes and produces the right summary for the right reader.

## Load context

`~/.claude/plugins/config/claude-for-legal/npj/CLAUDE.md` → supervision style, plain-language standards (for client-facing), jurisdiction.
Case notes for facts.

## Audience modes

### Client-facing

**Reader:** The client. Probably stressed. Possibly unfamiliar with legal process. Reading level per `~/.claude/plugins/config/claude-for-legal/npj/CLAUDE.md` plain-language standards (default 6th grade).

**Include:**
- What's happened since they last heard from the clinic
- What's happening next and when
- What (if anything) they need to do
- How to reach the clinic

**Don't include:**
- Legal analysis (they don't need to know the IRAC)
- Weaknesses in their case (unless it's time to have that conversation — and that's a call for the professor, not a status update)
- Jargon

*Review label for the student (not for the client — strip before sending):*
`[AI-ASSISTED DRAFT — requires student review and supervision step per plugin config]`

Check your jurisdiction's student practice rule for required estudante-direito sign-off language; some jurisdictions require specific forms.

```markdown
Dear [Client],

I wanted to update you on your case.

**What's happened:** [Plain English. "We filed your answer with the court on
[date]" not "The responsive pleading was submitted."]

**What's next:** [What and when. "The court scheduled a hearing for [date] at
[time]. You need to be there." Or: "We're waiting for the landlord's lawyer
to respond. That could take a few weeks."]

**What you need to do:** [Specific and clear. Or: "Nothing right now — we'll
let you know when we need something from you."]

**How to reach us:** [Clinic phone, hours, student name]

[Student name]
Law Student, Certified Legal Intern
Under the supervision of [Supervising Attorney]
[Clinic name]
```

**Before sending:** sending a client status update is a consequential action. The gate is the supervision workflow in `## Supervision style` in `~/.claude/plugins/config/claude-for-legal/npj/CLAUDE.md`, reinforced by the Part 0 role check confirming a licensed supervising attorney owns the setup. Confirm the draft has been reviewed per the supervision protocol (queue / flag / lighter-touch) and all internal review labels (`[AI-ASSISTED DRAFT]`, `[VERIFY]`, etc.) have been removed from the client-facing copy.

### Internal (for the professor)

**Reader:** The supervising professor. Knows the law. Wants to know where the case stands and what the student needs from them.

**Include:**
- Procedural status (where in the life of the case)
- What's been done since last check-in
- What's coming up (deadlines, hearings)
- Issues needing professor input
- Student's assessment (how it's going, concerns)

```markdown
# Status: [Client] — [Matter] — [date]

**Student:** [name] | **Procedural posture:** [pre-filing / answer filed /
discovery / motion pending / etc.]

## Since last check-in

- [What's been done]

## Upcoming

| Date | What | Action needed by |
|---|---|---|
| [date] | [deadline/hearing] | [date] |

## Needs professor input

- [Question or decision point — specific]

## Student's assessment

[How it's going. Strengths, concerns, strategic questions. This is where the
student's thinking shows.]

---
[AI-ASSISTED DRAFT — student should revise the assessment section especially;
that's your thinking, not a summary of notes]
```

### Court-ready

**Reader:** A judge or clerk. Formal. Specific to what the court needs (often a status report ordered by the court, or a statement in advance of a status conference).

**Include:**
- Procedural history (briefly)
- Current status of discovery/motions/settlement
- What's outstanding
- Proposed next steps or scheduling

**Format:** Per local rules. Caption, signature block, certificate of service if filed.

```markdown
═══════════════════════════════════════════════════════════════════════
  AI-ASSISTED DRAFT — requires student analysis and attorney review
  Court filings ALWAYS require professor review before filing
═══════════════════════════════════════════════════════════════════════

[Caption per jurisdiction — VERIFY against current local rules]

STATUS REPORT

[Party] respectfully submits this status report pursuant to [the court's
order of [date] / local rule [X] / in advance of the status conference
scheduled for [date]].

1. Procedural history: [brief]

2. Current status: [discovery status / motion status / settlement status]

3. Outstanding matters: [what's pending]

4. Proposed next steps: [scheduling, if the court wants input]

[Signature block — student attorney under supervision of [Professor]]

[Certificate of service if filing]

---

[VERIFY: caption format, local status report requirements, service
requirements — per current [Court] rules]
```

## Supervision routing

Per `~/.claude/plugins/config/claude-for-legal/npj/CLAUDE.md`:
- Client-facing → usually a flag trigger (client communication)
- Internal → no flag (it's going to the professor anyway)
- Court-ready → always flagged if formal queue enabled (court filings)

## What this skill does NOT do

- **Decide what to tell the client.** Especially on bad news or case weaknesses — that's a conversation for the student and professor to have, then the student to have with the client. Status updates are status, not strategic advice.
- **File anything with a court.** Drafts the document; professor reviews; filing per clinic procedure.
- **Replace the student's assessment in internal status.** The "student's assessment" section is the student's thinking — the draft can scaffold it but can't write it.

## Close with the next-steps decision tree

End with the next-steps decision tree per CLAUDE.md `## Outputs`. Customize the options to what this skill just produced — the five default branches (draft the X, escalate, get more facts, watch and wait, something else) are a starting point, not a lock-in. The tree is the output; the lawyer picks.

