---
name: matter-workspace
description: >
  Gerenciar matter workspaces — new, list, switch, close, ou detach (practice-level).
  Use quando trabalhar com múltiplos clientes ou matérias em advocacia privada e
  você precisar criar, listar, trocar, fechar ou destacar a matéria ativa para que
  contexto de uma não vaze para outra.
argument-hint: "<new | list | switch | close | none> [slug]"
---

# /matter-workspace

> **Adaptação BR.** Sigilo profissional aplica-se a todas as matter workspaces (EOAB Lei 8.906/1994, art. 7º, II e XIX; CPC art. 388). Calibrado para advocacia privada no Brasil.

Profissionais da advocacia trabalham com múltiplos clientes e matérias. Um matter workspace mantém o contexto de um cliente ou engajamento separado de todos os outros. Esta skill gerencia esses workspaces.

## Subcomandos

- `/produto:matter-workspace new <slug>` — cria um novo matter workspace, roda intake curta, escreve `matter.md`
- `/produto:matter-workspace list` — lista matérias com status e flag de ativa
- `/produto:matter-workspace switch <slug>` — define a matéria ativa
- `/produto:matter-workspace close <slug>` — arquiva uma matéria (move para `~/.claude/plugins/config/claude-for-legal/produto/matters/_archived/`, nunca deleta — preserva sigilo e atende exigência de guarda da OAB)
- `/produto:matter-workspace none` — destaca de qualquer matéria ativa, trabalha só em practice-level

## Instruções

1. Leia `~/.claude/plugins/config/claude-for-legal/produto/CLAUDE.md` — confirme que a seção `## Matter workspaces` está populada. Se `Enabled` for `✗`, diga ao usuário: "Matter workspaces estão desligadas — você está configurado(a) como prática in-house com um cliente, então o plugin trabalha a partir do contexto de practice-level automaticamente. Se você realmente trabalha em múltiplos clientes, rode `/produto:cold-start-interview --redo` e selecione um cenário de advocacia privada. Caso contrário, você não precisa de `/matter-workspace`." Não dê erro — o estado desabilitado é o esperado para in-house.
2. Aplique o layout de storage e a lógica dos subcomandos abaixo.
3. Despache no primeiro token de `$ARGUMENTS`:
   - `new` → roda a entrevista de intake, escreve `~/.claude/plugins/config/claude-for-legal/produto/matters/<slug>/matter.md`, semeia `history.md` e `notes.md`.
   - `list` → enumera `~/.claude/plugins/config/claude-for-legal/produto/matters/*/matter.md`, imprime tabela, marca a matéria ativa.
   - `switch` → atualiza a linha `Active matter:` no CLAUDE.md de practice-level.
   - `close` → move `~/.claude/plugins/config/claude-for-legal/produto/matters/<slug>/` para `~/.claude/plugins/config/claude-for-legal/produto/matters/_archived/<slug>/`, registra a data de fechamento em `history.md`.
   - `none` → define `Active matter:` para `nenhuma — contexto practice-level apenas`.
4. Mostre ao usuário o que mudou e confirme antes de escrever.

## Notas

- Esta skill nunca lê entre matérias a menos que `Cross-matter context` esteja `on` no CLAUDE.md de practice-level. Esta é a garantia operacional de sigilo entre clientes (EOAB art. 34, X — captação ilícita; CED-OAB).
- Arquivamento não é exclusão — matérias encerradas permanecem legíveis para retenção, conflitos e exigências da OAB (guarda mínima recomendada de 5 anos após encerramento, em alinhamento com prescrição civil — CC art. 206).
- Slugs são minúsculos com hífens. Se um slug for reutilizado entre arquivado e ativo, o arquivado é preservado em `_archived/<slug>/`.

---

# Matter Workspace

Profissionais multi-cliente (advocacia privada — solo, banca pequena, banca grande) trabalham em muitas matérias. Contexto de uma não pode vazar para outra. Esta skill é a camada fina de gerenciamento de arquivos que torna isso verdadeiro.

