---
name: matter-workspace
description: Gerencie workspaces de matéria para práticas multi-cliente — criar, listar, trocar, encerrar ou desvincular a matéria ativa. Use quando o(a) usuário(a) quer criar novo workspace de matéria, trocar a matéria ativa, listar matérias, arquivar uma matéria, ou trabalhar só no nível da prática sem matéria ativa.
argument-hint: "<new | list | switch | close | none> [slug]"
---

# /matter-workspace

Profissionais trabalham por múltiplos clientes e matérias. Um workspace de matéria mantém o contexto de um cliente ou engagement separado dos demais. Este comando gerencia esses workspaces.

## Subcomandos

- `/contencioso:matter-workspace new <slug>` — criar novo workspace, rodar intake curto, escrever `matter.md`
- `/contencioso:matter-workspace list` — listar matérias com status e flag de ativa
- `/contencioso:matter-workspace switch <slug>` — definir a matéria ativa
- `/contencioso:matter-workspace close <slug>` — arquivar matéria (mover para `~/.claude/plugins/config/claude-for-legal/contencioso/matters/_archived/`, nunca apagar)
- `/contencioso:matter-workspace none` — desvincular de qualquer matéria ativa, trabalhar só no nível da prática

Nota: `/contencioso:matter-briefing [slug]` (sem subcomando) é comando separado que produz briefing de uma matéria específica — útil para revisão de portfólio in-house. Gestão de workspace de matéria vive aqui.

## Instruções

1. Ler `~/.claude/plugins/config/claude-for-legal/contencioso/CLAUDE.md` — confirmar que a seção `## Workspaces de matéria` está populada. Se `Habilitado` for `✗`, dizer ao(à) usuário(a): "Workspaces de matéria estão off — você está configurado(a) como prática in-house com um cliente, então o plugin trabalha de contexto no nível da prática automaticamente. Se você de fato atua por múltiplos clientes, rerode `/contencioso:cold-start-interview --redo` e selecione setting de advocacia privada. Caso contrário, você não precisa de `/matter-workspace`." Não dê erro — o estado desabilitado é o estado esperado para usuários(as) in-house.
2. Seguir o workflow e referência abaixo.
3. Despachar no primeiro token de `$ARGUMENTS`:
   - `new` → rodar a entrevista de intake, escrever `~/.claude/plugins/config/claude-for-legal/contencioso/matters/<slug>/matter.md`, semear `history.md` e `notes.md`.
   - `list` → enumerar `~/.claude/plugins/config/claude-for-legal/contencioso/matters/*/matter.md`, imprimir tabela, marcar a matéria ativa.
   - `switch` → atualizar a linha `Matéria ativa:` no CLAUDE.md da prática.
   - `close` → mover `~/.claude/plugins/config/claude-for-legal/contencioso/matters/<slug>/` para `~/.claude/plugins/config/claude-for-legal/contencioso/matters/_archived/<slug>/`, logar a data de fechamento em `history.md`.
   - `none` → setar `Matéria ativa:` para `nenhuma — só contexto no nível da prática`.
4. Mostrar ao(à) usuário(a) o que mudou e confirmar antes de escrever.

## Notas

- A skill nunca lê entre matérias salvo se `Cross-matter context` estiver `on` no CLAUDE.md da prática.
- Arquivamento não é exclusão — matérias encerradas continuam legíveis para fins de retenção / conflito.
- Slugs são minúsculas com hífens. Se um slug é reusado entre arquivada e ativa, a arquivada é preservada sob `_archived/<slug>/`.

---

# Workspace de Matéria

Profissionais multi-cliente (advocacia privada — solo, banca pequena, banca grande) trabalham em muitas matérias. Contexto de uma não pode vazar para outra. Esta skill é a camada fina de gerenciamento de arquivo que torna isso verdade.

**Estado default é off.** Usuários(as) in-house nunca veem isso — rodam no nível da prática apenas. Workspaces de matéria ligam no cold-start para usuários(as) de advocacia privada, ou editando `## Workspaces de matéria` no CLAUDE.md da prática. Se `Habilitado` for `✗`, esta skill não roda; a skill `/matter-workspace` explica o estado desabilitado e sugere `/cold-start-interview --redo` para usuários(as) que efetivamente precisam de isolamento por matéria.

## Layout de armazenamento

Todo dado de matéria vive sob:

```
~/.claude/plugins/config/claude-for-legal/contencioso/
├── CLAUDE.md                       # perfil prático no nível da prática
└── matters/
    ├── <slug>/
    │   ├── matter.md               # cliente, contraparte, tipo de matéria, fatos-chave, overrides
    │   ├── history.md              # log datado de eventos, decisões, minutas, revisões
    │   ├── notes.md                # notas de trabalho em forma livre
    │   └── outputs/                # saídas de skill para esta matéria (subpasta opcional)
    └── _archived/
        └── <slug>/                 # matérias encerradas — legíveis mas não ativas
```

Slugs são minúsculas com hífens. Exemplos: `acme-c-uniao-2026`, `silva-trabalhista-2026`, `fornecedor-xyz-cessar-2026`.

## Matéria ativa está no CLAUDE.md da prática

A linha `Matéria ativa:` sob `## Workspaces de matéria` no CLAUDE.md da prática é a única fonte de verdade. Trocar de matéria edita essa linha. Sem arquivo de estado separado.

## Lógica de subcomando

### `new <slug>`

