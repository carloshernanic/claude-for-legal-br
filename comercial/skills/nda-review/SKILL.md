---
name: nda-review
description: >
  Reference: fast triage of inbound NDAs into GREEN / YELLOW / RED so the team only
  spends lawyer time on the ones that need it. Built for sales and BD to self-serve
  before pinging legal. Loaded by /comercial:review when an NDA is detected.
user-invocable: false
---

# NDA Review

**Adaptação BR.** Plugin adaptado ao Brasil. NDA aqui significa contrato/termo de confidencialidade (incluindo NDA mútuo) regido pelo CC. Cláusula penal por violação rege-se pelo CC arts. 408–416 (limite: valor da obrigação principal). Lei aplicável e foro seguem CPC art. 63 (eleição válida em B2B; restrita em adesão §3º) e LINDB art. 9º. Cláusula compromissória rege-se pela Lei 9.307/1996. **Em consumo (CDC art. 51, VII), cláusula de arbitragem compulsória é nula.** Antes de qualquer triagem: identifique regime B2B (CC) ou consumo (CDC).

## Contexto de matéria

**Contexto de matéria.** Cheque `## Workspaces de matéria` no CLAUDE.md de nível de prática. Se `Habilitado` é `✗` (padrão para in-house), pule este parágrafo — skills usam contexto de nível de prática e a maquinaria de matéria fica invisível. Se habilitado e não houver matéria ativa, pergunte: "Para qual matéria? Rode `/comercial:matter-workspace switch <slug>` ou diga `nível de prática`." Carregue o `matter.md` da matéria ativa. Escreva saídas em `~/.claude/plugins/config/claude-for-legal/comercial/matters/<slug>/`. Nunca leia matéria alheia salvo se `Contexto cross-matter` estiver `ligado`.

---

## Destination check

Antes de produzir saída, cheque o destino. Se o usuário nomeou destino (canal, lista, contraparte, "todo mundo"), pergunte se está dentro do círculo de sigilo profissional. Canais públicos, listas company-wide, contraparte/advogado(a) da outra parte, fornecedores e clientes (para material interno) quebram a proteção do EOAB art. 7º, XIX. Quando o destino parece fora do círculo, flague e ofereça (a) versão sigilosa só para o jurídico, (b) versão sanitizada para o canal mais amplo, ou (c) ambas — não aplique cabeçalho de sigilo em silêncio e ajude a colar onde o cabeçalho não protege. Veja `## Guardrails compartilhadas → Checagem de destino` no CLAUDE.md.

## Purpose

Most inbound NDAs are fine. A few have landmines. This skill sorts them in under a minute so legal only reads the ones that matter.

**The goal:** a GREEN NDA should need nothing more than a signature. A YELLOW needs a lawyer's eyes on one or two specific things. A RED stops before anyone wastes time.

## Load the playbook first

**Which side?** Before applying the playbook, determine which side the company is on for this NDA. Usually obvious from the context: if the counterparty is a vendor or partner evaluating your product, you're sales-side; if you're evaluating theirs, you're purchasing-side. Mutual NDAs still have a side — whose paper is it, and which direction is the evaluation running. If it's not obvious, ask. Read the matching playbook section (`### Sales-side playbook` or `### Purchasing-side playbook`) from the config. Note which side in the output so the reviewer knows which playbook was applied. If the matching side is `[Not configured]`, stop and tell the user to run `/comercial:cold-start-interview --side <side>` before this triage can proceed.

**Before triaging anything, read `~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md` → `## Playbook` → the matching side → `NDA triage positions`.** That section is the source of truth for what makes an NDA GREEN, YELLOW, or RED for *this* team on *this* side. This skill does not ship with default positions on NDA terms — the law, the market, and each team's risk tolerance vary too much for hardcoded defaults to be safe.

If `~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md` doesn't have an `NDA triage positions` section yet, or it's silent on a term that comes up in the NDA you're reviewing, ask the user:

> Your playbook doesn't cover [term — e.g., "residuals clauses," "survival period," "one-way NDAs where you're the receiver"]. What's your default position — when should this be GREEN, when YELLOW, when RED? I'll add it to `~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md` so the next review is consistent.

Then record the answer in `~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md` and proceed with the triage using the new position.

## Scope check

