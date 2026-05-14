---
name: matter-workspace
description: >
  Gerencie workspaces de matéria — novo, listar, trocar, fechar ou destacar
  (nível de prática). Use quando um(a) profissional multi-cliente precisa criar
  uma matéria, trocar a matéria ativa, listar matérias, arquivar matéria ou
  destacar para contexto de nível de prática, ou quando outra skill precisa
  saber em qual matéria está trabalhando.
argument-hint: "<new | list | switch | close | none> [slug]"
---

# /matter-workspace

**Adaptação BR.** Plugin adaptado ao Brasil. Workspaces de matéria respeitam sigilo profissional (EOAB art. 7º, XIX) — leituras cross-matter exigem flag explícito.

Profissionais trabalham por vários clientes e matérias. Um workspace de matéria mantém o contexto de um cliente ou engajamento separado de todos os outros. Este comando gerencia esses workspaces.

## Subcomandos

- `/comercial:matter-workspace new <slug>` — cria novo workspace, roda intake curto, escreve `matter.md`
- `/comercial:matter-workspace list` — lista matérias com status e flag de ativa
- `/comercial:matter-workspace switch <slug>` — define a matéria ativa
- `/comercial:matter-workspace close <slug>` — arquiva matéria (move para `~/.claude/plugins/config/claude-for-legal/comercial/matters/_archived/`, nunca deleta)
- `/comercial:matter-workspace none` — destaca de qualquer matéria ativa, trabalha só em nível de prática

## Instruções

1. Leia `~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md` — confirme que a seção `## Workspaces de matéria` está populada. Se `Habilitado` é `✗`, diga: "Workspaces de matéria estão desligados — você está configurado(a) como prática in-house com um cliente, então o plugin trabalha em contexto de nível de prática automaticamente. Se você efetivamente trabalha por vários clientes, refaça `/comercial:cold-start-interview --redo` e selecione modalidade de advocacia privada. Senão, não precisa de `/matter-workspace`." Não erre — o estado desligado é o esperado para in-house.
2. Use a lógica de subcomando abaixo.
3. Despache no primeiro token de `$ARGUMENTS`:
   - `new` → rode entrevista de intake, escreva `~/.claude/plugins/config/claude-for-legal/comercial/matters/<slug>/matter.md`, semeie `history.md` e `notes.md`.
   - `list` → enumere `~/.claude/plugins/config/claude-for-legal/comercial/matters/*/matter.md`, imprima tabela, marque a matéria ativa.
   - `switch` → atualize a linha `Matéria ativa:` no CLAUDE.md de nível de prática.
   - `close` → mova `~/.claude/plugins/config/claude-for-legal/comercial/matters/<slug>/` para `~/.claude/plugins/config/claude-for-legal/comercial/matters/_archived/<slug>/`, logue a data de fechamento em `history.md`.
   - `none` → defina `Matéria ativa:` como `nenhuma — só contexto de nível de prática`.
4. Mostre ao usuário o que mudou e confirme antes de escrever.

## Notas

- A skill nunca lê cross-matter salvo se `Contexto cross-matter` estiver `ligado` no CLAUDE.md de nível de prática.
- Arquivar não é deletar — matérias fechadas permanecem legíveis para retenção/conflito de interesse (EOAB).
- Slugs são minúsculos com hífens. Se um slug for reusado entre arquivado e ativo, o arquivado é preservado em `_archived/<slug>/`.

---

Profissionais multi-cliente (advocacia privada — solo, banca pequena, banca grande) trabalham por muitas matérias. Contexto de uma não pode vazar para outra. Esta skill é a camada fina de gestão de arquivos que faz isto valer. **Isolamento entre matérias reflete dever profissional de sigilo (EOAB art. 7º, XIX; art. 34, VII) e impede conflito de interesse.**

**Estado padrão é desligado.** Usuários in-house nunca veem isto — rodam só em nível de prática. Workspaces ligam no cold-start para usuários de advocacia privada, ou editando `## Workspaces de matéria` no CLAUDE.md de nível de prática. Se `Habilitado` é `✗`, esta skill não roda; o workflow acima explica o estado desligado e sugere `/comercial:cold-start-interview --redo` para usuários que efetivamente precisam de isolamento.

## Storage layout

All matter data lives under:

```
~/.claude/plugins/config/claude-for-legal/comercial/
├── CLAUDE.md                       # practice-level practice profile
└── matters/
    ├── <slug>/
    │   ├── matter.md               # client, counterparty, matter type, key facts, overrides
    │   ├── history.md              # dated log of events, decisions, drafts, reviews
    │   ├── notes.md                # free-form working notes
    │   └── outputs/                # skill outputs for this matter (optional subfolder)
    └── _archived/
        └── <slug>/                 # closed matters — readable but not active
```

Slugs são minúsculos com hífens. Exemplos: `acme-msa-2026`, `zenith-renovacao`, `fornecedor-xyz-nda`.

## Matéria ativa fica no CLAUDE.md da prática

A linha `Matéria ativa:` sob `## Workspaces de matéria` no CLAUDE.md de nível de prática é a única fonte de verdade. Trocar de matéria edita essa linha. Sem arquivo de estado separado.

## Lógica de subcomando

### `new <slug>`

