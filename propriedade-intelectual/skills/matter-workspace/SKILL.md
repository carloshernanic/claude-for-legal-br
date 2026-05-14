---
name: matter-workspace
description: >
  Gerencia workspaces de matéria — criar, listar, alternar, encerrar ou
  destacar a matéria ativa. Use em prática multi-cliente para manter o
  contexto de um cliente separado de outro, ou quando uma skill substantiva
  precisar saber em que matéria está trabalhando.
argument-hint: "<new | list | switch | close | none> [slug]"
---

# /matter-workspace

Praticantes operam em múltiplos clientes e matérias. Um workspace de matéria
mantém o contexto de um cliente ou engajamento separado de todos os outros.
Esta skill gerencia esses workspaces.

## Subcomandos

- `/propriedade-intelectual:matter-workspace new <slug>` — cria novo workspace de matéria,
  roda intake curto, grava `matter.md`
- `/propriedade-intelectual:matter-workspace list` — lista matérias com status e flag de
  ativa
- `/propriedade-intelectual:matter-workspace switch <slug>` — define a matéria ativa
- `/propriedade-intelectual:matter-workspace close <slug>` — arquiva matéria (move para
  `~/.claude/plugins/config/claude-for-legal/propriedade-intelectual/matters/_archived/`,
  nunca deleta)
- `/propriedade-intelectual:matter-workspace none` — destaca de qualquer matéria ativa,
  trabalha apenas em nível de prática

## Instruções

1. Leia `~/.claude/plugins/config/claude-for-legal/propriedade-intelectual/CLAUDE.md` —
   confirme que a seção `## Workspaces de matéria` está populada. Se
   `Habilitado` for `✗`, diga ao usuário: "Workspaces de matéria estão off —
   você está configurado como prática in-house com um único cliente, então o
   plugin trabalha a partir do contexto de nível de prática automaticamente.
   Se você efetivamente trabalha em múltiplos clientes, re-rode
   `/propriedade-intelectual:cold-start-interview --redo` e selecione um cenário de prática
   privada. Caso contrário, você não precisa de `/propriedade-intelectual:matter-workspace`
   de jeito nenhum." Não dê erro — o estado desabilitado é o esperado para
   usuários in-house.
2. Siga a lógica do subcomando abaixo.
3. Despacha pelo primeiro token de `$ARGUMENTS`:
   - `new` → roda a entrevista de intake, grava
     `~/.claude/plugins/config/claude-for-legal/propriedade-intelectual/matters/<slug>/matter.md`,
     semeia `history.md` e `notes.md`.
   - `list` → enumera
     `~/.claude/plugins/config/claude-for-legal/propriedade-intelectual/matters/*/matter.md`,
     imprime tabela, marca a matéria ativa.
   - `switch` → atualiza a linha `Matéria ativa:` no CLAUDE.md de nível de
     prática.
   - `close` → move
     `~/.claude/plugins/config/claude-for-legal/propriedade-intelectual/matters/<slug>/` para
     `~/.claude/plugins/config/claude-for-legal/propriedade-intelectual/matters/_archived/<slug>/`,
     registra data de encerramento em `history.md`.
   - `none` → define `Matéria ativa:` como `nenhuma — contexto de nível de
     prática apenas`.
4. Mostre ao usuário o que mudou e confirme antes de gravar.

## Notas

- A skill nunca lê entre matérias salvo se `Contexto cross-matéria` estiver
  `on` no CLAUDE.md de nível de prática.
- Arquivamento não é exclusão — matérias encerradas permanecem legíveis para
  fins de retenção/conflitos (importante porque o EOAB exige guarda de
  documentos por 5 anos após cessação do mandato, art. 34 XXI; e a guarda
  contábil/tributária pode ser maior).
- Slugs são minúsculas com hífens. Se um slug for reutilizado entre
  arquivada e ativa, a arquivada é preservada sob `_archived/<slug>/`.

---

Praticantes multi-cliente (prática privada — solo, banca pequena, grande
escritório) operam em muitas matérias. Contexto de uma não pode vazar para
outra. Esta skill é a fina camada de gestão de arquivos que torna isso
verdade.

