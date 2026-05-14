---
name: vendor-ai-review
description: >
  Revisa termos de IA de fornecedor — contrato, aditivo ou disposições de IA em
  ToS — contra suas posições de governança; sinaliza treinamento sobre dados,
  responsabilidade, mudanças de modelo e consistência com a política de IA.
  Aciona quando o(a) usuário(a) diz "revise esse contrato de IA", "checa os
  termos da OpenAI", "o que acordamos com [fornecedor]", "fornecedor mandou
  aditivo de IA", "esse contrato de IA está ok", ou anexa termos.
argument-hint: "[nome do fornecedor, ou anexe o contrato]"
---

# /vendor-ai-review

> **Adaptação BR não oficial.** Revisão calibrada para LGPD (bases legais — arts.
> 7º e 11; operador/controlador — arts. 5º VI-VII; segurança e incidente — arts.
> 46-48; transferência internacional — arts. 33-36 + Resolução CD/ANPD 19/2024;
> direito à revisão — art. 20; RIPD — art. 38), Resolução CD/ANPD 15/2024
> (incidentes), Marco Civil (Lei 12.965/2014), CDC arts. 12, 14, 25 (vedação a
> exoneração de responsabilidade em relação de consumo), CC arts. 186, 421, 422,
> 927 (responsabilidade civil), Lei 9.610/98 (autoral — debate de treinamento;
> PL 2338 traz proposta de regra de mineração de dados `[em tramitação —
> verificar]`), e Estatuto da OAB art. 7º, XIX (sigilo profissional) quando
> banca contrata fornecedor de IA. EU AI Act provedor-vs-deployer e GDPR como
> benchmark / obrigatório se houver nexo na UE.

1. Read `~/.claude/plugins/config/claude-for-legal/governanca-ia/CLAUDE.md`. Confirm vendor governance positions are populated — if not, stop and direct to setup.
2. Use the framework below.
3. Confirm document type (AI addendum / main agreement AI provisions / ToS). If only an AUP was provided, ask for the full terms.
4. Term-by-term review: training on data, confidentiality of inputs, model changes, output IP, liability, incident notification, human review rights, use restrictions, audit rights.
5. AI addendum gap check if DPA exists but no AI addendum.
6. AI policy consistency diff vs. `~/.claude/plugins/config/claude-for-legal/governanca-ia/CLAUDE.md`.
7. Output: bottom line, term-by-term, recommended redlines, if-they-won't-move routing.

```
/governanca-ia:vendor-ai-review openai-enterprise-agreement.pdf
```

---

## Matter context

**Matter context.** Check `## Matter workspaces` in the practice-level CLAUDE.md. If `Enabled` is `✗` (the default for in-house users), skip the rest of this paragraph — skills use practice-level context and the matter machinery is invisible. If enabled and there is no active matter, ask: "Which matter is this for? Run `/governanca-ia:matter-workspace switch <slug>` or say `practice-level`." Load the active matter's `matter.md` for matter-specific context and overrides. Write outputs to the matter folder at `~/.claude/plugins/config/claude-for-legal/governanca-ia/matters/<matter-slug>/`. Never read another matter's files unless `Cross-matter context` is `on`.

---

## Purpose

Vendor AI terms are where your governance positions actually get tested. The cold-start
interview captures what you *want*. This skill checks what you *agreed to* — and flags
the gaps between those two things.

The direction here is always the same: we are the deployer or buyer reviewing the
vendor's terms. This is the opposite posture from the DPA review controller/processor
question — there's no flip.

What varies is the *input*:
- A standalone AI agreement or AI addendum (most structured)
- A vendor's universal terms of service with AI provisions embedded (often buried)
- An acceptable use policy (tells you what you can't do; says nothing about what
  the vendor can do with your data or outputs)
- A combination — master agreement + DPA + AI addendum (common for serious enterprise
  AI vendors)

When there's a DPA already in place, this review complements it — it's not a
substitute. The DPA governs data protection obligations; the AI terms govern
model-specific rights and risks. Both need to be reviewed.

---

## Load the playbook

Read `~/.claude/plugins/config/claude-for-legal/governanca-ia/CLAUDE.md` → `## Vendor AI governance`. Also read `## AI policy commitments`
— vendor terms can't be consistent with a use restriction our own policy imposes if
we've agreed to something different.

