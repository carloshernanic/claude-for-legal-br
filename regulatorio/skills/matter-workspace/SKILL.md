---
name: matter-workspace
description: Gerencie matter workspaces — criar, listar, trocar, fechar, ou desvincular a matter ativa (practice-level). Use quando trabalhar entre múltiplos clientes ou matters e for preciso manter contexto de um engagement separado do outro, ou quando uma skill substantiva precisar saber em qual matter está trabalhando.
argument-hint: "<new | list | switch | close | none> [slug]"
---

> **Adaptação BR não oficial.** Calibrado para prática privada brasileira (advocacia — solo, banca pequena, banca grande), sob sigilo profissional do(a) advogado(a) (EOAB art. 7º, XIX; CPC art. 388).

# /matter-workspace

Profissionais trabalham entre múltiplos clientes e matters. Um matter workspace mantém o contexto de um cliente ou engagement separado de qualquer outro. Esta skill gerencia esses workspaces.

## Subcomandos

- `/regulatorio:matter-workspace new <slug>` — criar novo matter workspace, rodar intake curta, escrever `matter.md`
- `/regulatorio:matter-workspace list` — listar matters com status e flag de ativa
- `/regulatorio:matter-workspace switch <slug>` — setar a matter ativa
- `/regulatorio:matter-workspace close <slug>` — arquivar matter (mover para `~/.claude/plugins/config/claude-for-legal/regulatorio/matters/_archived/`, nunca deletar)
- `/regulatorio:matter-workspace none` — desvincular de qualquer matter ativa, trabalhar apenas em practice-level

## Instruções

1. Leia `~/.claude/plugins/config/claude-for-legal/regulatorio/CLAUDE.md` — confirme se a seção `## Matter workspaces` está populada. Se `Enabled` for `✗`, diga ao usuário: "Matter workspaces estão off — você está configurado(a) como prática in-house com um cliente, então o plugin trabalha em practice-level automaticamente. Se você na verdade trabalha entre múltiplos clientes, refaça `/regulatorio:cold-start-interview --redo` e selecione um ambiente de prática privada. Caso contrário, você não precisa de `/matter-workspace` para nada." Não erre — o estado desabilitado é o esperado para usuários in-house.
2. Use a lógica de file-management abaixo.
3. Despache no primeiro token de `$ARGUMENTS`:
   - `new` → rode a entrevista de intake, escreva `~/.claude/plugins/config/claude-for-legal/regulatorio/matters/<slug>/matter.md`, semeie `history.md` e `notes.md`.
   - `list` → enumere `~/.claude/plugins/config/claude-for-legal/regulatorio/matters/*/matter.md`, imprima tabela, marque a matter ativa.
   - `switch` → atualize a linha `Active matter:` no CLAUDE.md practice-level.
   - `close` → mova `~/.claude/plugins/config/claude-for-legal/regulatorio/matters/<slug>/` para `~/.claude/plugins/config/claude-for-legal/regulatorio/matters/_archived/<slug>/`, log da data de fechamento em `history.md`.
   - `none` → set `Active matter:` para `none — practice-level context only`.
4. Mostre ao usuário o que mudou e confirme antes de escrever.

## Notas

- A skill nunca lê entre matters a menos que `Cross-matter context` esteja `on` no CLAUDE.md practice-level.
- Arquivamento não é deleção — matters fechadas permanecem legíveis para fins de retenção/conflito (EOAB art. 35; OAB Provimento 188/2018 sobre conflito de interesses).
- Slugs em minúsculas com hifens. Se um slug for reusado entre arquivada e ativa, a arquivada é preservada em `_archived/<slug>/`.

---

Profissionais multi-cliente (prática privada — solo, banca pequena, banca grande) trabalham entre muitas matters. Contexto de uma não pode vazar para outra. Esta skill é a fina camada de file-management que torna isso real. **Sigilo profissional (EOAB art. 7º, XIX) e o dever de evitar conflito de interesses (OAB Provimento 188/2018) tornam o isolamento entre matters não apenas conveniência prática — é dever ético.**

