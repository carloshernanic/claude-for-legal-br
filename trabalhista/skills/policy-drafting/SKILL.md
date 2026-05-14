---
name: policy-drafting
description: >
  Redige política/norma interna ou cláusula de regulamento com suplementos
  por UF/categoria sindical/CCT/ACT onde a lei diverge no footprint. Use
  quando o(a) usuário(a) disser "redigir política de [tema]", "precisamos de
  política sobre", "atualizar nossa política de [tema]", ou nomear uma lacuna
  no regulamento interno.
argument-hint: "[tema da política — ex.: 'teletrabalho CLT 75-A', 'licença-maternidade Empresa Cidadã', 'férias', 'banco de horas']"
---

> **Adaptação BR:** Skill adaptada do regime US para o regime brasileiro. Mapeamentos-chave:
> - **State supplements (CA/NY/etc.)** → suplementos por **UF + categoria sindical + base territorial + CCT/ACT vigente** (a unidade de variação no BR é múltipla — política deve indicar regra geral CLT + ajustes onde CCT específica diverge nos pontos do art. 611-A — Reforma 13.467/2017).
> - **Handbook (US — implied contract risk)** → **regulamento interno** + **norma interna** + **política**. Atenção: **alteração unilateral lesiva é vedada (CLT art. 468)**; vantagem habitual paga por mais de 10 anos integra contrato (Súmula 51 TST + jurisprudência aplicada). Eficácia: regulamento publicado e amplamente divulgado vincula.
> - **Paid leave (CA/NY/CO PFL)** → licença-maternidade 120d (CF 7º XVIII) + extensão Empresa Cidadã 60d (Lei 11.770/2008); licença-paternidade 5d (ADCT 10 §1º) + 20d (Lei 11.770); auxílio-doença INSS (Lei 8.213/91).
> - **Parental leave (FMLA + state PFL)** → idem acima; adoção (Lei 12.873/2013 — licença-maternidade equiparada).
> - **Meal/rest breaks (CA penalty pay)** → CLT art. 71; supressão parcial = indenização do suprimido + 50% (Reforma 13.467/2017 — natureza indenizatória).
> - **Expense reimbursement (CA Lab. Code §2802)** → teletrabalho CLT 75-D (custos a serem regulados em contrato escrito); reembolso de despesas a serviço (jurisprudência: integra salário se habitual e sem comprovação).
> - **Pay transparency (state laws)** → Lei 14.611/2023; relatórios semestrais MTE (Decreto 11.795/2023); CLT 461 (igualdade salarial).
> - **Non-competes (state-by-state)** → jurisprudência TST: prazo + geografia + contrapartida. CCT pode regular.
> - **Final pay timing** → CLT 477 §6º (10 dias).
> - **At-will language ("this is not a contract")** → **NÃO USE NO BR.** Regulamento interno e políticas SÃO fonte de direito do contrato (CLT 444; jurisprudência) — não dá para se eximir com cláusula "não é contrato". Pode-se dizer que é norma interna sujeita a alteração mediante regras do CLT 468.
> - **State-mandated handbook policies** → exigências legais BR a integrar: política de prevenção ao assédio (Lei 14.457/2022 — CIPA passa a se chamar CIPA+A); canal de denúncia (Lei 13.608/2018); LGPD (Lei 13.709/2018) — política de privacidade do(a) empregado(a); NRs do MTE aplicáveis (NR-1 GRO, NR-5 CIPA, NR-7 PCMSO etc.); igualdade salarial (Lei 14.611/2023).
> - **State** → UF + categoria sindical + CCT/ACT. Sempre cheque se a CCT da categoria já regula o tema — política não pode ser menos favorável ao(à) empregado(a) (CLT 444).

# /policy-drafting

1. Load `~/.claude/plugins/config/claude-for-legal/trabalhista/CLAUDE.md` → jurisdictional footprint, handbook location.
2. Use the workflow below.
3. Draft core policy. Check each jurisdiction in footprint for required variants.
4. Output: core policy + state supplements. Flag where law is currently shifting.

---

## Matter context

