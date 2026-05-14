---
name: matter-workspace
description: >
  Gerenciar workspaces de matéria — new, list, switch, close, ou detach
  (nível de prática). Lógica de gerenciamento de arquivos para manter o
  contexto de um(a) cliente ou matéria separado de qualquer outro. Use
  quando trabalhar entre vários(as) clientes ou matérias, quando o(a)
  usuário(a) disser "nova matéria", "trocar matéria", "listar matérias",
  "fechar matéria", ou quando qualquer skill substantiva precisar saber em
  qual matéria está trabalhando.
argument-hint: "<new | list | switch | close | none> [slug]"
---

# /matter-workspace

Operadores(as) trabalham com vários(as) clientes e matérias. Um workspace
de matéria mantém o contexto de um(a) cliente ou engajamento separado de
todos os outros. Esta skill gerencia esses workspaces.

## Subcomandos

- `/governanca-ia:matter-workspace new <slug>` — criar novo workspace de matéria, rodar um intake curto, gravar `matter.md`
- `/governanca-ia:matter-workspace list` — listar matérias com status e flag de ativa
- `/governanca-ia:matter-workspace switch <slug>` — definir a matéria ativa
- `/governanca-ia:matter-workspace close <slug>` — arquivar uma matéria (mover para `~/.claude/plugins/config/claude-for-legal/governanca-ia/matters/_archived/`, nunca apagar)
- `/governanca-ia:matter-workspace none` — desacoplar de qualquer matéria ativa, trabalhar apenas em nível de prática

## Instruções

1. Leia `~/.claude/plugins/config/claude-for-legal/governanca-ia/CLAUDE.md` — confirme que a seção `## Workspaces de matéria` está populada. Se `Enabled` for `✗`, diga: "Workspaces de matéria estão desligados — você está configurado(a) como prática in-house com um(a) cliente, então o plugin trabalha automaticamente em contexto de nível de prática. Se você de fato trabalha entre vários(as) clientes, refaça `/governanca-ia:cold-start-interview --redo` e selecione um setting de banca privada. Caso contrário, você não precisa de `/matter-workspace` em absoluto." Não dispare erro — o estado desligado é o esperado para usuários(as) in-house.
2. Use o workflow abaixo.
3. Despache pelo primeiro token de `$ARGUMENTS`:
   - `new` → rode a entrevista de intake, grave `~/.claude/plugins/config/claude-for-legal/governanca-ia/matters/<slug>/matter.md`, semeie `history.md` e `notes.md`.
   - `list` → enumere `~/.claude/plugins/config/claude-for-legal/governanca-ia/matters/*/matter.md`, imprima tabela, marque a matéria ativa.
   - `switch` → atualize a linha `Matéria ativa:` no CLAUDE.md nível de prática.
   - `close` → mova `~/.claude/plugins/config/claude-for-legal/governanca-ia/matters/<slug>/` para `~/.claude/plugins/config/claude-for-legal/governanca-ia/matters/_archived/<slug>/`, registre a data de fechamento em `history.md`.
   - `none` → defina `Matéria ativa:` como `nenhuma — contexto nível de prática apenas`.
4. Mostre ao(à) usuário(a) o que mudou e confirme antes de gravar.

## Notas

- A skill nunca lê entre matérias a menos que `Contexto entre matérias` esteja `on` no CLAUDE.md de nível de prática.
- Arquivar não é apagar — matérias fechadas permanecem legíveis para fins de retenção/conflito.
- Slugs são minúsculas com hífens. Se um slug for reusado entre arquivada e ativa, a arquivada é preservada em `_archived/<slug>/`.

---

Operadores(as) multicliente (banca privada — solo, banca pequena, banca grande) trabalham entre muitas matérias. Contexto de uma não pode vazar para outra. Esta skill é a camada fina de gerenciamento de arquivos que torna isso verdade.

