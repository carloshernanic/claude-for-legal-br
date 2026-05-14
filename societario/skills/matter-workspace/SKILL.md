---
name: matter-workspace
description: >
  Gerencia workspaces de matéria — criar, listar, trocar, fechar ou desligar a
  matéria ativa para que profissional multicliente mantenha o contexto de um
  cliente separado de todo o resto. Lido por qualquer skill substantiva que
  precise saber em qual matéria está trabalhando. Use quando o(a) usuário(a)
  disser "nova matéria", "trocar matéria", "listar matérias", "fechar matéria",
  ou quiser trabalhar apenas em nível de prática.
argument-hint: "<new | list | switch | close | none> [slug]"
---

# /matter-workspace

Profissionais trabalham com vários clientes e matérias. Um workspace de matéria mantém o contexto de um cliente ou engagement separado de todo o resto. Esta skill gerencia esses workspaces.

## Subcomandos

- `/societario:matter-workspace new <slug>` — cria novo workspace de matéria, roda intake curto, escreve `matter.md`
- `/societario:matter-workspace list` — lista matérias com status e flag de ativa
- `/societario:matter-workspace switch <slug>` — define a matéria ativa
- `/societario:matter-workspace close <slug>` — arquiva uma matéria (move para `~/.claude/plugins/config/claude-for-legal/societario/matters/_archived/`, nunca deleta)
- `/societario:matter-workspace none` — desligar qualquer matéria ativa, trabalhar apenas em nível de prática

## Instruções

1. Leia `~/.claude/plugins/config/claude-for-legal/societario/CLAUDE.md` — confirme que a seção `## Workspaces de matéria` está populada. Se `Habilitado` é `✗`, diga ao(à) usuário(a): "Workspaces de matéria estão desligados — você está configurado(a) como prática in-house com um único cliente, então o plugin funciona a partir do contexto de prática automaticamente. Se você de fato atua para múltiplos clientes, re-rode `/societario:cold-start-interview --redo` e selecione um contexto de prática privada. Caso contrário, você não precisa de `/matter-workspace`." Não falhe — o estado desligado é o esperado para usuários(as) in-house.
2. Use o workflow abaixo.
3. Despache no primeiro token de `$ARGUMENTS`:
   - `new` → roda a entrevista de intake, escreve `~/.claude/plugins/config/claude-for-legal/societario/matters/<slug>/matter.md`, semeia `history.md` e `notes.md`.
   - `list` → enumera `~/.claude/plugins/config/claude-for-legal/societario/matters/*/matter.md`, imprime tabela, marca matéria ativa.
   - `switch` → atualiza a linha `Matéria ativa:` no CLAUDE.md de nível-prática.
   - `close` → move `~/.claude/plugins/config/claude-for-legal/societario/matters/<slug>/` para `~/.claude/plugins/config/claude-for-legal/societario/matters/_archived/<slug>/`, registra a data de encerramento em `history.md`.
   - `none` → define `Matéria ativa:` como `nenhuma — apenas contexto nível-prática`.
4. Mostre ao(à) usuário(a) o que mudou e confirme antes de gravar.

## Notas

- A skill nunca lê entre matérias a menos que `Contexto cross-matter` esteja `on` no CLAUDE.md de nível-prática.
- Arquivamento não é deleção — matérias fechadas permanecem legíveis para retenção/análise de conflitos.
- Slugs em minúsculas com hifens. Se um slug for reutilizado entre arquivado e ativo, o arquivado é preservado em `_archived/<slug>/`.

---

Profissionais multicliente (advocacia privada — solo, banca pequena, escritório grande) trabalham em várias matérias. Contexto de uma não pode vazar para outra. Esta skill é a camada fina de gestão de arquivos que torna isso verdadeiro.

**Estado padrão é desligado.** Usuários(as) in-house nunca veem isto — rodam apenas em nível-prática. Workspaces de matéria são ligados no cold-start para usuários(as) de prática privada, ou editando `## Workspaces de matéria` no CLAUDE.md nível-prática. Se `Habilitado` é `✗`, esta skill não roda; `/societario:matter-workspace` explica o estado desligado e sugere `/societario:cold-start-interview --redo` para quem efetivamente precisa de isolamento de matéria.

## Layout de armazenamento

Todos os dados de matéria vivem sob:

```
~/.claude/plugins/config/claude-for-legal/societario/
├── CLAUDE.md                       # perfil de prática nível-prática
└── matters/
    ├── <slug>/
    │   ├── matter.md               # cliente, parte adversa, tipo de matéria, fatos-chave, overrides
    │   ├── history.md              # log datado de eventos, decisões, minutas, revisões
    │   ├── notes.md                # notas livres de trabalho
    │   └── outputs/                # saídas de skills para esta matéria (subpasta opcional)
    └── _archived/
        └── <slug>/                 # matérias fechadas — legíveis mas não ativas
```

Slugs em minúsculas com hifens. Exemplos: `acme-spa-2026`, `zenith-renovacao`, `fornecedor-xyz-nda`.

## Matéria ativa fica no CLAUDE.md de prática

A linha `Matéria ativa:` sob `## Workspaces de matéria` no CLAUDE.md nível-prática é a fonte única de verdade. Trocar de matéria edita essa linha. Sem arquivo de estado separado.

## Lógica de subcomando

### `new <slug>`