**Before reviewing NDA-specific provisions, check whether the document is doing more than its name suggests.** Mutual commercial NDAs can hide: standstills, licensing grants, exclusivity, non-solicits, non-competes, IP assignments, right of first refusal, most-favored-nation clauses, and arbitration/jurisdiction clauses that govern far more than confidentiality disputes.

If the NDA contains obligations beyond confidentiality: **auto-YELLOW regardless of the NDA-term analysis.** Flag the non-NDA provisions:

> This document is labeled an NDA but contains [standstill / license grant / non-solicit / exclusivity / IP assignment / ROFR / MFN / broad arbitration]. It's more than an NDA. Route for attorney review.

Do not silently push a document labeled "NDA" through NDA triage when the substantive obligations are a services agreement, a term sheet, or a covenant package in NDA clothing.

## The triage

Classify the NDA into one of three buckets by applying the positions from `~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md`. The bucket definitions below are stable; the *criteria* that fill each bucket come from the playbook.

### GREEN — route to signature

The NDA satisfies every position in the team's playbook, and no term triggers a RED flag per the playbook. Examples of checks the playbook typically covers: mutuality, term length, survival period, carveouts, governing law, restrictive covenants, fee-shifting. Confirm each one against `~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md` before calling GREEN.

**GREEN requires attorney-reviewed playbook positions.** GREEN is the only path to signature without lawyer review. It cannot be issued against default or absent positions. Before issuing GREEN, check: does the practice profile have an attorney-reviewed `## NDA triage positions` section? If not:

> I can't issue GREEN without attorney-reviewed NDA positions in your practice profile. Run `/comercial:cold-start-interview --full` with your commercial counsel to set them, or route this NDA for attorney review. Issuing GREEN against defaults means a non-lawyer set the positions the next non-lawyer relies on.

Do not route to signature on defaults. YELLOW is the right call when positions are missing — it surfaces the NDA to a human who can decide.

**Output:**

Prepend the work-product header from `~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md` `## Outputs` (it differs by user role — see `## Who's using this`).

```markdown
[WORK-PRODUCT HEADER — per plugin config ## Outputs]

## NDA Triage: [Counterparty]

GREEN — route to signature

### Executive Summary

No red flags identified under the playbook. Route for signature per standard process.

| Check | Status | Playbook reference |
|---|---|---|
| [Each playbook check] | [pass/fail] | [`~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md` section] |

**Next step:** [Submit to [CLM] standard NDA workflow | Send to [approver from `~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md`] for signature]
```

**Before proceeding past GREEN to signature:** Read `## Who's using this` in `~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md`. If the Role is Non-lawyer:

> This step has legal consequences (countersigning an NDA binds the company). Have you reviewed this with an attorney? If yes, proceed. If no, here's a brief to bring to them:
>
> [Generate a 1-page summary: counterparty, NDA direction (mutual / one-way), the playbook checks run, anything the playbook didn't cover, what could go wrong if signed as-is, and the three things to ask the attorney.]
>
> Se precisar de advogado(a): consulte a OAB da sua seccional (oab.org.br → seccional → serviços ao público) para indicação. Também é opção a Defensoria Pública, núcleos de prática jurídica (universidades), ou advogados(as) dativos(as) da seccional.

Do not proceed past this gate without an explicit yes.

### YELLOW — needs a lawyer's eyes on specific items

One or more terms deviate from the playbook but aren't categorical deal-breakers, OR a term appears that the playbook doesn't address. Surface each item individually so the approver can make the call.

**Output:**

Prepend the work-product header from `~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md` `## Outputs` (it differs by user role — see `## Who's using this`).

```markdown
[WORK-PRODUCT HEADER — per plugin config ## Outputs]

## NDA Triage: [Counterparty]

YELLOW — flag for [approver name from `~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md`]

### Executive Summary

- [One-line actionable edit, e.g. "Strike non-solicit clause (Section 6)"]
- [One-line actionable edit]

### Flagged items

**1. [Issue]** — Section [X]
   What: [one line]
   Why flagged: [one line — which playbook position this hits, or "playbook is silent on this"]
   **Legal risk:** [🔴/🟠/🟡/🟢] | **Business friction:** [🔴 Blocks deals / 🟠 Slows deals / 🟡 Confuses customers / 🟢 Invisible]
   Likely resolution: [accept / push back on X / depends on deal context]

[repeat for each flag]

### Everything else

| Check | Status | Playbook reference |
|---|---|---|
| [playbook checks that passed] | pass | [`~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md` section] |

**Next step:** Ask [approver] about the flagged items, then route to signature if they're okay with it.
```

### RED — stop, talk to legal first

The NDA hits a position on the playbook's "never accept" list, or the structure of the agreement is incompatible with the team's standard posture (e.g., a one-way NDA where the team's playbook requires mutual; a perpetual term where the playbook caps at a finite period; governing law on the "never" list).

**Output:**

Prepend the work-product header from `~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md` `## Outputs` (it differs by user role — see `## Who's using this`).

```markdown
[WORK-PRODUCT HEADER — per plugin config ## Outputs]

## NDA Triage: [Counterparty]

RED — do not submit, talk to legal first

### Executive Summary

- [One-line actionable edit, e.g. "Section 4 — route to Legal for review"]
- [One-line actionable edit]

### Critical issues

**1. [Issue]** — Section [X]
   > "[exact quote]"
   Why this is a problem: [specific risk; cite the playbook position it violates]
   **Legal risk:** [🔴/🟠/🟡/🟢] | **Business friction:** [🔴 Blocks deals / 🟠 Slows deals / 🟡 Confuses customers / 🟢 Invisible]
   Recommended response: [use our paper instead | push back with specific language | walk]

**Next step:** Send this triage to [GC or named escalation person from `~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md`]. Do not send to [CLM or approvals workflow]. Do not tell the counterparty we'll sign.
```

## Redline granularity

**Edit at the smallest possible granularity.** A redline is a negotiation artifact, not a rewrite. Wholesale clause replacement signals "we threw out your drafting" — it's aggressive, it forces the counterparty to re-read the whole clause, and it discards the parts of their drafting that were fine. Surgical redlines — strike a word, insert a phrase, restructure a subclause — signal "we have specific asks" and are faster to read, understand, and accept.

Default to the smallest edit that achieves the playbook position:
- Replace a **word** before a phrase. ("twelve (12)" → "twenty-four (24)")
- Replace a **phrase** before a sentence. ("paid by the Buyer" → "paid and payable by the Buyer")
- Restructure a **subclause** before replacing the sentence. (Add "(a)" and "(b)" to split a compound condition.)
- Replace a **sentence** before replacing the clause.
- Only replace a **whole clause** when the counterparty's version is so far from your position that surgical edits would be harder to read than a fresh draft — and when you do, say so in the transmittal: "We've replaced §8.2 rather than marking it up because the changes were extensive. Happy to walk you through the delta."

When in doubt, smaller. A client who receives a surgical redline trusts that you read carefully. A client who receives a wholesale replacement wonders whether you read at all.

## Premissa jurisdicional

Esta triagem aplica as posições de lei aplicável e cláusulas restritivas registradas em `~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md`. Regras (validade de não-concorrência e não-aliciamento, sucumbência, eleição de foro/lei) variam materialmente. No Brasil: não-concorrência pós-contratual exige limite temporal, territorial e indenização (CLT art. 482 §; jurisprudência TST consolidada para empregado; em B2B vale CC art. 421/422, limite razoabilidade). Sucumbência segue CPC arts. 82–85. Eleição de foro vale em B2B (CPC art. 63) mas é restrita em adesão (§3º) e nula em consumo (CDC art. 51, IV). Lei aplicável estrangeira em contrato 100% interno tem eficácia limitada (LINDB art. 9º). Se o NDA envolve jurisdição fora da postura configurada, flague.

## Output rules

**Complexity filter:** If addressing an issue would require drafting new
language, restructuring a clause, or inserting substantive new
provisions — do not attempt it. Instead write:
"Section [X] — route to Legal for review."
Only include simple, mechanical actions in the Executive Summary
(strike, delete, replace a word or phrase).

**Clean NDA rule:** If the NDA passes all checks with no flags, the Executive Summary
should say only: "No red flags identified. Route for signature per
standard process."

Do not produce a lengthy report for a clean NDA.

## Detailed check reference

For each check below, the bucket (GREEN/YELLOW/RED) is determined by `~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md`. This skill lists the *categories* to check; it does not hardcode thresholds.

### Mutuality

Is the NDA mutual or one-way? Apply the team's position from `~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md`. If the playbook doesn't address one-way NDAs for this context, run the one-way questionnaire below and surface the result for a human.

**One-way NDA questionnaire**

When the NDA is unilateral (one party discloses, the other only receives), do not immediately flag RED or exit. Ask:

> A one-way NDA is appropriate in some situations. Before flagging this,
> let me ask a few quick questions:
>
> 1. In this relationship, are you the only party disclosing confidential
>    information? (i.e., the other side shares nothing back)
> 2. Is this for a limited, specific disclosure — for example, sharing
>    your technology with a vendor who will work on it, but not sharing
>    theirs with you?
> 3. Is this related to M&A, employment, or investment? (If yes, stop —
>    this skill is for commercial MNDAs only. Route to Legal.)

Use the answers plus the `~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md` position to decide GREEN/YELLOW/RED. If `~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md` doesn't take a position on this fact pattern, flag YELLOW and surface the questionnaire answers for the approver.

### Definition of Confidential Information

Check scope (marked-only vs. everything-disclosed), marking requirements, and oral-disclosure confirmation windows. Apply the team's position from `~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md`. If the playbook is silent on any of these, ask.

### Carveouts

The five carveouts typically present in an NDA:

1. Information that is or becomes public (other than through breach)
2. Information the receiving party already had
3. Information independently developed without reference to the CI
4. Information received from a third party without restriction
5. Information required to be disclosed by law or court order (with notice to discloser where legally permitted)

Which carveouts the team requires, and how strictly, is a playbook question. Check `~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md` for the team's position on required carveouts, acceptable variations in wording, and what happens when one is missing.

### Residuals

A residuals clause lets the receiving party use information retained in unaided memory. Whether this is acceptable — and under what conditions (e.g., narrow "unaided memory" wording vs. broader scope covering notes or copies) — is a playbook question. Apply `~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md`. If the playbook doesn't address residuals, ask.

### Term and survival

Check the initial term length, the post-term survival period for confidentiality obligations, and whether trade secrets are carved out with longer protection. Apply the team's position from `~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md`. If the playbook doesn't cover one of these, ask.

### Restrictive covenants

Check for non-solicits (employee, customer), non-competes, exclusivity, and any restriction on who else the receiving party can engage with. Apply `~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md`. If the playbook is silent, ask — restrictive covenants are jurisdiction-sensitive and the team's posture matters.

### Attorneys' fees

Check for fee-shifting provisions and whether they are mutual, one-sided, or prevailing-party. Apply `~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md`.

### Backup and archival carveout

Check whether the destruction/return clause includes an exception for standard backup and archival retention systems. Apply the team's position from `~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md` — some teams require this carveout and will push to add it; others accept an NDA without it. If the playbook doesn't address this, ask.

### Governing law

Per `~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md` `## Playbook` → `Governing law and venue`.

## Counterparty context

**BigCo NDAs:** Fortune 500 counterparties generally won't negotiate NDAs. Calibrate: is the RED flag truly a deal-breaker, or is it "different from our form"? If the business relationship matters, the call is whether to accept their paper — escalate that decision, don't make it.

**Startup NDAs:** Will usually take our paper. If their NDA has issues, the fastest path is often "let's use ours" rather than redlining theirs.

## Integration: CLM

If connected:
- GREEN → offer to create the CLM record in the standard NDA workflow
- YELLOW → offer to create it with a note attached listing the flagged items
- RED → do not create a record; the lawyer decides what happens next

## What this skill does NOT do

- It does not negotiate. It sorts.
- It does not draft an NDA. If the answer is "use our paper," the user pulls our form from [CLM or document system].
- It does not make the call on YELLOW items. It surfaces them for a human.
- It does not state a position on any NDA term. Positions live in `~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md`.

## Closing action

Read `~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md` → `## NDA triage preferences` → `closing_action`.

If configured, append the closing action verbatim at the end of every
output. Example configurations:

```
closing_action: "Send the full text of this analysis along with a copy
of the NDA to Legal at legal@[yourcompany].com for final confirmation before
signing."

closing_action: "Submit to [CLM] using the standard NDA workflow.
Legal will confirm before routing for signature."

closing_action: "Forward this output and the NDA to your contracts
manager."
```

If `closing_action` is not configured in `~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md`, append:
"Route final NDA through your standard approval process."

The cold-start interview asks: "When someone finishes an NDA
triage, what do you want them to do with the output? I'll add that as
a standing instruction at the end of every review."

## Close with the next-steps decision tree

End with the next-steps decision tree per CLAUDE.md `## Outputs`. Customize the options to what this skill just produced — the five default branches (draft the X, escalate, get more facts, watch and wait, something else) are a starting point, not a lock-in. The tree is the output; the lawyer picks.