**Estado padrão é off.** Usuários in-house nunca veem isto — rodam em practice-level apenas. Matter workspaces são ligadas no cold-start para usuários de prática privada, ou editando `## Matter workspaces` no CLAUDE.md practice-level. Se `Enabled` for `✗`, esta skill não roda; ela explica o estado desabilitado e sugere `/regulatorio:cold-start-interview --redo` para usuários que realmente precisam de isolamento entre matters.

## Layout de armazenamento

Todos os dados de matter ficam em:

```
~/.claude/plugins/config/claude-for-legal/regulatorio/
├── CLAUDE.md                       # perfil de prática practice-level
└── matters/
    ├── <slug>/
    │   ├── matter.md               # cliente, contraparte, tipo de matter, fatos-chave, overrides
    │   ├── history.md              # log datado de eventos, decisões, drafts, revisões
    │   ├── notes.md                # notas de trabalho livres
    │   └── outputs/                # outputs de skills para esta matter (subpasta opcional)
    └── _archived/
        └── <slug>/                 # matters fechadas — legíveis mas não ativas
```

Slugs em minúsculas com hifens. Exemplos: `acme-anpd-pas-2026`, `zenith-consulta-cvm-160`, `vendor-xyz-acordo-leniencia`.

## Matter ativa fica no CLAUDE.md practice-level

A linha `Active matter:` sob `## Matter workspaces` no CLAUDE.md practice-level é a fonte única de verdade. Trocar matter edita essa linha. Sem arquivo de estado separado.

## Lógica de subcomandos

### `new <slug>`

1. Confirme que slug não está presente em `matters/<slug>/` nem `matters/_archived/<slug>/`. Se reusado, peça outro slug.
2. Rode a entrevista de intake:
   - **Cliente** (a parte que representamos, ou a unidade de negócio interna se in-house)
   - **Contraparte** (o outro lado — pode ser múltiplas; em regulatório, frequentemente é o(a) regulador(a))
   - **Tipo de matter** (leia o perfil de prática do plugin para categorias típicas; para regulatorio: normatização | consulta pública | remediação de lacuna | ofício de agência | resposta a PAS — processo administrativo sancionador | tópico standing | outro)
   - **Nível de confidencialidade** (padrão | elevado | clean-team — elevado pede cuidado extra em settings cross-matter; em PAS de CVM/CADE/ANPD considere sempre elevado)
   - **Fatos-chave** (2–5 frases: do que esta matter trata, quem são os stakeholders, o que está em jogo)
   - **Overrides matter-específicos ao playbook da prática** (ex.: "cliente exige aviso 48h antes de qualquer comunicação a regulador", "contraparte é parceiro estratégico — tom preservador da relação")
   - **Matters relacionadas** (slugs de matters conectadas)
3. Escreva `matters/<slug>/matter.md` usando o template abaixo.
4. Semeie `matters/<slug>/history.md` com única entrada "Aberta".
5. Crie `matters/<slug>/notes.md` vazio.
6. **Não** trocar para a nova matter automaticamente. Pergunte: "Quer trocar para `<slug>` agora? (`/regulatorio:matter-workspace switch <slug>`)"

### `list`

Enumere `matters/*/matter.md`. Leia o front-matter ou primeiras linhas de cada arquivo para extrair status. Imprima tabela:

| Slug | Cliente | Tipo de matter | Status | Aberta | Ativa |
|---|---|---|---|---|---|

Marque a matter atualmente ativa com `*`. Inclua `_archived/*` sob cabeçalho separado "Arquivadas" se houver.

### `switch <slug>`

1. Confirme que `matters/<slug>/matter.md` existe. Se não, ofereça `/regulatorio:matter-workspace new <slug>`.
2. Edite a linha `Active matter:` no CLAUDE.md practice-level para `Active matter: <slug>`.
3. Mostre ao usuário o sumário do matter.md para que confirme estar na matter certa.