**Estado default é off.** Usuários in-house nunca veem isto — rodam apenas em
nível de prática. Workspaces de matéria ligam no cold-start para usuários de
prática privada, ou editando `## Workspaces de matéria` no CLAUDE.md de
nível de prática. Se `Habilitado` for `✗`, esta skill não roda; em vez
disso, explica o estado desabilitado e sugere
`/propriedade-intelectual:cold-start-interview --redo` para usuários que realmente precisam
de isolamento de matéria.

## Layout de armazenamento

Todo dado de matéria vive sob:

```
~/.claude/plugins/config/claude-for-legal/propriedade-intelectual/
├── CLAUDE.md                       # perfil de prática nível-prática
└── matters/
    ├── <slug>/
    │   ├── matter.md               # cliente, contraparte, tipo de matéria, fatos-chave, overrides
    │   ├── history.md              # log datado de eventos, decisões, minutas, revisões
    │   ├── notes.md                # notas de trabalho livres
    │   └── outputs/                # saídas de skill para esta matéria (subpasta opcional)
    └── _archived/
        └── <slug>/                 # matérias encerradas — legíveis mas não ativas
```

Slugs são minúsculas com hífens. Exemplos: `acme-marca-2026`,
`zenith-remocao`, `novacorp-fto`.

## Matéria ativa fica no CLAUDE.md de prática

A linha `Matéria ativa:` sob `## Workspaces de matéria` no CLAUDE.md de
nível de prática é a única fonte de verdade. Alternar matéria edita aquela
linha. Sem arquivo de estado separado.

## Lógica de subcomando

### `new <slug>`

1. Confirme que o slug não está em `matters/<slug>/` nem em
   `matters/_archived/<slug>/`. Se reutilizado, peça outro slug.
