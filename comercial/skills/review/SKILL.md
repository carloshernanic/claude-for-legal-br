---
name: review
description: >
  Review a vendor agreement, NDA, or SaaS subscription against your playbook.
  Identifies the agreement structure from titles, routes to the right review skill
  (vendor-agreement-review, nda-review, saas-msa-review), and integrates the output
  into a single memo. Use when the user says "review this contract", "check this
  MSA", "is this NDA okay", "look at this SaaS agreement", or attaches an inbound
  agreement for review.
argument-hint: '[file path | Drive link | [CLM ID] | paste text]'
---

# /review

**Adaptação BR.** Plugin adaptado ao Brasil (CC Livros II/III, CDC, LGPD, Lei 9.307/1996, LINDB, EOAB). Antes de qualquer revisão: identifique o regime — B2B (Código Civil), consumo (CDC Lei 8.078/1990 — atenção ao art. 51 sobre cláusulas nulas), ou adesão B2B (CC art. 423). A resposta muda quais cláusulas são negociáveis vs. nulas.

Revisa um contrato recebido contra o playbook em `~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md`. Identifica a estrutura por títulos, seleciona a(s) skill(s), e — se confirm_routing estiver ligado — checa com o usuário antes de prosseguir.

## Instructions

1. **Carregue `~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md`.** Se placeholders presentes, pare e diga: "Rode `/comercial:cold-start-interview` primeiro — preciso aprender seu playbook antes de revisar contra ele."

   **Identifique o regime contratual primeiro.** Antes de aplicar o playbook, classifique: B2B paritário (CC), B2B adesão (CC art. 423), consumo (CDC), ou misto. Em consumo, cláusulas do art. 51 do CDC são nulas de pleno direito e o playbook é irrelevante para essas cláusulas — você as rejeita, não negocia.

   Also read `~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md` → `## Review preferences` → `confirm_routing`. If the field is missing, treat it as `true`.

2. **Get the agreement:** From file path, Drive link, [CLM ID], or pasted text. If none provided, ask.

3. **Read the document structure — titles first.**

   Before reading the body, extract:
   - The main agreement title (e.g., "Master Services Agreement", "Non-Disclosure Agreement")
   - All exhibit, schedule, addendum, and attachment titles (e.g., "Exhibit A — Data Processing Addendum", "Schedule 1 — Subscription Order Form", "Annex B — Service Level Agreement")

   This is the routing signal. Do not rely on body keywords alone — a 40-page MSA with "confidential" throughout is not an NDA.

4. **Select the skill(s) based on document structure.**

   Map each identified document or section to a skill:

   | Título do documento / seção contém | Skill |
   |---|---|
   | NDA, Termo/Acordo de Confidencialidade, Non-Disclosure (como contrato principal) | **nda-review** |
   | Contrato de Prestação de Serviços, MSA, Master Services, Ordem de Serviço, Consultoria | **vendor-agreement-review** |
   | Assinatura, SaaS, Cloud, Software como Serviço, Pedido com renovação automática, Licença com fees recorrentes | **saas-msa-review** (overlay sobre vendor-agreement-review) |
   | Anexo LGPD, DPA, Acordo de Tratamento de Dados (anexo ou standalone) | nota para **vendor-agreement-review** → seção LGPD |
   | Acordo de Nível de Serviço, SLA (como anexo) | nota para **saas-msa-review** → seção SLA |

   Multiple skills may apply. Common combinations:
   - MSA + DPA exhibit → vendor-agreement-review, with DPA noted
   - SaaS subscription + Order Form + SLA exhibit → saas-msa-review (covers all three)
   - MSA + Order Form with auto-renewal → vendor-agreement-review + saas-msa-review overlay

   When the structure is genuinely ambiguous after reading titles (e.g., a document titled "Agreement" with no exhibits listed), read the first two pages of the body to resolve it — then stop and route.

5. **Confirm routing if enabled.**

   If `confirm_routing` is `true` in `~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md` (or field is absent):

   ```
   I'm going to review this as: [agreement type(s)].

   Documents identified:
   - [Main agreement title] → [skill]
   - [Exhibit A title] → [how it will be handled]
   - [Exhibit B title] → [how it will be handled]

   Sound right? (yes / no — or tell me what I got wrong)
   ```

   Wait for confirmation before proceeding. If the user corrects the routing, apply their instruction and proceed.

   If `confirm_routing` is `false`: proceed silently. Log the routing decision at the top of the review memo so the user can see what was applied.

6. **Run the skill(s).** Follow each skill's workflow fully. If multiple skills apply, run them in sequence and integrate the output into a single memo — don't produce separate memos.

7. **Check for escalations:** If any issue exceeds the reviewer's authority per the `~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md` matrix, invoke **escalation-flagger** to route and draft the ask.

8. **Offer follow-ups:**
   - Stakeholder summary for the business owner
   - Redline .docx with tracked changes
   - [CLM] record creation (if connected)
   - Add to renewal register (if auto-renewal found)

## Configuring confirm_routing

Add to `~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md` → `## Review preferences`:

```markdown
## Review preferences

confirm_routing: true   # Set to false to skip routing confirmation and proceed automatically
```

The cold-start interview should ask about this preference. Default is `true` — confirmation on. As trust builds, the user can set it to `false`.

## Examples

```
/comercial:review vendor-msa.pdf
```

```
/comercial:review https://drive.google.com/file/d/ABC123
```

```
/comercial:review
[paste agreement text]
```

## Output

Full review memo per the skill's format. Routing decision logged at the top. Deviation-by-deviation, specific redline language, named approver. Saved where `~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md` → House style says work product goes.
