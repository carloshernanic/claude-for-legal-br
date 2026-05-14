---
name: policy-starter
description: >
  Rascunha uma política de uso de IA a partir de políticas-modelo publicadas,
  adaptada ao perfil de prática — ferramenta de pesquisa-e-síntese cujo output é
  um rascunho para revisão e adoção por advogado(a), não política finalizada.
  Aciona quando o(a) usuário(a) diz "rascunha uma política de IA", "precisamos
  de uma política de IA", "construa política de uso de IA", "nossa banca
  precisa de política de GenAI", ou pedidos similares.
argument-hint: "[opcional — sugestão de escopo, p.ex. 'banca toda', 'só time jurídico', 'atualizar existente']"
---

# /policy-starter

> **Adaptação BR não oficial.** Política rascunhada para o framework brasileiro:
> LGPD, Marco Civil, CDC, CF, princípios OCDE, Resoluções CD/ANPD, e específicos
> para banca (Estatuto da OAB — Lei 8.906/1994, Provimentos CFOAB, Resolução CNJ
> 332/2020 e 615/2025 sobre IA no Judiciário, sigilo profissional do(a)
> advogado(a) — EOAB art. 7º, II, XIX e §6º). PL 2338/2023 `[em tramitação —
> verificar]` como referência prospectiva. EU AI Act (especialmente Art. 4 —
> AI literacy; Art. 17 — quality management) como benchmark. Estado-da-arte
> internacional (ABA Op. 512, state bar guidance) como referência comparativa
> de profissão jurídica.

1. Read `~/.claude/plugins/config/claude-for-legal/governanca-ia/CLAUDE.md`. If the practice profile is unpopulated, stop and direct to `/governanca-ia:cold-start-interview`.
2. Use the framework below.
3. Run the scope interview — which sections does the policy need to cover, who's the audience, what's the deployment context. Do not skip to drafting.
4. Web search for the current published model policies and guidance relevant to the deployment context (ABA, state bars, ILTA, CLOC, NIST, peer-firm / peer-company policies, current state AI laws, EU AI Act, sector regulators as applicable).
5. Draft the selected sections, sourced from the model policies, with `[review]` flags on every choice point and `[review]` open questions at the bottom of each section.
6. Output with the draft header ("DRAFT FOR INTERNAL LEGAL REVIEW — NOT FOR DISTRIBUTION"), the sources block, the reviewer note, and the adoption checklist.
7. Close with the next-steps decision tree.

```
/governanca-ia:policy-starter
/governanca-ia:policy-starter "we need an AI policy for our 30-lawyer firm"
/governanca-ia:policy-starter "update our existing policy for the 2026 state AI laws"
```

---

## Matter context

**Matter context.** Check `## Matter workspaces` in the practice-level CLAUDE.md. If `Enabled` is `✗` (the default for in-house users), skip the rest of this paragraph — skills use practice-level context and the matter machinery is invisible. If enabled and there is no active matter, ask: "Which matter is this for? Run `/governanca-ia:matter-workspace switch <slug>` or say `practice-level`." Load the active matter's `matter.md` for matter-specific context and overrides. Write outputs to the matter folder at `~/.claude/plugins/config/claude-for-legal/governanca-ia/matters/<matter-slug>/`. Never read another matter's files unless `Cross-matter context` is `on`.

---

## Purpose

A lot of firms and in-house teams don't have a written AI usage policy yet, or
are running on a 2024-vintage one that doesn't mention the state AI laws, the EU
AI Act implementing acts, the 2025 COPPA amendments, or what they actually ended
up doing with Copilot and Claude for Work. This skill produces a **draft** policy
to bring to the decision-maker — GC, managing partner, executive committee,
board, head of IT, head of HR — not a finished policy to circulate.

The discipline of this skill:

1. **Source from published model policies, not from invention.** Search for and
   read the ABA AI Toolkit, state bar guidance, ILTA's model policy, CLOC's
   templates, and peer-firm / peer-company policies that are public. Cite what
   each source says and adapt it — don't generate policy language out of thin
   air.