1. Confirme que o slug não está em `matters/<slug>/` nem em `matters/_archived/<slug>/`. Se reusado, peça outro.
2. Rode entrevista de intake:
   - **Cliente** (a parte que representamos, ou a unidade de negócio interna se in-house)
   - **Contraparte** (o outro lado — pode ser múltiplo)
   - **Tipo de matéria** (categorias típicas; em comercial: MSA fornecedor | contrato com cliente | NDA | assinatura SaaS | aditivo | renovação | outro)
   - **Nível de confidencialidade** (padrão | reforçado | clean-team — reforçado pede cuidado extra em cross-matter)
   - **Fatos-chave** (2–5 frases: do que se trata, quem são stakeholders, o que está em jogo)
   - **Overrides específicos da matéria sobre o playbook de prática** (ex.: "cliente exige cap de responsabilidade de 24 meses, não os 12 da casa"; "contraparte é parceira estratégica — tom preservativo de relação")
   - **Matérias relacionadas** (slugs de matérias conectadas)
3. Escreva `matters/<slug>/matter.md` com o template abaixo.
4. Semeie `matters/<slug>/history.md` com uma entrada "Aberta".
5. Crie `matters/<slug>/notes.md` vazio.
6. **Não** troque automaticamente para a nova matéria. Pergunte: "Quer trocar para `<slug>` agora? (`/comercial:matter-workspace switch <slug>`)"

### `list`

Enumere `matters/*/matter.md`. Leia front-matter ou primeiras linhas para extrair status. Imprima:

| Slug | Cliente | Tipo | Status | Aberta | Ativa |
|---|---|---|---|---|---|

Marque a matéria ativa com `*`. Inclua `_archived/*` sob "Arquivadas" se houver.

### `switch <slug>`

1. Confirme que `matters/<slug>/matter.md` existe. Se não, ofereça `/comercial:matter-workspace new <slug>`.
2. Edite `Matéria ativa:` no CLAUDE.md de nível de prática para `Matéria ativa: <slug>`.
3. Mostre o resumo do `matter.md` para o usuário confirmar que está na matéria certa.

### `close <slug>`

1. Confirme que `matters/<slug>/` existe.
2. Anexe entrada "Fechada" em `matters/<slug>/history.md` com data de hoje.
3. Mova `matters/<slug>/` → `matters/_archived/<slug>/`.
4. Se a matéria fechada era a ativa, defina `Matéria ativa:` como `nenhuma — só contexto de nível de prática`.

### `none`

Defina `Matéria ativa:` no CLAUDE.md de nível de prática como `nenhuma — só contexto de nível de prática`. Confirme com o usuário.

## Template `matter.md`

```markdown
[CABEÇALHO DE MATERIAL DE TRABALHO — conforme config do plugin ## Saídas — varia por papel; veja `## Quem está usando` no CLAUDE.md]

# Matéria: [Cliente] — [descrição curta]

**Slug:** [slug]
**Aberta:** [YYYY-MM-DD]
**Status:** ativa
**Confidencialidade:** [padrão / reforçada / clean-team]

---

## Partes

**Cliente:** [nome]
**Contraparte:** [nome(s)]

## Tipo de matéria

[MSA fornecedor | contrato com cliente | NDA | assinatura SaaS | aditivo | renovação | outro — com racional de uma linha]

## Fatos-chave

[2–5 frases. Sobre o que é a matéria. Quem são stakeholders. O que está em jogo. O que a torna diferente do playbook padrão.]

## Overrides específicos da matéria

*Qualquer desvio do playbook de nível de prática que se aplica a esta matéria e só a ela.*

- [ex.: "Cap de responsabilidade: cliente exige 24 meses, não os 12 da casa."]
- [ex.: "Tom: preservativo de relação — contraparte é parceira estratégica."]
- [ex.: "Lei aplicável: deve ser direito brasileiro com foro CAM-CCBC, não LCIA."]

## Matérias relacionadas

- [slug — uma linha sobre por que relacionada]

## Notas de confidencialidade

[Se reforçada ou clean-team, descreva por quê. Quem pode ver arquivos da matéria. Se contexto cross-matter é permissível mesmo com flag global ligado. Lembre EOAB art. 7º, XIX e art. 34, VII (conflito de interesse).]
```

## Seed do `history.md`

```markdown
# Histórico: [Cliente] — [descrição curta]

Log append-only. Mais recente no topo.

---

## [YYYY-MM-DD] — Matéria aberta

Intake completado. Slug: `[slug]`. Status: ativa.
[Qualquer contexto inicial que valha preservar além do matter.md — ex.: "Aberta em resposta a minuta de MSA recebida de [contraparte]."]
```

## Contexto cross-matter

O CLAUDE.md de nível de prática tem a flag `Contexto cross-matter:`. Quando `desligada` (padrão), uma skill trabalhando na matéria A **nunca lê** arquivos em `matters/B/` para qualquer outro `B`. Ponto. Esta é a garantia de sigilo profissional que a configuração serve para prover (EOAB art. 7º, XIX).

Quando `ligada`, a skill pode ler entre pastas de matéria só quando o usuário explicitamente pedir (ex.: "compare nossa posição em cap de responsabilidade nas últimas cinco matérias de fornecedor"). Mesmo `ligada`, o padrão é carregar só a matéria ativa salvo pedido cross-matter.

## O que esta skill NÃO faz

- **Roda checagem de conflito de interesse.** Conflitos são tarefa do(a) profissional / da banca; o intake captura o que o usuário declara.
- **Impõe retenção.** Fechar arquiva, não deleta. Política de retenção está fora de escopo.
- **Auto-rotea saídas.** A skill substantiva decide para onde escrever; esta skill diz *qual pasta* é ativa, não o que colocar nela.
- **Decide se cross-matter é apropriado.** Lê a flag e obedece.