**Matter context.** Check `## Matter workspaces` in the practice-level CLAUDE.md. If `Enabled` is `✗` (the default for in-house users), skip the rest of this paragraph — skills use practice-level context and the matter machinery is invisible. If enabled and there is no active matter, ask: "Which matter is this for? Run `/trabalhista:matter-workspace switch <slug>` or say `practice-level`." Load the active matter's `matter.md` for matter-specific context and overrides. Write outputs to the matter folder at `~/.claude/plugins/config/claude-for-legal/trabalhista/matters/<matter-slug>/`. Never read another matter's files unless `Cross-matter context` is `on`.

---

## Purpose

A policy that's right for California may be wrong (or unnecessary) in Texas. This skill drafts a core policy and generates state supplements where the footprint requires different rules.

## Load context

`~/.claude/plugins/config/claude-for-legal/trabalhista/CLAUDE.md` → jurisdictional footprint, handbook location and format.

## Workflow

### Step 1: Scope the policy

- What's the policy for? (Remote work, parental leave, social media, etc.)
- Why now? (Legal requirement, incident, growth, gap noticed)
- Who does it apply to? (All employees, certain roles, certain locations)

### Step 2: Jurisdictional scan

For each state/country in the footprint, check: does this jurisdiction have a specific rule on this topic?

**Common topics with jurisdictional variance:**

| Topic | Variance |
|---|---|
| Paid leave | State mandates (CA, NY, CO, WA, etc.) with different accrual rates, uses, carryover |
| Parental leave | State programs layer on top of FMLA (CA PFL, NY PFL, etc.) |
| Meal and rest breaks | CA is the outlier (penalty pay); most states minimal |
| Expense reimbursement | CA requires; most states don't |
| Pay transparency | Growing list of states requiring ranges in postings |
| Non-competes | See hiring-review skill — unenforceable in some states |
| Final pay | Timing varies widely |

If the topic has no jurisdictional variance (dress code, say), skip this step.

### Step 3: Draft the core policy

One policy. Applies everywhere. Clear and readable — employees should understand it without a lawyer.

Structure:
- Purpose (one sentence — why this policy exists)
- Scope (who it applies to)
- The rule (what's required/permitted/prohibited)
- Process (how to request, who approves, what happens if)
- Questions (who to ask)

Avoid: "heretofore," "notwithstanding," nested exceptions. This is a handbook policy, not a contract.

### Step 4: State supplements

For each jurisdiction where the rule differs, a supplement:

```markdown
### [State] Supplement

Employees working in [State] are subject to the following in addition to / instead of the core policy:

- [Specific difference]
- [Cite the state law if helpful]
```

Keep supplements tight. Only what's different — don't repeat the core.

### Step 5: Cross-check

- Does this policy conflict with anything already in the handbook?
- Does it promise more than the company intends to deliver? (A policy is a promise — courts hold employers to handbook promises.)
- Does it inadvertently create a contract? (Some states treat handbook policies as contractual — include the standard "this is not a contract" language if the handbook doesn't already.)

## Output

```markdown
# [Policy Name]

## Core Policy

[Full text]

## State Supplements

### [State 1]
[Supplement]

### [State 2]
[Supplement]

---

## Drafting Notes (internal — remove before handbook insertion)

- **Jurisdictional scan:** [which states checked, which have variance]
- **Conflicts with existing handbook:** [none | list]
- **Law currently shifting:** [any state where this is in flux]
- **Review cadence:** [when to revisit — annual, or when X happens]
```

> **Draft, not a policy in effect.** This is a drafting aid for attorney review, not a policy you can publish. Publishing a handbook policy has legal consequences — in several states it can bind the company as a contractual promise, and wage/leave/accommodation policies are routinely read against the employer. A licensed attorney, solicitor, barrister, or other authorised legal professional in your jurisdiction reviews, edits as needed, and takes professional responsibility before the policy is rolled out. Do not publish or distribute this draft unreviewed.

## Handoff

To handbook-updates skill: when this policy is approved, it diffs against the current handbook and flags what changes.

## What this skill does not do

- Approve the policy. It drafts; a human approves.
- Roll out the policy. Communication to employees is an HR workflow.
- Cover every jurisdiction on earth — only the ones in the footprint. If the footprint expands, re-run.