If `~/.claude/plugins/config/claude-for-legal/governanca-ia/CLAUDE.md` contains `[PLACEHOLDER]`, surface this bounce:

> I notice you haven't configured your practice profile yet — that's how I tailor vendor governance positions to your practice.
>
> **Two choices:**
> - Run `/governanca-ia:cold-start-interview` (2 minutes) to configure your profile, then I'll review tailored to YOUR positions.
> - Say **"provisional"** and I'll review against generic defaults — US jurisdiction, middle risk appetite, lawyer role, no playbook — and tag every output `[PROVISIONAL — configure your profile for tailored output]` so you can see what I do before committing.

### Provisional mode

If the user says "provisional," run the vendor AI review normally using these generic defaults: middle risk appetite, lawyer role, US jurisdiction, no playbook (flag all common vendor-AI risks from first principles rather than matching to configured positions). Tag the reviewer note and every finding block with `[PROVISIONAL]`. At the end of the output, append:

> "That was a generic run against default assumptions. Run `/governanca-ia:cold-start-interview` to get output calibrated to YOUR practice — your vendor governance positions, your jurisdiction, your risk appetite. 2 minutes."

---

## Before reading the document

If the user hasn't shared the actual vendor terms, ask:

> "Can you share the vendor's AI terms? The most useful thing is the actual contract
> language — the AI addendum if there is one, or the main agreement with AI provisions
> highlighted. An acceptable use policy alone won't tell us what the vendor can do
> with our inputs; it only tells us what we're allowed to do."

If they share an acceptable use policy only:
> "This is the acceptable use policy — it tells us what we can't do with the vendor's
> AI. That's useful context, but it doesn't address the commercial terms: whether
> the vendor can train on our data, what their liability is for AI errors, whether
> they notify us when the model changes. Do you have the service agreement or AI
> addendum?"

---

## The term-by-term review

### Core AI-specific terms (check every vendor AI agreement)

Review each term below. For each, extract what the vendor's contract actually says and compare it against the position in `~/.claude/plugins/config/claude-for-legal/governanca-ia/CLAUDE.md` → `## Vendor AI governance` (standard / acceptable fallback / automatic no). The default positions come from the team's playbook, not from this skill.

| Termo | O que procurar |
|---|---|
| **Treinamento sobre nossos dados** | Fornecedor usa nossos inputs para treinar, fine-tunar ou melhorar modelos? Há opt-out explícito ou proibição? Treinamento é opt-in ou opt-out por default? (Aciona LGPD — base legal; Lei 9.610/98 — autoral; debate PL 2338 sobre mineração de dados.) |
| **Sigilo dos inputs** | Nossos prompts, documentos e dados são confidenciais? Há ressalvas de "revisão de qualidade" que deixem equipe do fornecedor ler inputs? (Crítico para banca — EOAB art. 7º, XIX — sigilo profissional; LGPD se houver dados pessoais.) |
| **Mudanças de modelo** | Há obrigação de notificação de mudanças materiais ao modelo? Version pinning disponível? |
| **Titularidade do output / PI** | Quem possui o conteúdo gerado por IA? Licença de retorno ao fornecedor sobre outputs? Indenização por PI? (Lei 9.610; debate de proteção de outputs.) |
| **Responsabilidade por outputs** | Fornecedor aceita responsabilidade se a IA produz outputs nocivos, incorretos ou infratores? Estrutura de cap? Ressalvas? (CC arts. 186 e 927; CDC arts. 12, 14, 25 se há relação de consumo — exoneração de responsabilidade ao consumidor é vedada; Marco Civil art. 19 para conteúdo gerado.) |
| **Notificação de incidente** | Como e quando somos notificados se o sistema falha, é comprometido ou produz erros sistemáticos? (LGPD art. 48; Resolução CD/ANPD 15/2024 — prazos e conteúdo da comunicação.) |
| **Direito de revisão humana** | Podemos exigir revisão humana de outputs em casos específicos? Podemos contestar decisão de IA? (LGPD art. 20 — direito à revisão de decisão automatizada.) |
| **Restrições de uso** | O que somos proibidos de fazer? Bate com o que queremos? Termos definicionais (p.ex., "tomada de decisão automatizada") podem varrer usos pretendidos? |
| **Auditoria / auditabilidade** | SOC 2, auditorias de terceiro, resultados de teste de viés — direitos de auditoria? (Alinhar com LGPD art. 20 — explicabilidade; princípios OCDE.) |
| **Suboperadores / provedores de modelo** | Fornecedor usa sub-fornecedores para o modelo? São divulgados? Quais termos regem? (LGPD art. 39 — operadores e suboperadores.) |
| **Localização dos dados** | Onde nossos dados são processados? Para onde vão para inferência? (LGPD arts. 33-36 — transferência internacional; Resolução CD/ANPD 19/2024.) |
| **Prazo e rescisão** | O que acontece com nossos dados ao rescindir? Prazos de exclusão? (LGPD art. 15 — término do tratamento; art. 18 — exclusão.) |
| **Stacked-vendor accountability** | Is this vendor the model provider (e.g., Anthropic, OpenAI, Google, Meta), or are they a deployer of someone else's model (e.g., a SaaS wrapper of Claude, ChatGPT, or Gemini) or a reseller of infrastructure-hosted foundation models (Anthropic-on-Bedrock, Claude-on-Vertex, OpenAI-on-Azure)? If the latter: there are TWO vendors' terms in play — the one you're reviewing, plus the upstream model provider's terms. Identify (a) whose terms govern training on inputs, retention, and safety, (b) who is contractually liable for model behavior, and (c) whether each upstream commitment (e.g., "no training on inputs") is flowed down to you, or remains between the vendor and the upstream provider only. Flag any clause where one party disclaims responsibility for the other (e.g., "Anthropic is not responsible for Bedrock or any other services it receives from AWS"; "Azure disclaims responsibility for OpenAI model outputs") and whether the counter-party's contract closes the gap. Do not review the two contracts in isolation. |

