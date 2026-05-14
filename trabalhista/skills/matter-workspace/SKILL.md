---
name: matter-workspace
description: >
  Gerencia workspaces de matéria — novo, listar, alternar, fechar ou destacar
  (nível-de-prática). Cria, lista, alterna, fecha e destaca a matéria ativa
  para que o contexto de um(a) cliente nunca vaze para outro(a). Use quando
  um(a) profissional com múltiplos clientes disser "nova matéria", "alternar
  matéria", "listar minhas matérias", "fechar esta matéria", ou precisa
  gerenciar qual matéria está ativa.
argument-hint: "<new | list | switch | close | none> [slug]"
---

# /matter-workspace

Profissionais trabalham com múltiplos clientes e matérias. Um workspace de matéria mantém o contexto de um cliente ou contratação separado de todos os outros. Esta skill gerencia esses workspaces.

## Subcomandos

- `/trabalhista:matter-workspace new <slug>` — criar novo workspace de matéria, rodar intake curto, gravar `matter.md`
- `/trabalhista:matter-workspace list` — listar matérias com status e flag de ativa
- `/trabalhista:matter-workspace switch <slug>` — definir a matéria ativa
- `/trabalhista:matter-workspace close <slug>` — arquivar matéria (mover para `~/.claude/plugins/config/claude-for-legal/trabalhista/matters/_archived/`, nunca deletar)
- `/trabalhista:matter-workspace none` — destacar de qualquer matéria ativa, trabalhar apenas nível-de-prática

## Instruções

1. Leia `~/.claude/plugins/config/claude-for-legal/trabalhista/CLAUDE.md` — confirme que a seção `## Workspaces de matéria` está populada. Se `Habilitado` for `✗`, diga ao(à) usuário(a): "Workspaces de matéria estão desligados — você está configurado(a) como prática in-house com um único cliente/empregador, então o plugin trabalha do contexto a nível de prática automaticamente. Se você na verdade trabalha com múltiplos clientes, rerode `/trabalhista:cold-start-interview --redo` e selecione uma configuração de prática privada. Caso contrário, você não precisa de `/matter-workspace` nada." Não dê erro — o estado desabilitado é o estado esperado para in-house.
2. Use a lógica de subcomandos abaixo.
3. Despache no primeiro token de `$ARGUMENTS`:
   - `new` → rode o intake, grave `~/.claude/plugins/config/claude-for-legal/trabalhista/matters/<slug>/matter.md`, semeie `history.md` e `notes.md`.
   - `list` → enumere `~/.claude/plugins/config/claude-for-legal/trabalhista/matters/*/matter.md`, imprima tabela, marque a matéria ativa.
   - `switch` → atualize a linha `Matéria ativa:` no CLAUDE.md a nível de prática.
   - `close` → mova `~/.claude/plugins/config/claude-for-legal/trabalhista/matters/<slug>/` para `~/.claude/plugins/config/claude-for-legal/trabalhista/matters/_archived/<slug>/`, registre data de fechamento em `history.md`.
   - `none` → defina `Matéria ativa:` como `none — apenas contexto nível-de-prática`.
4. Mostre o que mudou e confirme antes de gravar.

## Notas

- A skill nunca lê entre matérias salvo se `Contexto cross-matter` estiver `on` no CLAUDE.md a nível de prática.
- Arquivamento não é exclusão — matérias fechadas permanecem legíveis para retenção/conflitos.
- Slugs em minúsculas com hífens. Se um slug for reutilizado entre arquivada e ativa, a arquivada é preservada em `_archived/<slug>/`.

---

## Referência

Profissionais multi-cliente (prática privada — solo, banca pequena, escritório grande) trabalham em muitas matérias. Contexto de uma não pode vazar para outra. Esta skill é a camada fina de gerenciamento de arquivos que torna isso verdadeiro.

**Estado padrão é off.** In-house nunca veem isto — rodam apenas em nível-de-prática. Workspaces de matéria são ligados no cold-start para usuários de prática privada, ou editando `## Workspaces de matéria` no CLAUDE.md a nível de prática. Se `Habilitado` for `✗`, esta skill não roda; em vez disso, explica o estado desabilitado e sugere `/trabalhista:cold-start-interview --redo` para usuários que realmente precisam de isolamento de matéria.

## Layout de armazenamento

Todo dado de matéria vive sob:

```
~/.claude/plugins/config/claude-for-legal/trabalhista/
├── CLAUDE.md                       # perfil de prática nível-de-prática
└── matters/
    ├── <slug>/
    │   ├── matter.md               # cliente, contraparte, tipo de matéria, fatos-chave, overrides
    │   ├── history.md              # log datado de eventos, decisões, rascunhos, revisões
    │   ├── notes.md                # notas livres de trabalho
    │   └── outputs/                # saídas de skill para esta matéria (subpasta opcional)
    └── _archived/
        └── <slug>/                 # matérias fechadas — legíveis mas não ativas
```

Slugs em minúsculas com hífens. Exemplos: `acme-rescisao-2026`, `zenith-renovacao-cct`, `apuracao-assedio-rj`.

## Matéria ativa está no CLAUDE.md de prática

A linha `Matéria ativa:` sob `## Workspaces de matéria` no CLAUDE.md a nível de prática é a única fonte da verdade. Alternar matéria edita aquela linha. Sem arquivo de estado separado.

## Lógica de subcomandos

### `new <slug>`