1. Confirmar que slug não está em `matters/<slug>/` nem `matters/_archived/<slug>/`. Se reusado, pedir slug diferente.
2. Rodar a entrevista de intake:
   - **Cliente** (a parte que representamos, ou a unidade de negócio interna se in-house)
   - **Contraparte** (a outra parte — pode ser múltiplas)
   - **Tipo de matéria** (ler o perfil prático do plugin para categorias típicas; para contencioso: contratual / comercial | trabalhista | PI | regulatório / investigação | consumidor | responsabilidade civil / produto | ação coletiva (Lei 7.347/1985 ACP; CDC arts. 81-104) | outro)
   - **Nível de confidencialidade** (padrão | reforçado | clean-team — reforçado pede cuidado extra em settings cross-matter)
   - **Fatos-chave** (2–5 frases: do que se trata a matéria, quem são os stakeholders, o que está em jogo)
   - **Overrides específicos da matéria ao playbook da prática** (ex. "cliente exige cap de responsabilidade de 24 meses não 12", "contraparte é parceiro estratégico — tom preserva-relação")
   - **Matérias relacionadas** (slugs de qualquer matéria conectada)
3. Escrever `matters/<slug>/matter.md` usando o template abaixo.
4. Semear `matters/<slug>/history.md` com entrada única "Aberta".
5. Criar `matters/<slug>/notes.md` vazio.
6. **Não** trocar automaticamente para a nova matéria. Perguntar: "Quer trocar para `<slug>` agora? (`/contencioso:matter-workspace switch <slug>`)"

### `list`

Enumerar `matters/*/matter.md`. Ler front-matter ou primeiras linhas para extrair status. Imprimir tabela:

| Slug | Cliente | Tipo de matéria | Status | Aberta | Ativa |
|---|---|---|---|---|---|

Marcar a matéria atualmente ativa com `*`. Incluir `_archived/*` sob heading separado "Arquivadas" se houver.

### `switch <slug>`

1. Confirmar que `matters/<slug>/matter.md` existe. Se não, oferecer `/contencioso:matter-workspace new <slug>`.
2. Editar a linha `Matéria ativa:` no CLAUDE.md da prática para `Matéria ativa: <slug>`.
3. Mostrar ao(à) usuário(a) o resumo do matter.md para confirmar matéria certa.

### `close <slug>`

1. Confirmar que `matters/<slug>/` existe.
2. Anexar entrada "Encerrada" a `matters/<slug>/history.md` com data de hoje.
3. Mover `matters/<slug>/` → `matters/_archived/<slug>/`.
4. Se a matéria encerrada era a matéria ativa, setar `Matéria ativa:` para `nenhuma — só contexto no nível da prática`.

### `none`

Setar `Matéria ativa:` no CLAUDE.md da prática para `nenhuma — só contexto no nível da prática`. Confirmar com o(a) usuário(a).

## Template `matter.md`

```markdown
[CABEÇALHO DE MATERIAL DE TRABALHO — conforme config do plugin ## Saídas — varia por papel; ver `## Quem está usando` no CLAUDE.md da prática]

# Matéria: [Cliente] — [descrição curta]

**Slug:** [slug]
**Aberta:** [AAAA-MM-DD]
**Status:** ativa
**Confidencialidade:** [padrão / reforçada / clean-team]

---

## Partes

**Cliente:** [nome]
**Contraparte:** [nome(s)]

## Tipo de matéria

[contratual / comercial | trabalhista | PI | regulatório | consumidor | produto | ação coletiva | outro — com racional de uma linha]

## Fatos-chave

[2–5 frases. Do que se trata a matéria. Quem são os stakeholders. O que está em jogo. O que torna esta diferente do playbook default.]

## Overrides específicos da matéria

*Qualquer desvio do playbook da prática que se aplique a esta matéria e somente a ela.*

- [ex. "Cap de responsabilidade: cliente exige 24 meses, não os 12 padrão da casa."]
- [ex. "Tom: preserva-relação — contraparte é parceiro estratégico."]
- [ex. "Lei aplicável: tem que ser direito brasileiro com foro em SP, não Delaware."]

## Matérias relacionadas

- [slug — uma linha por que relacionado]

## Notas sobre confidencialidade

[Se reforçada ou clean-team, descrever por quê. Quem pode ver arquivos da matéria. Se contexto cross-matter é permissível mesmo se globalmente on.]
```

## Seed de `history.md`

```markdown
# Histórico: [Cliente] — [descrição curta]

Log de eventos append-only. Mais recente no topo.

---

## [AAAA-MM-DD] — Matéria aberta

Intake completo. Slug: `[slug]`. Status: ativa.
[Qualquer contexto inicial que valha preservar além do matter.md — ex. "Aberta em resposta a minuta inicial de contrato recebida de [contraparte]."]
```

## Contexto cross-matter

O CLAUDE.md da prática tem flag `Cross-matter context:`. Quando off (default), uma skill trabalhando na matéria A **nunca lê** arquivos em `matters/B/` para qualquer outra `B`. Ponto. Essa é a garantia de confidencialidade para a qual o setting existe.

Quando on, uma skill pode ler entre pastas de matéria só quando o(a) usuário(a) explicitamente pede (ex. "compare nossa posição sobre cap de responsabilidade nas últimas cinco matérias de fornecedor"). Mesmo quando on, o default é carregar só a matéria ativa salvo pedido explícito de view cross-matter.

## O que esta skill NÃO faz

- **Rodar conferência de conflito.** Conflito é trabalho do(a) profissional / banca; o intake captura o que o(a) usuário(a) declara.
- **Forçar retenção.** Fechar arquiva matéria; não apaga. Política de retenção está fora de escopo.
- **Rotear saídas automaticamente.** A skill substantiva decide onde escrever; esta skill diz *qual pasta* está ativa, não o que pôr nela.
- **Decidir se cross-matter é apropriado.** Lê o flag e obedece.
