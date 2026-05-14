---
name: customize
description: >
  Customização guiada do perfil do NPJ — mudar uma coisa sem rerodar o
  cold-start completo. Ajustar perfil do NPJ, jurisdição (comarca, foros),
  modelo de supervisão, templates por área, configuração do semestre ou
  salvaguardas de output. Use quando o(a) usuário(a) disser "mudar
  [coisa]", "novo semestre", "adicionar uma área", "atualizar config" ou
  "customizar".
argument-hint: "[nome da seção, ou descreva o que quer mudar]"
---

> **Adaptação BR (não oficial).** Customização do CLAUDE.md do NPJ. Lembre dos institutos brasileiros ao calibrar áreas: família (CC + Maria da Penha 11.340/2006 + ECA + Alienação Parental 12.318/2010), inquilinato (Lei 8.245/91), consumidor (CDC + Lei 14.181/2021 superendividamento), trabalhista (CLT + jus postulandi até 2 SM CLT 791), previdenciário (Lei 8.213 + BPC LC 142/2013), criminal (Defensoria/dativo CPP 263 + reabilitação CP 93-95), migração/refúgio (Lei 13.445/2017 + 9.474/97 + CONARE), PCD (LBI Lei 13.146), idoso(a) (Estatuto Lei 10.741), LGBTQIA+ (ADO 26 STF), mediação (Lei 13.140/2015 + CEJUSCs). Jurisdição: comarca, vara, JEC (Lei 9.099 — 20 SM, mas até 40 SM com advogado(a)), JEF (10.259), Fazenda (12.153), TRT, Justiça Federal, Vara da Infância. Encaminhamentos: Defensoria (LC 80/94), Procon, CRAS/CREAS, DEAM, Conselho Tutelar, MP, CONARE. Sigilo: EOAB art. 7º XIX. Tags: [Defensoria], [Procon], [DEAM], [CREAS], [JusBrasil], [STJ], [STF], [TST], [Planalto], [LEXML].

# /customize

## When this runs

The user typed `/npj:customize`. They (usually the professor, sometimes
a student) want to change something in the clinic profile — a jurisdiction, a
supervision style, a practice-area template, a semester rollover — without
re-running the whole cold-start interview and without hand-editing YAML.

## What to do

1. **Read the config.** Read
   `~/.claude/plugins/config/claude-for-legal/npj/CLAUDE.md`.
   If the plugin config does not exist or still contains `[PLACEHOLDER]`
   values, say:

   > You haven't run setup yet. Run `/npj:cold-start-interview`
   > first — customize is for adjusting a profile you already have.

2. **Show the customizable map.** List what's in the profile, grouped, with a
   one-line summary of the current value:

   - **Clinic profile** — clinic name, host school, faculty lead, active
     practice areas, case type limits
   - **Jurisdiction** — primary state, courts, agencies, local rules path
   - **Supervision style** — informal vs. formal review queue; if formal,
     who reviews what before it goes out
   - **Practice-area templates** — which templates are active (immigration,
     housing, small business, family, expungement, etc.) and any local
     overrides
   - **Semester** — current semester, active students, rollover rules,
     handoff memo format
   - **Output safeguards** — plain-language standards for client-facing
     outputs, deadline warning rules, privilege labeling
   - **Seed documents** — clinic handbook, jurisdiction rules, template
     letters, sample memos, form libraries
   - **Outputs** — supervisor guide format, client letter templates, memo
     scaffolds
   - **Workflow** — case directories, deadline tracker location, review
     queue channel
   - **Integrations** — document storage / Slack / court e-filing status,
     fallbacks

3. **Ask what they want to change.**

   > What would you like to adjust? Pick a section, or describe the change in
   > your own words.

4. **Make the change.** Show the current value, ask for the new value, explain
   what changes downstream, confirm, write it to the config.

   Examples:
   - *Adding a new practice area:* "`/intake` will route matters of this
     type through the new template. `/draft`, `/memo`, and `/client-letter`
     will use the practice-area prompts. `/research-start` will add the
     corresponding Westlaw search terms."
   - *Supervision style informal → formal review queue:* "`/queue` becomes
     active — student output will land there for supervisor sign-off before
     it goes to the client."
   - *New semester rollover:* "I'll archive the prior semester's active
     cases, carry forward matters you flag as continuing, and prompt the
     incoming students through `/ramp`."

5. **Close.**

   > Done. Your next output will reflect the change. Anything else? You can
   > run `/npj:customize` anytime.

## Guardrails

- **Never delete a section.** If the user wants to "drop" a practice area,
  offer to mark it `[Archived]` and explain that archiving keeps case
  history accessible but hides the template from `/intake` routing.
- **Flag internal inconsistency.** If the change would make the profile
  inconsistent (e.g., formal review queue on + informal supervision note;
  or practice area on + no jurisdiction rules configured), flag the
  tension.
- **Flag guardrail degradation.** These are load-bearing and should not be
  removed: the "NOT final work product" framing on `/draft`, plain-language
  standards on client-facing outputs, "does NOT decide case acceptance" on
  `/intake`, "NOT substantive advice" on `/client-letter`, and the
  scaffold-not-analysis framing on `/memo`. These exist because students
  ship work product — if the safeguards go, the risk of student work
  reaching a client without supervisor review goes up. Confirm the
  trade-off with the user, and if they're a student rather than the
  professor, suggest they discuss it with the supervisor first.
- **One change at a time.** Don't re-ask the whole interview.