2. **Decision-tree the scope before drafting.** A policy that tries to cover
   everything covers nothing. Ask the user what sections the policy needs. Let
   them pick. Then build each picked section with `[review]` flags on every
   choice point.
3. **Flag every judgment call.** The output is a draft the attorney reviews and
   adopts; every threshold, every named tool, every disclosure trigger, every
   enforcement consequence is a `[review]` line.
4. **Header signals the scope of the audience.** This output may be read beyond
   legal — by HR, IT, all staff. The header is adapted accordingly.

This skill does NOT finalize, distribute, publish, or even recommend a specific
position on the hard calls. It produces a draft and surfaces the choices.

## Read `~/.claude/plugins/config/claude-for-legal/governanca-ia/CLAUDE.md` first

Before drafting, always read the practice profile. The sections that drive the
draft:

- `## Company profile` — AI role (Builder / Deployer / Both), regulatory footprint,
  external commitments, practice setting
- `## Use case registry` — what's already approved, conditional, or a red line
- `## AI policy commitments` — what a prior or current policy already says
- `## Vendor AI governance` — what the team already requires from vendors
- `## Governance team and escalation` — who approves, who escalates
- `## Who's using this` — Role (lawyer / non-lawyer) governs the header and the
  "adopt this" framing

If `## AI policy commitments` is populated, this is an UPDATE, not a new draft —
treat the existing policy as the base and propose changes. If it's empty, this
is a first-cut draft.

## Scope interview (do this BEFORE drafting)

Ask the user which sections the policy should cover. Present as a checklist —
the user picks, you build. Do not pre-decide.

> **What should the AI policy cover? Pick the sections you want in the draft:**
> 1. **Scope** — who the policy applies to (all staff, certain roles, contractors), what tools it covers (GenAI only, all AI, specific vendors), what data is in/out of scope.
> 2. **Permitted and prohibited uses** — the approved categories, the red lines, the "ask first" cases.
> 3. **Approval and review** — who approves a new tool, who approves a new use case, how the review request is filed, what the SLA is.
> 4. **Disclosure** — to clients (for firms), to courts, to counterparties, to employees, to end users of an AI feature.
> 5. **Data handling** — what confidential/client/privileged data can go where, data residency, vendor retention terms, training-on-data posture.
> 6. **Training and certification** — who has to take training, on what cadence, consequences for non-completion.
> 7. **Incidents and reporting** — what counts as an AI incident, how to report, who handles.
> 8. **Enforcement** — what happens when the policy is violated, link to disciplinary framework.
> 9. **Review cadence and ownership** — how often the policy gets updated, who owns updates, how changes are communicated.
> 10. **Glossary** — defined terms (GenAI, approved tool, high-risk use, consequential decision, confidential data, etc.).
>
> Default starter pack for a firm / in-house legal team that's never had a policy: 1, 2, 3, 4, 5, 9. Skip the rest for v1.

After the user picks, ask the second question:

> **Two more inputs before I draft:**
> - **Audience** — who's reading this? (All staff / legal team only / attorneys plus staff / client-facing version also needed) This drives tone and the glossary.
> - **Deployment context** — (a) law firm, (b) in-house legal at a company (policy covers legal or company-wide?), (c) legal aid / clinic, (d) government. This drives which model policies I search.

## Source the model policies

Before drafting, run web searches for the most recent published model AI
policies and guidance.

**Derive the model policy sources from the practice profile's `## Regulatory footprint`.** Don't hardcode US sources for a global user.