2. Rode a entrevista de intake:
   - **Cliente** (a parte que representamos, ou unidade de negócio interna
     se in-house)
   - **Contraparte** (o outro lado — pode ser múltipla; pode ser "infrator
     terceiro desconhecido" para matérias disparadas por watch)
   - **Tipo de matéria** (leia o perfil de prática do plugin para categorias
     típicas; para propriedade-intelectual: clearance de marca | enforcement de marca |
     pedido de remoção judicial / notificação Marco Civil art. 21 | FTO de
     patente | infração de patente | revisão de cláusula de PI | compliance
     OSS | manutenção de portfólio | outro)
   - **Nível de confidencialidade** (padrão | reforçado | clean-team —
     reforçado pede cuidado extra em cenários cross-matéria; clean-team
     comum em trabalho de FTO de patente)
   - **Fatos-chave** (2–5 frases: do que se trata a matéria, quem são os
     stakeholders, o que está em jogo)
   - **Overrides específicos da matéria à postura de prática** (ex.:
     "cliente quer postura agressiva só para esta marca", "contraparte é
     parceiro estratégico — tom medido apenas", "inventor indisponível —
     não trazer para entrevista")
   - **Matérias relacionadas** (slugs de quaisquer matérias conectadas)
3. Grave `matters/<slug>/matter.md` usando o template abaixo.
4. Semeie `matters/<slug>/history.md` com uma única entrada "Aberta".
5. Crie `matters/<slug>/notes.md` vazio.
6. **Não** alterne automaticamente para a nova matéria. Pergunte: "Quer
   alternar para `<slug>` agora? (`/propriedade-intelectual:matter-workspace switch
   <slug>`)"

### `list`

Enumere `matters/*/matter.md`. Leia o front-matter ou as primeiras linhas de
cada arquivo para extrair status. Imprima tabela:

| Slug | Cliente | Tipo de matéria | Status | Aberta em | Ativa |
|---|---|---|---|---|---|

Marque a matéria atualmente ativa com `*`. Inclua `_archived/*` sob heading
"Arquivadas" se existirem.

### `switch <slug>`

1. Confirme que `matters/<slug>/matter.md` existe. Se não, ofereça
   `/propriedade-intelectual:matter-workspace new <slug>`.
2. Edite a linha `Matéria ativa:` no CLAUDE.md de nível de prática para
   `Matéria ativa: <slug>`.
3. Mostre ao usuário o resumo do matter.md para confirmar que está na
   matéria certa.

### `close <slug>`

1. Confirme que `matters/<slug>/` existe.
2. Adicione entrada "Encerrada" ao `matters/<slug>/history.md` com a data de
   hoje.
3. Mova `matters/<slug>/` → `matters/_archived/<slug>/`.
4. Se a matéria encerrada era a ativa, defina `Matéria ativa:` como
   `nenhuma — contexto de nível de prática apenas`.

### `none`

Defina `Matéria ativa:` no CLAUDE.md de nível de prática como
`nenhuma — contexto de nível de prática apenas`. Confirme com o usuário.

## Template `matter.md`

```markdown
[CABEÇALHO DE WORK-PRODUCT — conforme config do plugin ## Outputs — varia
por papel; vide `## Quem está usando` no CLAUDE.md de nível de prática]

# Matéria: [Cliente] — [descrição curta]

**Slug:** [slug]
**Aberta em:** [YYYY-MM-DD]
**Status:** ativa
**Confidencialidade:** [padrão / reforçado / clean-team]

---

## Partes

**Cliente:** [nome]
**Contraparte:** [nome(s)]

## Tipo de matéria

[clearance de marca | enforcement de marca | pedido de remoção judicial |
notificação Marco Civil art. 21 | FTO de patente | infração de patente |
revisão de cláusula de PI | compliance OSS | manutenção de portfólio | outro
— com racional de uma linha]

## Fatos-chave

[2–5 frases. Do que se trata. Quem são os stakeholders. O que está em jogo.
O que torna diferente da postura default.]

## Overrides específicos da matéria

*Qualquer desvio da postura de nível de prática que se aplica a esta matéria
e somente a ela.*

- [ex.: "Postura de enforcement: medida aqui mesmo que o default da casa
  seja agressivo — contraparte é parceiro de canal chave."]
- [ex.: "Aprovação para asserir: sign-off extra de marketing exigido antes
  de qualquer carta sair."]
- [ex.: "Clean-team: arquivos da matéria não legíveis mesmo com contexto
  cross-matéria on."]

## Matérias relacionadas

- [slug — uma linha do porquê relacionada]

## Notas sobre confidencialidade

[Se reforçado ou clean-team, descreva por quê. Quem pode ver os arquivos da
matéria. Se contexto cross-matéria é permissível mesmo se globalmente on.]
```

## Seed de `history.md`

```markdown
# Histórico: [Cliente] — [descrição curta]

Log apenas-anexa de eventos. Mais recente no topo.

---

## [YYYY-MM-DD] — Matéria aberta

Intake concluído. Slug: `[slug]`. Status: ativa.
[Qualquer contexto inicial valioso de preservar além do matter.md — ex.:
"Aberta em resposta a hit do serviço de watch em `APEXLEAF` na classe NCL
25."]
```

## Contexto cross-matéria

O CLAUDE.md de nível de prática tem flag `Contexto cross-matéria:`. Quando
está `off` (o default), uma skill trabalhando na matéria A **nunca lê**
arquivos em `matters/B/` para qualquer outra `B`. Ponto. Essa é a garantia
de confidencialidade que a configuração existe para prover.

Quando está `on`, uma skill pode ler arquivos entre pastas de matéria
apenas quando o usuário pedir explicitamente (ex.: "mostre toda notificação
extrajudicial que enviamos sobre esta marca em todas as matérias"). Mesmo
quando `on`, o default é carregar apenas a matéria ativa salvo se o usuário
pedir visão cross-matéria.

## O que esta skill NÃO faz

- **Rodar checagem de conflito.** Conflitos são tarefa do(a)
  praticante/escritório; o intake captura o que o usuário declara.
- **Forçar retenção.** Encerrar arquiva uma matéria; não deleta. Política
  de retenção está fora de escopo (mas note EOAB art. 34 XXI: 5 anos após
  cessação do mandato).
- **Auto-rotear saídas.** A skill substantiva decide onde gravar; esta skill
  diz a ela *qual pasta* está ativa, não o que pôr nela.
- **Decidir se cross-matéria é apropriado.** Lê a flag e obedece.