If `~/.claude/plugins/config/claude-for-legal/governanca-ia/CLAUDE.md` doesn't define a position for a term on this list, ask: "Your playbook doesn't cover [term]. What's your default position, your acceptable fallback, and your automatic no? I'll add it to `~/.claude/plugins/config/claude-for-legal/governanca-ia/CLAUDE.md` so the next review is consistent."

---

## Playbook comparison

For each term above, compare what we found to the positions in `~/.claude/plugins/config/claude-for-legal/governanca-ia/CLAUDE.md`.

**Output format for each term:**

> **[Term name]**
> 🟢 / 🟡 / 🟠 / 🔴
> **Vendor says:** [summary of what the contract actually says]
> **Our position:** [from `~/.claude/plugins/config/claude-for-legal/governanca-ia/CLAUDE.md`]
> **Gap:** [specific delta — or "Aligned"]
> **Proposed fix:** [specific redline language, or "escalate — outside fallback"]

Use the severity ratings consistently (calibrated against `~/.claude/plugins/config/claude-for-legal/governanca-ia/CLAUDE.md` positions):

- 🟢 **Aligned** — at or better than the standard position in the playbook.
- 🟡 **Note** — within fallback but worse than standard; flag for awareness, not a blocker.
- 🟠 **Significant** — outside standard position but within fallback; needs redline before signing.
- 🔴 **Critical** — outside fallback; deployment should not proceed without resolution. Escalate per `~/.claude/plugins/config/claude-for-legal/governanca-ia/CLAUDE.md`.

---

## AI addendum gap check

**If the vendor has a DPA but no AI addendum:**

> "There's a DPA in place but no AI-specific addendum. The DPA covers data protection
> obligations but doesn't address: training on our data, model change notification,
> liability for AI outputs, or incident notification for AI system failures.
>
> For a [Standard / Elevated / High] tier use case, this gap is [acceptable at
> Standard tier / a blocker at Elevated or High tier]. Recommend requesting an
> AI addendum or at minimum negotiating AI-specific terms into the next renewal."

**If there are no AI terms at all:**

> "There are no AI-specific terms in this agreement. The vendor is providing an
> AI-powered service under general service terms — which means we have no
> contractual protection on the highest-risk AI governance items (training, liability,
> model changes). This is a 🔴 for any Elevated or High tier use case."

---

## AI policy consistency check