| Jurisdição | Fontes de política-modelo |
|---|---|
| **BR (default)** | LGPD (especialmente arts. 6º, 9º, 18, 20, 38, 48); Resoluções CD/ANPD (4/2023 sandbox, 15/2024 incidentes, 19/2024 transferência); orientações ANPD; CNJ Resoluções 332/2020 e 615/2025 (IA no Judiciário); Provimentos CFOAB e Estatuto da OAB (Lei 8.906/1994) para política em banca; CDC arts. 6º III, 12, 14, 31, 39; Marco Civil (Lei 12.965/2014) art. 11; CF art. 5º; Lei 7.716 e Lei 12.288 (não-discriminação); Lei 14.811/2024 (deepfake/CSAM); PL 2338/2023 e PL 2630 `[em tramitação — verificar]`; Resoluções TSE em ano eleitoral; Provimento CFM 2.314/2022 (saúde); deliberações BCB/CVM (financeiro); princípios OCDE |
| US (benchmark) | ABA Formal Opinion 512, state bar guidance (CA, FL, NY, TX), ILTA model policy, CLOC templates, peer firm AI policies |
| UK | Solicitors Regulation Authority risk outlook, Law Society AI principles, ICO AI guidance, Bar Council guidance |
| UE | EU AI Act (Art. 4 AI literacy, Art. 17 quality management), orientações nacionais DPA (CNIL, Garante, AEPD), EDPB guidelines |
| Multi-jurisdicional | Use todas aplicáveis, anote onde divergem (p.ex., LGPD art. 20 e PL 2338 enfatizam revisão de decisões automatizadas; EU AI Act exige documentação de supervisão humana; CDC adiciona camada de relação de consumo que muitos regimes não têm) |

If the practice profile's footprint is empty or `[PLACEHOLDER]`, ask: "What jurisdiction(s) does your organization operate in? I'll draft from the model policies that match your regulatory environment and professional responsibility framework, not a US-centric template."

For each source the draft uses, **record it in a "Sources" block at the top of
the output** with: name, URL, date accessed, and what the draft took from it.

If a web search can't be run, note in the reviewer note: "Could not run web
search — draft sourced from training knowledge alone, verify against current
versions of the cited sources before adopting." The verification log applies.

## The draft

Output follows a consistent structure. **Every choice point gets a `[review]`
flag.** The user has to decide; the skill presents options.

### Header

```
DRAFT FOR INTERNAL LEGAL REVIEW — NOT FOR DISTRIBUTION
Prepared for: [firm / company name from practice profile]
Date: [today's date]
Prepared by: governanca-ia policy-starter skill, adapted from published model policies
Not for adoption, distribution, posting, or reliance until reviewed, adapted, and approved by [attorney / GC / managing partner / executive committee per the governance team section of the practice profile].
```

When the Role in `## Who's using this` is Non-lawyer: add a second line under
the header — "If you are not a licensed attorney, solicitor, barrister, or other
authorised legal professional in your jurisdiction, bring this draft to your
attorney contact ([name from practice profile]) before using any of it. This is
a starting draft for their review, not a policy you can adopt."

### Sources block (at the top, under the header)

A table of the model policies / guidance / regulations the draft drew from:

| Fonte | URL | Acessado | O que o rascunho extraiu |
|---|---|---|---|
| LGPD art. 20 | planalto.gov.br | [data] | Decisão automatizada e direito à revisão |
| Resolução CD/ANPD 15/2024 | anpd.gov.br | [data] | Notificação de incidente |
| Resolução CNJ 332/2020 | cnj.jus.br | [data] | Transparência e supervisão humana |
| PL 2338/2023 (texto atual) | senado.leg.br | [data] | Categorias de risco (em tramitação) |
| Estatuto da OAB art. 7º, XIX | planalto.gov.br | [data] | Sigilo profissional como limite |
| ABA Formal Op. 512 (benchmark) | [url] | [data] | Framing de competência e disclosure |
| EU AI Act, Art. [X] (benchmark) | [url] | [data] | Flow-down a fornecedor |

### Executive summary

Three paragraphs max. What the policy does, who it binds, what the reader has
to do before it takes effect.

### The sections

Only the sections the user picked, in the order above. For each:

- A **header and scope** sentence.
- The **substantive rules**, adapted from the cited model policies. Every
  specific threshold, number, named tool, named vendor, or escalation contact
  is `[review]`. Example: "Confidential client data may not be entered into
  [general-purpose consumer AI tools] `[review — list tools, or reference the
  approved-tools list]`. Use of such data in [approved firm-licensed tools]
  `[review — list tools]` is permitted subject to the data handling section."
- **Source attribution** inline where a rule is adapted from a specific source.
  Example: "Attorneys must verify the accuracy of all AI-generated work product
  before using it in representation of a client `[ABA Formal Op. 512]`."
- **Open questions** at the bottom of each section — 2-3 decisions the attorney
  needs to make before the section is ready. These are distinct from inline
  `[review]` flags — these are the "we don't have a position here yet" items,
  not the "fill in the specifics" items.

### Adoption checklist

At the end of the draft, a checklist of the things that have to happen before
the policy is adopted. Don't invent these — pull from the practice profile's
governance team and escalation section. Typical items:

- [ ] Review by GC / managing partner `[review — name]`
- [ ] Review by IT / security `[review — name]`
- [ ] Review by HR (for enforcement / training sections) `[review — name]`
- [ ] Board / executive committee approval (if required) `[review — confirm whether required]`
- [ ] Training materials drafted
- [ ] Announcement drafted
- [ ] Effective date set `[review]`
- [ ] Review cadence calendared `[review — annual is typical]`
- [ ] Add policy to the `## AI policy commitments` section of the practice
      profile once adopted

### Reviewer note

The standard reviewer note above the header, per the `## Outputs` section of
the practice profile. Use the block format:

> **⚠️ Reviewer note**
> - **Sources:** web search ✓ / not connected — cites from training knowledge
> - **Read:** practice profile · [N] published model policies
> - **Flagged for your judgment:** [N] `[review]` items inline · [N] open questions per section
> - **Currency:** searched for developments since [date]
> - **Before relying:** this is a DRAFT — bring to [approver from practice profile], don't distribute until adopted

## Don'ts

- **Don't invent policy language.** Every substantive rule in the draft must be
  traceable to a cited source or flagged `[review — adapted, no direct source]`.
- **Don't pick the hard calls for the attorney.** "Should paralegals be
  permitted to use AI for first-draft work?" is a `[review]`, not a recommended
  position.
- **Don't produce a finished-looking policy.** The header, the reviewer note,
  and the `[review]` flags throughout are the signal that this is a draft. Do
  not soften them.
- **Don't skip the scope interview.** If the user says "just draft a full
  policy," push back: "A policy that tries to cover everything covers nothing.
  Which sections do you want? Here's the checklist." One round of negotiation
  is fine — two is also fine. Drafting without scope is the failure mode.
- **Don't generate section content the user didn't ask for.** If they picked 1,
  2, 3, 4, 5, 9, do those. Don't add section 6 because "a real policy needs
  training."
- **Don't recommend a specific vendor, tool, or consequence.** Flag those
  `[review]` with context on what a typical decision would be, not what the
  user's should be.
- **Don't promise legal sufficiency.** The draft is a starting point for
  attorney review, not a tested policy.

## Handoffs

After the draft is produced, close with the decision tree from the practice
profile. The most common next steps:

1. **Tune the draft** — the user walks through the `[review]` flags and resolves
   them with the attorney; the skill re-runs with the decisions baked in.
2. **Stakeholder summary** — produce a one-page version for the board or
   executive committee explaining what the policy does and doesn't do.
3. **Training materials** — once the policy is adopted, `/governanca-ia:aia-generation` can be used to produce per-use-case training notes.
4. **Vendor sweep** — once the policy is adopted, `/governanca-ia:vendor-ai-review` should be run against the vendors the policy references to check conformance.
5. **Gap check against new regulation** — pair with `/governanca-ia:reg-gap-analysis` to test the draft against a specific regulation or guidance before adoption.

## Output scope reminder

The document this skill produces reaches HR, IT, and the broader business — not
just legal. Keep the language plain enough for non-lawyers to follow. The legal
precision is in the `[review]` flags and the sources, not in jargon.