**Estado default é off.** Usuários in-house nunca veem isto — rodam só em practice-level. Matter workspaces ligam no cold-start para usuários de advocacia privada, ou editando `## Matter workspaces` no CLAUDE.md de practice-level. Se `Enabled` for `✗`, esta skill não roda; o comando `/matter-workspace` explica o estado desabilitado e sugere `/cold-start-interview --redo` para usuários que de fato precisam de isolamento de matéria.

## Layout de storage

Todos os dados de matéria ficam em:

```
~/.claude/plugins/config/claude-for-legal/produto/
├── CLAUDE.md                       # practice profile de practice-level
└── matters/
    ├── <slug>/
    │   ├── matter.md               # cliente, contraparte, tipo de matéria, fatos-chave, overrides
    │   ├── history.md              # log datado de eventos, decisões, drafts, revisões
    │   ├── notes.md                # notas livres de trabalho
    │   └── outputs/                # outputs de skill desta matéria (subpasta opcional)
    └── _archived/
        └── <slug>/                 # matérias encerradas — legíveis mas não ativas
```

Slugs são minúsculos com hífens. Exemplos: `acme-msa-2026`, `zenith-renovacao`, `fornecedor-xyz-nda`.

## Matéria ativa fica no CLAUDE.md da prática

A linha `Active matter:` sob `## Matter workspaces` no CLAUDE.md de practice-level é a única fonte de verdade. Trocar uma matéria edita aquela linha. Sem arquivo de estado separado.

## Lógica dos subcomandos

### `new <slug>`

1. Confirme que o slug não está presente em `matters/<slug>/` ou `matters/_archived/<slug>/`. Se reutilizado, peça ao usuário para escolher outro slug.
2. Rode a entrevista de intake:
   - **Cliente** (a parte que representamos, ou a área de negócio interna se in-house)
   - **Contraparte** (o outro lado — pode ser múltipla)
   - **Tipo de matéria** (leia o practice profile do plugin para categorias típicas; para produto: lançamento | revisão de feature | revisão de claim de marketing | risk deep dive | área de produto (standing) | outro)
   - **Nível de confidencialidade** (padrão | reforçado | clean-team — reforçado pede cuidado extra em cenários cross-matter)
   - **Fatos-chave** (2–5 frases: do que se trata, quem são os stakeholders, o que está em jogo)
   - **Overrides específicos da matéria ao playbook da prática** (ex.: "cliente exige cap de responsabilidade de 24 meses, não 12", "contraparte é parceiro estratégico — tom preservador de relacionamento")
   - **Matérias relacionadas** (slugs de quaisquer matérias conectadas)
3. Escreva `matters/<slug>/matter.md` usando o template abaixo.
4. Semeie `matters/<slug>/history.md` com uma única entrada "Aberta".
5. Crie um `matters/<slug>/notes.md` vazio.
6. **Não** troque automaticamente para a nova matéria. Pergunte: "Quer trocar para `<slug>` agora? (`/produto:matter-workspace switch <slug>`)"

### `list`

Enumere `matters/*/matter.md`. Leia o front-matter ou as primeiras linhas para extrair status. Imprima uma tabela:

| Slug | Cliente | Tipo de matéria | Status | Aberta em | Ativa |
|---|---|---|---|---|---|

Marque a matéria atualmente ativa com `*`. Inclua `_archived/*` sob título "Arquivadas" se houver.

### `switch <slug>`

1. Confirme que `matters/<slug>/matter.md` existe. Se não, ofereça `/produto:matter-workspace new <slug>`.
2. Edite a linha `Active matter:` no CLAUDE.md de practice-level para `Active matter: <slug>`.
3. Mostre ao usuário o resumo de matter.md para confirmar que está na matéria certa.

### `close <slug>`