**Estado default é desligado.** Usuários(as) in-house nunca veem isto — rodam apenas em nível de prática. Workspaces de matéria ligam no cold-start para usuários(as) de banca privada, ou editando `## Workspaces de matéria` no CLAUDE.md de nível de prática. Se `Enabled` for `✗`, esta skill não roda; o workflow acima explica o estado desligado e sugere `/governanca-ia:cold-start-interview --redo` para usuários(as) que de fato precisam de isolamento por matéria.

## Layout de armazenamento

Todos os dados da matéria ficam sob:

```
~/.claude/plugins/config/claude-for-legal/governanca-ia/
├── CLAUDE.md                       # perfil de prática nível de prática
└── matters/
    ├── <slug>/
    │   ├── matter.md               # cliente, contraparte, tipo de matéria, fatos-chave, overrides
    │   ├── history.md              # log datado de eventos, decisões, rascunhos, revisões
    │   ├── notes.md                # notas de trabalho livres
    │   └── outputs/                # outputs de skill para esta matéria (subpasta opcional)
    └── _archived/
        └── <slug>/                 # matérias fechadas — legíveis mas não ativas
```

Slugs são minúsculas com hífens. Exemplos: `acme-msa-2026`, `zenith-renovacao`, `fornecedor-xyz-nda`.

## Matéria ativa está no CLAUDE.md de prática

A linha `Matéria ativa:` sob `## Workspaces de matéria` no CLAUDE.md de nível de prática é a única fonte de verdade. Trocar de matéria edita essa linha. Sem arquivo de estado separado.

## Lógica de subcomandos

### `new <slug>`

1. Confirme que o slug não está presente em `matters/<slug>/` ou `matters/_archived/<slug>/`. Se reusado, peça outro slug.
2. Rode a entrevista de intake:
   - **Cliente** (a parte que representamos, ou a unidade de negócio interna se in-house)
   - **Contraparte** (o outro lado — pode ser várias)
   - **Tipo de matéria** (leia o perfil de prática do plugin para categorias típicas; para governanca-ia: caso de uso (interno) | revisão de fornecedor de IA | RIPD/AIA | mudança regulatória | projeto de política | outro)
   - **Nível de confidencialidade** (standard | reforçado | clean-team — reforçado puxa cuidado extra em settings entre matérias)
   - **Fatos-chave** (2–5 frases: do que se trata a matéria, quem são os stakeholders, o que está em jogo)
   - **Overrides específicos da matéria ao playbook de prática** (p.ex., "cliente exige cap de LoL de 24 meses, não 12", "contraparte é parceira estratégica — tom preservador da relação")
   - **Matérias relacionadas** (slugs de matérias conectadas)
3. Grave `matters/<slug>/matter.md` usando o template abaixo.
4. Semeie `matters/<slug>/history.md` com uma única entrada "Aberta".
5. Crie um `matters/<slug>/notes.md` vazio.
6. **Não** troque automaticamente para a nova matéria. Pergunte: "Quer trocar para `<slug>` agora? (`/governanca-ia:matter-workspace switch <slug>`)"

### `list`

Enumere `matters/*/matter.md`. Leia o front-matter ou as primeiras linhas de cada arquivo para extrair status. Imprima tabela:

| Slug | Cliente | Tipo de matéria | Status | Aberta | Ativa |
|---|---|---|---|---|---|

Marque a matéria atualmente ativa com `*`. Inclua `_archived/*` sob um cabeçalho "Arquivadas" se existir.

### `switch <slug>`

1. Confirme que `matters/<slug>/matter.md` existe. Se não, ofereça `/governanca-ia:matter-workspace new <slug>`.
2. Edite a linha `Matéria ativa:` no CLAUDE.md de nível de prática para `Matéria ativa: <slug>`.
3. Mostre ao(à) usuário(a) o resumo do matter.md para que confirmem que estão na matéria certa.

### `close <slug>`