Cross-check the vendor's terms against our AI policy commitments in `~/.claude/plugins/config/claude-for-legal/governanca-ia/CLAUDE.md`.

Common conflicts:
- Our policy prohibits vendor training on our data — the vendor's terms permit it by
  default. (Contract needs explicit prohibition or opt-out confirmation.)
- Our policy requires human review for certain use cases — vendor's terms say AI outputs
  are final. (Workflow needs to impose the human step, not the vendor terms.)
- Our approved vendor list doesn't include this vendor — or blocklist does.
- Our policy requires disclosure to affected parties — vendor's terms impose a
  confidentiality obligation on AI system capabilities that would prevent disclosure.

Flag every mismatch. One of them has to change.

---

## Redline granularity

**Edit at the smallest possible granularity.** A redline is a negotiation artifact, not a rewrite. Wholesale clause replacement signals "we threw out your drafting" — it's aggressive, it forces the counterparty to re-read the whole clause, and it discards the parts of their drafting that were fine. Surgical redlines — strike a word, insert a phrase, restructure a subclause — signal "we have specific asks" and are faster to read, understand, and accept.

Default to the smallest edit that achieves the playbook position:
- Replace a **word** before a phrase. ("twelve (12)" → "twenty-four (24)")
- Replace a **phrase** before a sentence. ("paid by the Buyer" → "paid and payable by the Buyer")
- Restructure a **subclause** before replacing the sentence. (Add "(a)" and "(b)" to split a compound condition.)
- Replace a **sentence** before replacing the clause.
- Only replace a **whole clause** when the counterparty's version is so far from your position that surgical edits would be harder to read than a fresh draft — and when you do, say so in the transmittal: "We've replaced §8.2 rather than marking it up because the changes were extensive. Happy to walk you through the delta."

When in doubt, smaller. A client who receives a surgical redline trusts that you read carefully. A client who receives a wholesale replacement wonders whether you read at all.

## Output

**Before recommending signature of a vendor AI agreement (the version the company will execute):** Read `## Who's using this` in `~/.claude/plugins/config/claude-for-legal/governanca-ia/CLAUDE.md`. If the Role is Non-lawyer:

> Signing this vendor AI agreement has legal consequences. Have you reviewed this with an attorney? If yes, proceed. If no, here's a brief to bring to them:
>
> [Generate a 1-page summary: the vendor and the use case, the key terms reviewed (data use, liability, auditability, model change, human review), where vendor positions diverge from policy, what's being accepted, what could go wrong, what to ask the attorney.]
>
> If you need to find an attorney, solicitor, barrister, or other authorised legal professional: your professional regulator's referral service is the fastest starting point (state bar in the US, SRA/Bar Standards Board in England & Wales, Law Society in Scotland/NI/Ireland/Canada/Australia, or your jurisdiction's equivalent).

Do not proceed past this gate without an explicit yes. Review/redline drafts for attorney consideration do not require the gate — signature does.

```markdown
[WORK-PRODUCT HEADER — per plugin config ## Outputs — differs by role; see `## Who's using this`]

*This review is derived from vendor contract terms that are typically confidential under NDA, and it may itself be privileged. It inherits the source's confidentiality and privilege status. Distributing it beyond the privilege circle (e.g., forwarding to the vendor, sharing in an open channel) can waive privilege and breach the NDA. Mark, store, and route accordingly.*

# Vendor AI Review: [Vendor Name]

**Document reviewed:** [AI addendum / main agreement AI provisions / ToS]
**Reviewed:** [date]
**Use case(s):** [what we're deploying this vendor's AI for]
**Governance tier:** [Standard / Elevated / High]

---

## Bottom line

[Two sentences. Can we deploy under these terms? What has to change first?]

**Issues:** [N]🔴 [N]🟠 [N]🟡 [N]🟢

---

## Term-by-term

[For each term above — vendor position, our position, gap, severity, proposed fix]

---

## AI addendum status

[Present / Absent — and what that means for this deployment]

---

## AI policy consistency

[🟢 Consistent | 🟡 Flags: list]

---

## Recommended redlines

[Consolidated draft redlines. Review with counsel before sending externally. For critical
issues where no fallback exists, flag for escalation rather than proposing language.]

---

## If they won't move