### `close <slug>`

1. Confirme que `matters/<slug>/` existe.
2. Acrescente entrada "Fechada" em `matters/<slug>/history.md` com data de hoje.
3. Mova `matters/<slug>/` → `matters/_archived/<slug>/`.
4. Se a matter fechada era a ativa, set `Active matter:` para `none — practice-level context only`.

### `none`

Set `Active matter:` no CLAUDE.md practice-level para `none — practice-level context only`. Confirme com o usuário.

## Template `matter.md`

```markdown
[WORK-PRODUCT HEADER — por config do plugin ## Outputs — varia por role; veja `## Who's using this` no CLAUDE.md practice-level]

# Matter: [Cliente] — [descrição curta]

**Slug:** [slug]
**Aberta:** [YYYY-MM-DD]
**Status:** ativa
**Confidencialidade:** [padrão / elevada / clean-team]

---

## Partes

**Cliente:** [nome]
**Contraparte:** [nome(s) — pode ser regulador em PAS]

## Tipo de matter

[normatização | consulta pública / tomada de subsídios | remediação de lacuna | ofício de agência | resposta a PAS | TAC / termo de compromisso / acordo de leniência | outro — com uma linha de justificativa]

## Fatos-chave

[2–5 frases. Do que esta matter trata. Quem são os stakeholders. O que está em jogo. O que a distingue do playbook padrão.]

## Overrides matter-específicos

*Qualquer desvio do playbook practice-level que se aplique a esta matter e apenas a ela.*

- [ex.: "Cap de responsabilidade: cliente exige 24 meses, não o padrão da casa de 12."]
- [ex.: "Tom: preservador da relação — contraparte é parceiro estratégico."]
- [ex.: "Lei aplicável: deve ser direito brasileiro (LINDB art. 9º), não Delaware."]

## Matters relacionadas

- [slug — uma linha de por que relacionada]

## Notas sobre confidencialidade

[Se elevada ou clean-team, descreva por quê. Quem pode ver arquivos da matter. Se cross-matter context é admissível mesmo com flag globalmente on. Em PAS (ANPD/CVM/CADE/ANS): considere clean-team se houver overlap com investigação concorrente. EOAB art. 7º, XIX protege documentos no exercício da profissão.]
```

## Semente `history.md`

```markdown
# History: [Cliente] — [descrição curta]

Log append-only. Mais recente no topo.

---

## [YYYY-MM-DD] — Matter aberta

Intake concluída. Slug: `[slug]`. Status: ativa.
[Qualquer contexto inicial que valha preservar além do matter.md — ex.: "Aberta em resposta a ofício da ANPD recebido em [data]."]
```

## Cross-matter context

O CLAUDE.md practice-level tem flag `Cross-matter context:`. Quando `off` (default), uma skill trabalhando na matter A **nunca lê** arquivos em `matters/B/` para qualquer outra `B`. Ponto. É a garantia de confidencialidade que o setting existe para dar.

Quando `on`, uma skill pode ler arquivos entre pastas de matters apenas quando o usuário explicitamente pedir (ex.: "compare nossa posição sobre limitação de responsabilidade nas últimas cinco matters de vendor"). Mesmo quando `on`, o padrão é carregar apenas a matter ativa a menos que o usuário peça view cross-matter.

## O que esta skill não faz

- **Roda checagem de conflito.** Conflito é tarefa do(a) profissional/banca (OAB Provimento 188/2018); a intake captura o que o usuário declara.
- **Aplica retenção.** Fechamento arquiva uma matter; não deleta. Política de retenção está fora de escopo.
- **Roteia outputs automaticamente.** A skill substantiva decide onde escrever; esta skill diz a ela *qual pasta* está ativa, não o que pôr nela.
- **Decide se cross-matter é apropriado.** Lê a flag e obedece.