1. Confirme que slug não está presente em `matters/<slug>/` ou `matters/_archived/<slug>/`. Se reutilizado, peça outro slug.
2. Rode o intake:
   - **Cliente** (parte que representamos, ou unidade de negócio interna se in-house)
   - **Contraparte** (o outro lado — pode ser múltiplas; em trabalhista: o(a) empregado(a) ou ex-empregado(a))
   - **Tipo de matéria** (leia o perfil de prática do plugin para categorias típicas; para trabalhista: admissão | rescisão | apuração interna | afastamento | acomodação/LBI | classificação CLT/PJ/terceirização | expansão internacional | projeto de política | reclamação trabalhista TRT | acordo extrajudicial CLT 855-B | outro)
   - **Nível de confidencialidade** (padrão | reforçado | clean-team — reforçado dispara cuidado extra em cross-matter)
   - **Fatos-chave** (2–5 sentenças: do que se trata, quem são os stakeholders, o que está em jogo)
   - **Overrides específicos da matéria sobre o playbook geral** (ex.: "cliente tem CCT mais restritiva que o padrão", "contraparte é parceiro estratégico — tom preservador de relação")
   - **Matérias relacionadas** (slugs de matérias conectadas)
3. Grave `matters/<slug>/matter.md` usando o template abaixo.
4. Semeie `matters/<slug>/history.md` com entrada única "Aberta".
5. Crie `matters/<slug>/notes.md` vazio.
6. Não alterne automaticamente para a nova matéria. Pergunte: "Quer alternar para `<slug>` agora? (`/trabalhista:matter-workspace switch <slug>`)"

### `list`

Enumere `matters/*/matter.md`. Leia front-matter ou primeiras linhas de cada arquivo para extrair status. Imprima tabela:

| Slug | Cliente | Tipo de matéria | Status | Aberta | Ativa |
|---|---|---|---|---|---|

Marque a matéria atualmente ativa com `*`. Inclua `_archived/*` sob heading "Arquivadas" separado, se houver.

### `switch <slug>`

1. Confirme que `matters/<slug>/matter.md` existe. Se não, ofereça `/trabalhista:matter-workspace new <slug>`.
2. Edite a linha `Matéria ativa:` no CLAUDE.md de prática para `Matéria ativa: <slug>`.
3. Mostre ao(à) usuário(a) o resumo do matter.md para confirmar que está na matéria certa.

### `close <slug>`

1. Confirme que `matters/<slug>/` existe.
2. Anexe entrada "Fechada" em `matters/<slug>/history.md` com a data de hoje.
3. Mova `matters/<slug>/` → `matters/_archived/<slug>/`.
4. Se a matéria fechada era a ativa, defina `Matéria ativa:` como `none — apenas contexto nível-de-prática`.

### `none`

Defina `Matéria ativa:` no CLAUDE.md de prática como `none — apenas contexto nível-de-prática`. Confirme com o(a) usuário(a).

## Template de `matter.md`

```markdown
[CABEÇALHO DE MATERIAL DE TRABALHO — conforme config do plugin ## Saídas — varia por papel; ver `## Quem está usando` no CLAUDE.md de prática]

# Matéria: [Cliente] — [breve descrição]

**Slug:** [slug]
**Aberta:** [AAAA-MM-DD]
**Status:** ativa
**Confidencialidade:** [padrão / reforçada / clean-team]

---

## Partes

**Cliente:** [nome]
**Contraparte:** [nome(s)]

## Tipo de matéria

[admissão | rescisão | apuração interna | afastamento | acomodação | classificação CLT/PJ | reclamação TRT | acordo CLT 855-B | outro — com racional de uma linha]

## Fatos-chave

[2–5 sentenças. Do que se trata. Quem são os stakeholders. O que está em jogo. O que torna isto diferente do playbook padrão.]

## Overrides específicos da matéria

*Qualquer desvio do playbook nível-de-prática que se aplica a esta matéria e somente a ela.*

- [ex.: "CCT FENABAN aplicável — jornada 6h, ATS PCD."]
- [ex.: "Tom: preservador de relação — contraparte é parceiro estratégico."]
- [ex.: "Foro: TRT-2 (São Paulo capital) por base territorial do sindicato."]

## Matérias relacionadas

- [slug — uma linha por que relacionada]

## Notas sobre confidencialidade

[Se reforçada ou clean-team, descreva por quê. Quem pode ver arquivos da matéria. Se contexto cross-matter é permissível mesmo que globalmente ligado.]
```

## Semente de `history.md`

```markdown
# Histórico: [Cliente] — [breve descrição]

Log append-only de eventos. Mais recente no topo.

---

## [AAAA-MM-DD] — Matéria aberta

Intake concluído. Slug: `[slug]`. Status: ativa.
[Qualquer contexto inicial digno de preservar além de matter.md — ex.: "Aberta em resposta a notificação extrajudicial do(a) ex-empregado(a)."]
```

## Contexto cross-matter

O CLAUDE.md a nível de prática tem flag `Contexto cross-matter:`. Quando `off` (default), uma skill trabalhando na matéria A **nunca lê** arquivos em `matters/B/` para qualquer outra `B`. Ponto. Esta é a garantia de confidencialidade que a configuração existe para prover.

Quando `on`, uma skill pode ler entre pastas de matéria apenas quando o(a) usuário(a) explicitamente pedir (ex.: "compare nossa posição sobre cláusulas restritivas nas últimas cinco rescisões executivas"). Mesmo com `on`, o default é carregar apenas a matéria ativa salvo se o(a) usuário(a) pedir visão cross-matter.

## O que esta skill não faz

- **Rodar checagem de conflitos.** Conflitos são responsabilidade do(a) profissional/escritório; o intake captura o que o(a) usuário(a) declara.
- **Forçar retenção.** Fechar arquiva uma matéria; não deleta. Política de retenção está fora de escopo.
- **Auto-rotear saídas.** A skill substantiva decide onde gravar; esta diz *qual pasta* está ativa, não o que pôr nela.
- **Decidir se cross-matter é apropriado.** Lê a flag e obedece.