[For each 🔴 and 🟠: the fallback from `~/.claude/plugins/config/claude-for-legal/governanca-ia/CLAUDE.md`, or "escalate — outside fallback"
and routing per escalation table]
```

---

## Practical notes

**The training-on-data clause is the one most people miss.**
Vendor AI terms have historically varied widely on whether API inputs can be used
to train or improve models — some vendors permit it by default, others prohibit it,
and many have changed their position over time. Do not assume any particular vendor's
current stance without reading the specific agreement in front of you. This is almost
always the most important term for any company with confidential or sensitive data,
and it must be confirmed in writing, not assumed from reputation or prior experience.

**Map the AI stack.** Modern AI deployments are layered. Before reviewing terms, map the layers:
1. **End-user SaaS application** (e.g., a legal tech tool, a CRM with AI scoring, a document assistant) — the tool your org signs up for
2. **API gateway / orchestration layer** (e.g., Azure OpenAI Service, AWS Bedrock, Google Vertex, LangChain-hosted) — often invisible, always has its own terms
3. **Model provider** (e.g., Anthropic, OpenAI, Google, Meta) — the LLM
4. **Hosted knowledge base / RAG source** (e.g., a vector database, a third-party data corpus, a retrieval service) — the data Claude reads from
5. **Additional subprocessors** — analytics, logging, fine-tuning partners

Ask: "Walk me through the stack — what does [SaaS tool] use under the hood? Is it built on a cloud AI service? Does it call a model provider directly or through a gateway? Does it use a hosted knowledge base?" Then review terms at EACH layer, not just the top.

Each handoff between layers is a flow-down risk. A commitment at layer 1 ("we won't train on your data") means nothing if layer 3's terms say otherwise and layer 1 never flowed the commitment down.

**Flow-down test.** For each flagged stacked-vendor term — especially training-on-data, data retention, subprocessor changes, and liability — don't just flag "check upstream terms." DO THE CHECK:

1. **Search the contract for flow-down language.** Look for: "subprocessor obligations no less protective than," "flow-down of data commitments," "back-to-back terms," "Provider shall ensure that its subprocessors are bound by," "equivalent obligations."
2. **If present:** Quote it, verify it covers the specific flagged term, and flag whether it's enforceable (who can enforce it — you, or only the intermediate vendor?).
3. **If absent:** Produce a specific redline requiring it:
   > "Add to §[X]: Provider shall ensure that any third-party model providers, infrastructure providers, or subprocessors used in delivering the Services are bound by obligations with respect to [Customer Data / AI training / data retention / confidentiality] no less protective than those set forth in this Agreement, and shall be responsible for any breach of this Agreement caused by such third parties."
4. **Flag the gap with a severity:** 🔴 if the term is training-on-data or liability and there's no flow-down; 🟡 if the term is less sensitive or there's partial flow-down.

"Escalate and check upstream" is where compliance dies. Produce the test and the redline.

**Acceptable use policies flip the frame.**
AUPs tell you what you can't do; they don't tell you what the vendor can do.
Don't let a clean AUP review substitute for reading the data use and liability terms.

**Renewals are leverage points.**
If the current agreement is unfavorable and the vendor won't renegotiate mid-term,
document the gaps now and flag them for the renewal. Flag to procurement:
"This renewal should not close without AI addendum addressing [list]."

**Builder context adds a layer.**
If the company is a builder using a vendor's model as a foundation, the vendor's terms
also govern what the company can offer its own customers. Some terms prohibit certain
downstream uses. Check use restrictions against the product roadmap, not just current
internal workflows.

---

## Close with the next-steps decision tree

End with the next-steps decision tree per CLAUDE.md `## Outputs`. Customize the options to what this skill just produced — the five default branches (draft the X, escalate, get more facts, watch and wait, something else) are a starting point, not a lock-in. The tree is the output; the lawyer picks.

## What this skill does not do

- It doesn't review the DPA provisions of the same agreement — run
  `/privacidade:dpa-review`, if the plugin is installed, for that.
- It doesn't decide whether to accept terms outside the fallbacks. It routes those
  per the escalation table in `~/.claude/plugins/config/claude-for-legal/governanca-ia/CLAUDE.md`.
- It doesn't evaluate vendor security posture beyond what's in the agreement —
  that's a security team function.