1. Confirme que o slug não está presente em `matters/<slug>/` ou `matters/_archived/<slug>/`. Se reusado, peça outro slug.
2. Rode a entrevista de intake:
   - **Cliente** (a parte que representamos, ou a unidade interna se in-house)
   - **Parte adversa** (a outra parte — pode haver mais de uma)
   - **Tipo de matéria** (leia o perfil de prática para categorias típicas; em societario: M&A buy-side | M&A sell-side | financiamento | matéria de conselho | reorganização societária | projeto de integração | outro)
   - **Nível de confidencialidade** (padrão | reforçado | clean-team — reforçado dispara cuidado extra em cenários cross-matter)
   - **Fatos-chave** (2–5 frases: o que é esta matéria, quem são os stakeholders, o que está em jogo)
   - **Overrides específicos da matéria ao playbook de prática** (ex.: "cliente exige cap de responsabilidade 24 meses não 12", "parte adversa é parceira estratégica — tom preservador da relação")
   - **Matérias relacionadas** (slugs de matérias conectadas)
3. Grave `matters/<slug>/matter.md` usando o template abaixo.
4. Semeie `matters/<slug>/history.md` com uma única entrada "Aberta".
5. Crie um `matters/<slug>/notes.md` vazio.
6. **Não** troque automaticamente para a nova matéria. Pergunte: "Quer trocar para `<slug>` agora? (`/societario:matter-workspace switch <slug>`)"

### `list`

Enumere `matters/*/matter.md`. Leia o frontmatter ou as primeiras linhas de cada arquivo para extrair status. Imprima tabela:

| Slug | Cliente | Tipo | Status | Aberta | Ativa |
|---|---|---|---|---|---|

Marque a matéria atualmente ativa com `*`. Inclua `_archived/*` sob cabeçalho "Arquivadas" separado se houver.

### `switch <slug>`

1. Confirme que `matters/<slug>/matter.md` existe. Se não, ofereça `/societario:matter-workspace new <slug>`.
2. Edite a linha `Matéria ativa:` no CLAUDE.md nível-prática para `Matéria ativa: <slug>`.
3. Mostre ao(à) usuário(a) o sumário de matter.md para confirmação.

### `close <slug>`

1. Confirme que `matters/<slug>/` existe.
2. Anexe entrada "Fechada" a `matters/<slug>/history.md` com a data de hoje.
3. Mova `matters/<slug>/` → `matters/_archived/<slug>/`.
4. Se a matéria fechada era a ativa, defina `Matéria ativa:` como `nenhuma — apenas contexto nível-prática`.

### `none`

Defina `Matéria ativa:` no CLAUDE.md nível-prática como `nenhuma — apenas contexto nível-prática`. Confirme com o(a) usuário(a).

## Template `matter.md`

```markdown
[CABEÇALHO DE MATERIAL DE TRABALHO — conforme config do plugin ## Outputs — varia por papel; ver `## Quem está usando` no CLAUDE.md nível-prática]

# Matéria: [Cliente] — [descrição curta]

**Slug:** [slug]
**Aberta em:** [AAAA-MM-DD]
**Status:** ativa
**Confidencialidade:** [padrão / reforçado / clean-team]

---

## Partes

**Cliente:** [nome]
**Parte adversa:** [nome(s)]

## Tipo de matéria

[M&A buy-side | M&A sell-side | acordo de acionistas | financiamento | reorganização | integração pós-fechamento | outro — com uma linha de racional]

## Fatos-chave

[2–5 frases. O que é esta matéria. Quem são os stakeholders. O que está em jogo. O que a torna diferente do playbook padrão.]

## Overrides específicos da matéria

*Qualquer desvio do playbook nível-prática que se aplique a esta matéria e somente a esta matéria.*

- [ex.: "Cap de responsabilidade: cliente exige 24 meses, não os 12 padrão da casa."]
- [ex.: "Tom: preservador da relação — parte adversa é parceira estratégica."]
- [ex.: "Lei aplicável: necessariamente brasileira, foro de São Paulo."]

## Matérias relacionadas

- [slug — uma linha por que está relacionada]

## Notas sobre confidencialidade

[Se reforçada ou clean-team, descreva o porquê. Quem pode ver arquivos da matéria. Se contexto cross-matter é admissível mesmo que globalmente esteja on.]
```

## Seed de `history.md`

```markdown
# Histórico: [Cliente] — [descrição curta]

Log append-only de eventos. Mais recente no topo.

---

## [AAAA-MM-DD] — Matéria aberta

Intake concluído. Slug: `[slug]`. Status: ativa.
[Qualquer contexto inicial que valha preservar além de matter.md — ex.: "Aberta em resposta a minuta de SPA recebida de [parte adversa]."]
```

## Contexto cross-matter

O CLAUDE.md nível-prática tem flag `Contexto cross-matter:`. Quando está `off` (padrão), uma skill trabalhando na matéria A **nunca lê** arquivos em `matters/B/` para qualquer outra `B`. Ponto. Esta é a garantia de confidencialidade para a qual a configuração existe.

Quando está `on`, uma skill pode ler arquivos entre pastas de matéria apenas quando o(a) usuário(a) explicitamente pedir (ex.: "compare nossa posição em caps de responsabilidade nas últimas cinco matérias de fornecedor"). Mesmo com `on`, o padrão é carregar apenas a matéria ativa a menos que se peça uma visão cross-matter.

## O que esta skill não faz

- **Roda checagem de conflitos.** Conflitos são responsabilidade do(a) profissional / escritório; o intake captura o que o(a) usuário(a) declara.
- **Aplica retenção.** Fechar arquiva uma matéria; não deleta. Política de retenção está fora de escopo.
- **Roteia saídas automaticamente.** A skill substantiva decide onde gravar; esta skill diz *qual pasta* está ativa, não o que colocar nela.
- **Decide se cross-matter é apropriado.** Lê a flag e obedece.
