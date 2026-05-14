---
name: vendor-agreement-review
description: >
  Reference: review of an inbound vendor agreement against the team playbook in
  `~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md`. Flags deviations, assesses risk, generates
  specific redline language, and routes to the right approver. Loaded by
  /comercial:review when a vendor MSA, services agreement, or similar is detected.
user-invocable: false
---

# Revisão de Contrato com Fornecedor (Vendor Agreement)

**Adaptação BR.** Plugin adaptado ao Brasil. Limitação de responsabilidade B2B: admitida salvo dolo (CC arts. 421/422/944). Em consumo (CDC art. 51, I): qualquer atenuação nula. Indenização limita-se à extensão do dano (CC art. 944). Cláusula penal: cap = valor da obrigação principal (CC art. 412). Cessão de contrato: CC art. 286+. Força maior/caso fortuito: CC art. 393. LGPD em vez de GDPR. E-sig: Lei 14.063/2020 e MP 2.200-2/2001 (ICP-Brasil e assinaturas eletrônicas avançadas/simples). Lei aplicável: LINDB art. 9º. Cláusula compromissória: Lei 9.307/1996 (câmaras: CAM-CCBC, CAM B3, CBMA, CMA-FGV). Sucumbência: CPC arts. 82–85. Antes de tudo: identifique B2B (CC) ou consumo (CDC).

## Contexto de matéria

**Matter context.** Check `## Matter workspaces` in the practice-level CLAUDE.md. If `Enabled` is `✗` (the default for in-house users), skip the rest of this paragraph — skills use practice-level context and the matter machinery is invisible. If enabled and there is no active matter, ask: "Which matter is this for? Run `/comercial:matter-workspace switch <slug>` or say `practice-level`." Load the active matter's `matter.md` for matter-specific context and overrides. Write outputs to the matter folder at `~/.claude/plugins/config/claude-for-legal/comercial/matters/<matter-slug>/`. Never read another matter's files unless `Cross-matter context` is `on`.

---

## Destination check

Before producing output, check where it's going. If the user has named a destination (a channel, a distribution list, a counterparty, "everyone"), ask whether it's inside the privilege circle. Public channels, company-wide lists, counterparty/opposing counsel, vendors, and clients (for work product) waive the protection. When the destination looks outside the circle, flag it and offer (a) the privileged version for legal only, (b) a sanitized version for the broader channel, or (c) both — don't silently apply a privileged header and then help paste it somewhere the header won't protect it. See the canonical `## Shared guardrails → Destination check` in this plugin's CLAUDE.md.

## Purpose

Read a vendor agreement against the playbook this team actually uses (in `~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md`), find every term that deviates, and tell the lawyer what to do about each one — with specific redline language, not vague "consider revising."

The output is a review memo the lawyer can act on in one pass. Every issue has a severity, a business-impact explanation, a proposed fix, and an escalation call if one is needed.

## Precondition: load the playbook

**Before reading the contract, read `~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md`.** If it's missing or still has placeholders, surface this bounce:

> I notice you haven't configured your practice profile yet — that's how I tailor playbook positions, escalation, and house style to your practice.
>
> **Two choices:**
> - Run `/comercial:cold-start-interview` (2 minutes) to configure your profile, then I'll review tailored to YOUR playbook.
> - Say **"provisional"** and I'll review against generic defaults — US jurisdiction, middle risk appetite, lawyer role, no playbook (flag all common vendor-contract risks from first principles) — and tag every output `[PROVISIONAL — configure your profile for tailored output]` so you can see what I do before committing.

### Modo provisório

Se o usuário disser "provisório", rode a revisão normalmente com estes padrões genéricos: apetite de risco mediano, papel advogado(a), jurisdição Brasil (lei brasileira, foro Comarca da Capital de SP), sem playbook (flague os riscos comuns do lado comprador a partir de princípios — responsabilidade sem cap, sem ressalvas de violação de dados, indenização sem cap, renovação automática sem notificação, ausência de cláusula LGPD, etc. — em vez de cruzar com posições configuradas). Marque a nota de revisor e cada bloco de achado com `[PROVISÓRIO]`. Ao final, anexe:

> "Isto foi uma rodada genérica contra premissas padrão. Rode `/comercial:cold-start-interview` para saída calibrada à SUA prática — seu playbook, sua jurisdição, seu apetite de risco. 2 minutos."