1. Confirme que `matters/<slug>/` existe.
2. Anexe entrada "Encerrada" em `matters/<slug>/history.md` com a data de hoje.
3. Mova `matters/<slug>/` → `matters/_archived/<slug>/`.
4. Se a matéria fechada era a ativa, defina `Active matter:` para `nenhuma — contexto practice-level apenas`.

### `none`

Defina `Active matter:` no CLAUDE.md de practice-level para `nenhuma — contexto practice-level apenas`. Confirme com o usuário.

## Template `matter.md`

```markdown
[CABEÇALHO DE MATERIAL DE TRABALHO — conforme config do plugin ## Outputs — varia por papel; veja `## Quem está usando isto` no CLAUDE.md de practice-level]

# Matéria: [Cliente] — [descrição curta]

**Slug:** [slug]
**Aberta em:** [YYYY-MM-DD]
**Status:** ativa
**Confidencialidade:** [padrão / reforçada / clean-team]

---

## Partes

**Cliente:** [nome]
**Contraparte:** [nome(s)]

## Tipo de matéria

[MSA com fornecedor | contrato com cliente | NDA | assinatura SaaS | aditivo | renovação | outro — com uma linha de racional]

## Fatos-chave

[2–5 frases. Do que trata a matéria. Quem são os stakeholders. O que está em jogo. O que torna esta matéria diferente do playbook padrão.]

## Overrides específicos da matéria

*Qualquer desvio do playbook de practice-level que se aplica a esta matéria e apenas a esta.*

- [ex.: "Cap de responsabilidade: cliente exige 24 meses, não os 12 padrão da casa."]
- [ex.: "Tom: preservador de relacionamento — contraparte é parceiro estratégico."]
- [ex.: "Lei aplicável: deve ser direito brasileiro (LINDB art. 9º) com foro de São Paulo (CPC art. 63)."]

## Matérias relacionadas

- [slug — uma linha de por que está relacionada]

## Notas sobre confidencialidade

[Se reforçada ou clean-team, descreva por quê. Quem pode ver arquivos da matéria. Se contexto cross-matter é admissível mesmo se globalmente ligado. Lembrar do sigilo do EOAB art. 7º, II e XIX e do CED-OAB.]
```

## Seed de `history.md`

```markdown
# Histórico: [Cliente] — [descrição curta]

Log de eventos apenas-append. Mais recente no topo.

---

## [YYYY-MM-DD] — Matéria aberta

Intake completada. Slug: `[slug]`. Status: ativa.
[Qualquer contexto inicial que valha preservar além do matter.md — ex.: "Aberta em resposta a draft de MSA inbound de [contraparte]."]
```

## Cross-matter context

O CLAUDE.md de practice-level tem flag `Cross-matter context:`. Quando está `off` (default), uma skill trabalhando na matéria A **nunca lê** arquivos em `matters/B/` para qualquer outro `B`. Ponto. Esta é a garantia de confidencialidade que o setting existe para prover — e o cumprimento operacional do sigilo profissional (EOAB art. 7º, II e XIX) entre matérias do mesmo escritório.

Quando está `on`, uma skill pode ler arquivos entre pastas de matéria apenas quando o usuário pede explicitamente (ex.: "compare nossa posição em caps de responsabilidade nas últimas cinco matérias de fornecedor"). Mesmo `on`, o default é carregar só a matéria ativa salvo pedido cross-matter expresso.

## O que esta skill NÃO faz

- **Rodar conflict check.** Conflitos são responsabilidade do(a) advogado(a)/escritório (CED-OAB; EOAB art. 17 e ss.); o intake captura o que o usuário declara.
- **Aplicar política de retenção.** Encerrar arquiva uma matéria; não deleta. Política de retenção está fora de escopo (mas considere a guarda mínima recomendada — CC art. 206 — antes de purgar).
- **Auto-rotear outputs.** A skill substantiva decide onde escrever; esta skill diz a ela *qual pasta* está ativa, não o que colocar nela.
- **Decidir se cross-matter é apropriado.** Lê a flag e obedece.