1. Confirme que `matters/<slug>/` existe.
2. Adicione uma entrada "Fechada" em `matters/<slug>/history.md` com a data de hoje.
3. Mova `matters/<slug>/` → `matters/_archived/<slug>/`.
4. Se a matéria fechada era a ativa, defina `Matéria ativa:` como `nenhuma — contexto nível de prática apenas`.

### `none`

Defina `Matéria ativa:` no CLAUDE.md de nível de prática como `nenhuma — contexto nível de prática apenas`. Confirme com o(a) usuário(a).

## Template `matter.md`

```markdown
[CABEÇALHO DE MATERIAL DE TRABALHO — conforme config do plugin ## Outputs — varia por papel; ver `## Quem está usando` no CLAUDE.md de nível de prática]

# Matéria: [Cliente] — [descrição curta]

**Slug:** [slug]
**Aberta:** [YYYY-MM-DD]
**Status:** ativa
**Confidencialidade:** [standard / reforçado / clean-team]

---

## Partes

**Cliente:** [nome]
**Contraparte:** [nome(s)]

## Tipo de matéria

[fornecedor MSA | contrato com cliente | NDA | assinatura SaaS | aditivo | renovação | outro — com racional em uma linha]

## Fatos-chave

[2–5 frases. Do que se trata a matéria. Quem são os stakeholders. O que está em jogo. O que a torna diferente do playbook default.]

## Overrides específicos da matéria

*Qualquer desvio do playbook de nível de prática que se aplique a esta matéria e apenas a esta.*

- [p.ex., "Cap de LoL: cliente exige 24 meses, não os 12 padrão da casa."]
- [p.ex., "Tom: preservador de relação — contraparte é parceira estratégica."]
- [p.ex., "Lei aplicável: lei brasileira (não Delaware) — foro de São Paulo."]

## Matérias relacionadas

- [slug — uma linha de por que relacionada]

## Notas sobre confidencialidade

[Se reforçado ou clean-team, descreva por quê. Quem pode ver arquivos da matéria. Se contexto entre matérias é permissível mesmo que globalmente ligado.]
```

## Semente `history.md`

```markdown
# Histórico: [Cliente] — [descrição curta]

Log de eventos append-only. Mais recente no topo.

---

## [YYYY-MM-DD] — Matéria aberta

Intake concluído. Slug: `[slug]`. Status: ativa.
[Qualquer contexto inicial digno de preservação além do matter.md — p.ex., "Aberta em resposta a draft de MSA inbound de [contraparte]."]
```

## Contexto entre matérias

O CLAUDE.md de nível de prática tem flag `Contexto entre matérias:`. Quando está `off` (default), uma skill trabalhando na matéria A **nunca lê** arquivos em `matters/B/` para qualquer outra `B`. Ponto. É a garantia de confidencialidade pela qual o setting existe — análoga ao dever de sigilo profissional do(a) advogado(a) sob EOAB art. 7º, XIX, no contexto interno do plugin.

Quando está `on`, uma skill pode ler entre pastas de matéria apenas quando o(a) usuário(a) pedir explicitamente (p.ex., "compare nossa posição sobre caps de responsabilidade nas últimas cinco matérias de fornecedor"). Mesmo quando `on`, o default é carregar apenas a matéria ativa a menos que o(a) usuário(a) peça uma visão entre matérias.

## O que esta skill não faz

- **Rodar conflito de interesse.** Conflito é trabalho do(a) operador(a)/banca (EOAB art. 17 e Provimentos CFOAB); o intake captura o que o(a) usuário(a) declara.
- **Forçar retenção.** Fechar arquiva; não apaga. Política de retenção está fora de escopo.
- **Rotear outputs automaticamente.** A skill substantiva decide onde gravar; esta skill informa *qual pasta* está ativa, não o que colocar nela.
- **Decidir se contexto entre matérias é apropriado.** Lê a flag e obedece.