**Which side?** Before applying the playbook, determine which side the company is on for this contract. Usually obvious: if the counterparty is a vendor/supplier providing goods or services, you're purchasing-side. If the counterparty is a customer buying your product/service, you're sales-side. If it's not obvious (a reseller agreement, a partnership, a revenue share), ask: "Which side is [company] on for this agreement — vendor or customer?" Read the matching playbook section (`### Sales-side playbook` or `### Purchasing-side playbook`) from the config. Note which side in the output so the reviewer knows which playbook was applied. If the matching side is `[Not configured]`, stop and tell the user to run `/comercial:cold-start-interview --side <side>` before this review can proceed.

This skill is typically used for purchasing-side contracts (vendors supplying you), but the side check still applies — a "vendor agreement" could be your own template sent to a vendor as part of a reseller arrangement (sales-side).

The playbook in `~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md` is the source of truth. It tells you:
- What this team's standard positions are (not market standard — *their* standard)
- What fallbacks they've accepted before
- What they never accept
- Who approves what
- The one deal-breaker to check first

If the contract has the deal-breaker, flag it at the top of the memo and stop the detailed review. There's no point spending 30 minutes on liability caps if the agreement gives the vendor rights to use customer data for training.

## Workflow

### Step 1: Orient

Read the whole agreement once, fast. Answer:

| Question | Answer |
|---|---|
| What kind of agreement is this? | MSA / SaaS subscription / Professional services / License / Other |
| Who are we? | Customer / Vendor (this plugin assumes customer — flag if not) |
| Counterparty | Name, and are they a BigCo (won't negotiate) or a startup (will)? |
| Dollar value | Annual / total contract value if stated |
| Term | Length, renewal mechanics |
| Is there a DPA? | Attached / referenced by URL / missing |
| Is there an order form? | Separate doc or integrated |

**Dollar-value handling.** If the main agreement does not state a dollar value (the MSA sets terms but the Order Form carries price, which is typical), **stop and ask** before running escalation math or applying dollar thresholds:

> The MSA itself doesn't state an annual contract value. The Order Form carries the price. Your escalation threshold is $[X from the matrix]. Before I route this, I need the ACV. Options:
> 1. Paste the Order Form value (preferred — I'll use it for routing and the memo).
> 2. Tell me if this is above or below $[threshold] and I'll route accordingly; the memo will flag that the routing assumed [above/below threshold] without an ACV in hand.
> 3. Route conservatively to the higher approver regardless — safer for a review you haven't priced.

Do NOT silently assume a value and then use the assumed value to drive routing. The assumption propagates into the approval call, which is a place the review shouldn't be guessing.

**DPA-by-reference handling.** If the main agreement incorporates a DPA "available at [URL]" or "as set forth at [URL]" or similar by reference, the DPA is part of the contract but is not in front of you. Note it explicitly in the Orient table and in the review memo:

> This agreement incorporates a DPA by URL reference at `[URL]`. The DPA carries the real data terms — subprocessor rights, breach-notification timing, data-return mechanics, standard contractual clauses, audit rights. Without reading it, the data-protection analysis below is partial. Offer to route the DPA to `/privacidade:dpa-review` (if installed) for a separate review, or fetch and read it inline before completing Step 3's data-protection analysis.

If the user is installed with `privacidade`, explicitly offer:

> Want me to hand the DPA URL to `/privacidade:dpa-review` once you're ready? That skill is built for the DPA work and will catch subprocessor / SCC / breach-notification issues that this skill only flags at the gate.

Do not silently proceed as if the DPA were absent when it is incorporated by reference. A missing DPA and an unread DPA are different gaps — label them differently.

### Step 2: Deal-breaker check

Check the "one thing" from `~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md` first. If present:

```markdown
## ⛔ DEAL-BREAKER PRESENT

**Section [X.X]** contains [the deal-breaker]. Per the team playbook, this is a
hard no. Recommend:

- [ ] Push back — propose [specific alternative language]
- [ ] Walk — if counterparty won't move, we don't sign

Detailed review below is provided for completeness but is moot unless this is
resolved.
```

### Step 3: Term-by-term comparison

For each playbook category in `~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md`, find the corresponding contract section and compare.

**For each deviation, produce:**

```markdown
### [Section X.X]: [Issue name]

**Playbook says:** [our standard position, quoted from `~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md`]

**Contract says:**
> "[exact quote from the contract]"

**Gap:** [Missing term | Weaker than standard | Weaker than fallback | Non-standard structure | Unacceptable]

**Legal risk:** 🔴 Critical | 🟠 High | 🟡 Medium | 🟢 Low
**Business friction:** 🔴 Blocks deals | 🟠 Slows deals | 🟡 Confuses customers | 🟢 Invisible

**Why it matters:** [one or two sentences in plain English — what goes wrong
for the business if this term stays as-is]

**Proposed redline:**
> "[the specific replacement language — ready to paste into a markup]"

**If they won't move:** [the fallback from `~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md`, or "escalate to [person]"
if no fallback exists]
```

**Severity calibration:**

| Level | Means |
|---|---|
| 🔴 Critical | Don't sign without fixing. A term on the team's "never accept" list in `~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md`, or a deal-breaker. |
| 🟠 High | Strongly push; escalate if they won't move. A term outside the playbook's stated fallback range. |
| 🟡 Medium | Push in first round; accept if it's the last open item. A term inside the fallback range but short of the standard position. |
| 🟢 Low | Note it, don't spend capital. A term the playbook explicitly tolerates, or a purely stylistic deviation. |

Severity is always applied *against `~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md`*. If a term doesn't map cleanly to a playbook position, ask the user which bucket it belongs in and offer to record the answer in `~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md`.

#### Liability cap decision procedure

**The cap amount is the least important part of the cap.** When reviewing the limitation-of-liability clause, do not produce a single "check liability cap against playbook" line item. Work through the four dimensions below and state each one explicitly in the finding:

1. **Direct vs. indirect/consequential damages.** Does the cap apply to ALL liability, or only direct damages? A 12-month cap on direct damages with uncapped consequential damages is a completely different position than a 12-month aggregate cap. State both treatments explicitly.

2. **The cap base — quote it verbatim.** "12-month cap" could mean: (a) fees paid in the 12 months preceding the claim, (b) fees payable in the current 12-month period, (c) fees over the last 12 months of usage, (d) fees under the current order form, (e) total fees ever paid. These can differ by an order of magnitude. Quote the exact language. If ambiguous, flag it: "Cap base is ambiguous — `[the quoted language]` — could mean [X] or [Y]. Confirm before signing."

3. **Cap-carveout interaction.** A $100K cap with uncapped indemnity for data breach, IP, and confidentiality is functionally uncapped for the claims that actually arise in SaaS disputes. Enumerate what sits ABOVE the cap (the carveouts), what sits BELOW (what's actually capped), and assess whether the capped surface is meaningful: "The cap covers [general contract breach]. Data breach, IP indemnity, and confidentiality are carved out and uncapped. For this vendor's risk profile, the capped surface is [meaningful / nominal]."

4. **Your playbook position per dimension.** The practice profile should have positions for: direct cap (multiple of fees), indirect damages (excluded / capped / uncapped), carveout list (what's acceptable above the cap), and cap base (which definition you'll accept). If the playbook has one "standard position" field, note: "Your playbook has a single cap position — consider splitting into direct/indirect/carveouts/base for more precise review."

#### Checagem de delta jurisdicional

**O playbook aplica preferência global de lei. Exequibilidade varia materialmente.** Cheque a lei aplicável do contrato contra as principais divergências antes de aceitar posições do playbook:

- **Não-aliciamento / não-concorrência:** No Brasil em B2B, válidas se limitadas em tempo, território e atividade, com indenização adequada (CC art. 421/422). Em CLT, exige indenização (TST). `[verificar jurisdição]`
- **Renovação automática:** CC art. 423 (adesão — favorável ao aderente). CDC arts. 51 e 54 (consumo — onerosa exige destaque). `[verificar jurisdição]`
- **Exclusão de responsabilidade:** B2B paritário admite salvo dolo (CC 421/422/944). Consumo: nula (CDC 51, I). Adesão B2B: renúncia antecipada a direito da natureza do negócio é nula (CC 424). `[verificar jurisdição]`
- **Indenização:** Cap = extensão do dano (CC 944). Cláusula penal cap = valor da obrigação principal (CC 412). Em consumo, multa moratória ao consumidor cap 2% (CDC 52 §1º). `[verificar jurisdição]`
- **Confidencialidade:** "Perpétua" pode ser reduzida por razoabilidade. Segredo de empresa: Lei 9.279/1996 art. 195, XI–XII. `[verificar jurisdição]`
- **Eleição de foro:** CPC art. 63 — válida em B2B; restrita em adesão (§3º); nula em consumo abusivo (CDC 51, IV). `[verificar jurisdição]`
- **Cláusula compromissória:** Lei 9.307/1996. Em consumo, só se o consumidor a propuser ou aderir voluntariamente (art. 4º §2º). `[verificar jurisdição]`
- **Sigilo em mediação/conciliação:** CPC art. 166 §3º (princípio da confidencialidade — análogo brasileiro mais próximo de FRE 408; em negociação livre fora de mediação institucionalizada, não há análogo geral). `[verificar jurisdição]`

Quando a posição do playbook conflita com a exequibilidade sob a lei aplicável, flague: "Seu playbook prefere [X], mas este contrato é regido por [Y] onde [X] é [inexequível / restrito / sujeito a override]. `[verificar jurisdição]`"

### Step 4: Favorable terms and gaps

Two short lists:

**Better than our standard:** Terms where the vendor gave us more than we'd ask for. Note these — they're trade bait if you need to give something up elsewhere.

**Missing entirely:** Standard provisions that just aren't there. Most common: assignment restrictions, audit rights (if we want them), force majeure, insurance requirements.

### Step 5: Escalation routing

Check the escalation matrix in `~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md` against:
- Contract dollar value
- Presence of any 🔴 critical issues
- Any automatic-escalation triggers (unlimited liability, IP assignment, etc.)

State clearly who needs to approve this:

```markdown
## Approval routing

Based on [dollar value / issue severity], this agreement requires:

- [ ] **[Name/role]** approval — [reason]
- [ ] **Business owner sign-off** on [specific commercial term they should weigh in on]

**Recommended next step:** [Send redlines to counterparty | Escalate to GC before
responding | Get business input on commercial term X before legal responds]
```

**Before proceeding to send redlines to the counterparty:** Read `## Who's using this` in `~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md`. If the Role is Non-lawyer:

> Sending redlines is a legal act — the counterparty will treat every edit as our negotiating position. Have you reviewed this with an attorney? If yes, proceed. If no, here's a brief to bring to them:
>
> [Generate a 1-page summary: counterparty, agreement type, the specific redlines proposed, the playbook positions behind each, the fallbacks, and what to ask the attorney before the package leaves.]
>
> Se precisar de advogado(a): consulte a OAB da sua seccional (oab.org.br) para indicação. Defensoria Pública e núcleos universitários também são opção.

Do not proceed past this gate without an explicit yes.

## Redline granularity

**Edit at the smallest possible granularity.** A redline is a negotiation artifact, not a rewrite. Wholesale clause replacement signals "we threw out your drafting" — it's aggressive, it forces the counterparty to re-read the whole clause, and it discards the parts of their drafting that were fine. Surgical redlines — strike a word, insert a phrase, restructure a subclause — signal "we have specific asks" and are faster to read, understand, and accept.

Default to the smallest edit that achieves the playbook position:
- Replace a **word** before a phrase. ("twelve (12)" → "twenty-four (24)")
- Replace a **phrase** before a sentence. ("paid by the Buyer" → "paid and payable by the Buyer")
- Restructure a **subclause** before replacing the sentence. (Add "(a)" and "(b)" to split a compound condition.)
- Replace a **sentence** before replacing the clause.
- Only replace a **whole clause** when the counterparty's version is so far from your position that surgical edits would be harder to read than a fresh draft — and when you do, say so in the transmittal: "We've replaced §8.2 rather than marking it up because the changes were extensive. Happy to walk you through the delta."

When in doubt, smaller. A client who receives a surgical redline trusts that you read carefully. A client who receives a wholesale replacement wonders whether you read at all.

### Step 6: Assemble the memo

Prepend the work-product header from `~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md` `## Outputs` (it differs by user role — see `## Who's using this`).

This memo and the underlying agreement may be privileged, confidential, or both. The output inherits that status from the source. Distribute only within the privilege circle; mark and store it where privileged materials live; strip the work-product header before any external delivery (e.g., counterparty redlines, stakeholder summaries).

The playbook positions applied below reflect the jurisdiction recorded in `~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md` → `Governing law and venue`. Legal rules and enforceability vary materially by jurisdiction. If this deal implicates a different governing law or a choice-of-law question, flag it in the memo — the analysis may not transfer as written.

> **No silent supplement.** If a research query to the configured legal research tool returns few or no results for a rule the memo needs (enforceability of a limitation clause, indemnity scope, governing-law choice), report what was found and stop. Do NOT fill the gap from web search or model knowledge without asking. Say: "The search returned [N] results from [tool]. Coverage appears thin for [rule / jurisdiction]. Options: (1) broaden the search query, (2) try a different research tool, (3) search the web — results will be tagged `[web search — verify]` and should be checked against a primary source before relying, or (4) flag as unverified and stop. Which would you like?" A lawyer decides whether to accept lower-confidence sources.
>
> **Source attribution.** Where the memo cites a statute, regulation, or case, tag the citation: `[Westlaw]`, `[statute / regulator site]`, or the MCP tool name for citations retrieved from a legal research connector; `[web search — verify]` for web-search citations; `[model knowledge — verify]` for citations recalled from training data; `[user provided]` for citations from the counterparty draft or house files. Citations tagged `verify` carry higher fabrication risk and should be checked first. Never strip or collapse the tags.

```markdown
[WORK-PRODUCT HEADER — per plugin config ## Outputs]

# Vendor Agreement Review: [Counterparty] [Agreement Type]

**Reviewed:** [date]
**Contract value:** $[amount] / [term]
**Our role:** Customer

---

## Bottom line

[Two sentences. Can we sign this? What has to change first?]

**Issues (legal risk):** [N]🔴 [N]🟠 [N]🟡 [N]🟢
**Issues (business friction):** [N]🔴 [N]🟠 [N]🟡 [N]🟢

**Approval needed from:** [name]

---

## Deal-breaker check

[✅ Clear | ⛔ Present — see above]

---

## Issues by severity

[All the deviation blocks from Step 3, grouped Critical → Low]

---

## Favorable terms

[list]

## Missing provisions

[list]

---

## Approval routing

[from Step 5]

---

## Redline package

[If requested: consolidated markup-ready language for all proposed changes]
```

## Integration: [CLM]

If a [CLM] MCP is connected, after the review:

- Check if this counterparty already has agreements with us (may inform negotiating posture — "we already gave them 24-month cap on the last deal")
- Pull the workflow template that matches this agreement type
- Offer to create the [CLM] record with the review memo attached and approvers pre-routed

## Integração: assinatura eletrônica (DocuSign / Clicksign / D4Sign / ZapSign / Autentique / Adobe Sign)

Se o MCP de e-sig estiver conectado e o contrato pronto para assinar (tudo verde ou todos os pontos aceitos), ofereça:
- Gerar o envelope
- Rotear signatários na ordem correta conforme matriz de escalonamento
- Marcar nível de assinatura: simples / avançada / qualificada (ICP-Brasil) conforme exigência da MP 2.200-2/2001 e Lei 14.063/2020. Para contrato com órgão público, em geral exige-se assinatura qualificada com certificado ICP-Brasil; entre privados, assinatura simples/avançada é válida (CC art. 219 — ônus da prova).

**Não** envie nada para assinatura sem instrução explícita. "Pronto para assinar" é call do(a) advogado(a), não sua.

**Before generating a signature envelope or routing for countersignature:** Read `## Who's using this` in `~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md`. If the Role is Non-lawyer:

> This step has legal consequences (signing binds the company to the whole agreement). Have you reviewed this with an attorney? If yes, proceed. If no, here's a brief to bring to them:
>
> [Generate a 1-page summary: counterparty, contract value, the issues found and how they resolved, any risk the lawyer accepted, and what to ask the attorney before envelope goes out.]
>
> Se precisar de advogado(a): consulte a OAB da sua seccional (oab.org.br) para indicação. Defensoria Pública e núcleos universitários também são opção.

Do not proceed past this gate without an explicit yes.

## Output formats

**Full memo (default):** As above. Goes in the [CLM] record or the Drive folder from `~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md` house-style section.

**Slack-sized summary:** Two lines and a link. For when someone asks "is this okay?" in a channel.

```
[Counterparty] [type] — NEEDS WORK. 1🔴 (uncapped liability §8.2), 2🟠. Full review: [link]. Needs [GC] approval.
```

**Redline doc:** If the user asks for it, output a .docx with tracked changes. Use the docx skill. Comments on each change cite the playbook position.

## Quality checks before delivering

- [ ] `~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md` was loaded and quoted — not generic market positions
- [ ] Deal-breaker checked first
- [ ] Every issue has specific replacement language
- [ ] Risk levels are calibrated (not everything is Critical)
- [ ] Approver is named, not "escalate to legal"
- [ ] Counterparty context considered (BigCo vs. startup — affects what's worth fighting over)

## Close with the next-steps decision tree

End with the next-steps decision tree per CLAUDE.md `## Outputs`. Customize the options to what this skill just produced — the five default branches (draft the X, escalate, get more facts, watch and wait, something else) are a starting point, not a lock-in. The tree is the output; the lawyer picks.

