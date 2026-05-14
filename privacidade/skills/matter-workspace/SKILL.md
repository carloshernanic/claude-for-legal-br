---
name: matter-workspace
description: >
  Gerencia workspaces de caso — cria, lista, troca, fecha ou desliga
  (nível de prática). Mantém o contexto de um cliente ou de uma demanda
  separado de todos os outros, para advogados(as) com múltiplos clientes.
  Use quando o(a) usuário(a) quiser abrir novo caso, trocar de caso,
  listar casos, fechar/arquivar um caso, ou trabalhar somente em nível
  de prática.
argument-hint: "<new | list | switch | close | none> [slug]"
---

# /matter-workspace

Advogados(as) que atuam em múltiplos clientes e casos precisam manter o contexto de cada um separado dos demais. Um workspace de caso mantém o contexto de um cliente ou demanda isolado dos outros. Esta skill gerencia esses workspaces.

## Subcomandos

- `/privacidade:matter-workspace new <slug>` — cria novo workspace de caso, roda intake curto, escreve `matter.md`
- `/privacidade:matter-workspace list` — lista casos com status e flag de ativo
- `/privacidade:matter-workspace switch <slug>` — define o caso ativo
- `/privacidade:matter-workspace close <slug>` — arquiva o caso (move para `~/.claude/plugins/config/claude-for-legal/privacidade/matters/_archived/`, nunca apaga)
- `/privacidade:matter-workspace none` — desliga qualquer caso ativo, trabalha somente em nível de prática

## Instruções

1. Leia `~/.claude/plugins/config/claude-for-legal/privacidade/CLAUDE.md` — confirme que a seção `## Workspaces de caso` está populada. Se `Habilitado` for `✗`, diga ao(à) usuário(a): "Workspaces de caso estão desligados — você está configurado(a) como prática in-house com um único cliente, então o plugin trabalha em contexto de prática automaticamente. Se você de fato atua em múltiplos clientes, rerode `/privacidade:cold-start-interview --redo` e selecione uma configuração de advocacia privada. Caso contrário, não precisa de `/matter-workspace`." Não dê erro — o estado desligado é o esperado para usuários in-house.
2. Use a lógica de subcomando abaixo.
3. Dispatch no primeiro token de `$ARGUMENTS`:
   - `new` → rode a entrevista de intake, escreva `~/.claude/plugins/config/claude-for-legal/privacidade/matters/<slug>/matter.md`, semeie `history.md` e `notes.md`.
   - `list` → enumere `~/.claude/plugins/config/claude-for-legal/privacidade/matters/*/matter.md`, imprima tabela, marque o caso ativo.
   - `switch` → atualize a linha `Caso ativo:` no CLAUDE.md de nível de prática.
   - `close` → mova `~/.claude/plugins/config/claude-for-legal/privacidade/matters/<slug>/` para `~/.claude/plugins/config/claude-for-legal/privacidade/matters/_archived/<slug>/`, registre a data de encerramento no `history.md`.
   - `none` → defina `Caso ativo:` como `none — somente contexto de prática`.
4. Mostre ao(à) usuário(a) o que mudou e confirme antes de escrever.

## Notas

- A skill nunca lê entre casos a menos que `Contexto cruzado entre casos` esteja `on` no CLAUDE.md de nível de prática.
- Arquivamento não é deleção — casos encerrados continuam legíveis para retenção e checagem de conflitos.
- Slugs são em minúsculas com hífens. Se um slug for reaproveitado entre arquivado e ativo, o arquivado é preservado em `_archived/<slug>/`.

---

# Matter Workspace

Advogados(as) em múltiplos clientes (advocacia privada — solo, pequena banca, banca grande) atuam em vários casos simultâneos. Contexto de um não pode vazar para outro. Esta skill é a camada fina de gestão de arquivos que torna isso verdadeiro.

**Estado padrão é desligado.** Usuários(as) in-house nunca veem isto — rodam somente em nível de prática. Workspaces de caso são ligados no cold-start para usuários(as) de advocacia privada, ou editando `## Workspaces de caso` no CLAUDE.md de prática. Se `Habilitado` for `✗`, esta skill não roda; o workflow acima explica o estado desligado e sugere `/privacidade:cold-start-interview --redo` para quem de fato precisa de isolamento por caso.

## Layout de armazenamento

Todo dado de caso vive em:

```
~/.claude/plugins/config/claude-for-legal/privacidade/
├── CLAUDE.md                       # perfil de prática (nível de prática)
└── matters/
    ├── <slug>/
    │   ├── matter.md               # cliente, contraparte, tipo de caso, fatos-chave, overrides
    │   ├── history.md              # log datado de eventos, decisões, minutas, revisões
    │   ├── notes.md                # notas de trabalho livres
    │   └── outputs/                # saídas de skills para este caso (subpasta opcional)
    └── _archived/
        └── <slug>/                 # casos encerrados — legíveis mas não ativos
```

Slugs em minúsculas com hífens. Exemplos: `acme-tratamento-2026`, `zenith-renovacao`, `fornecedor-xyz-contrato-tratamento`.

## O caso ativo está no CLAUDE.md de prática

A linha `Caso ativo:` sob `## Workspaces de caso` no CLAUDE.md de nível de prática é a fonte única da verdade. Trocar de caso edita essa linha. Sem arquivo de estado separado.

## Lógica dos subcomandos

### `new <slug>`

1. Confirme que o slug não está presente em `matters/<slug>/` ou `matters/_archived/<slug>/`. Se reaproveitado, peça outro slug.
2. Rode a entrevista de intake:
   - **Cliente** (a parte que representamos, ou a unidade de negócio interna se for in-house)
   - **Contraparte** (o outro lado — pode ser múltiplas)
   - **Tipo de caso** (leia o perfil de prática do plugin para categorias típicas; para privacidade: RIPD (atividade de tratamento) | revisão de contrato de tratamento | requisição de titular | fiscalização ANPD | revisão de mecanismo de transferência internacional | incidente | outro)
   - **Nível de confidencialidade** (padrão | reforçado | clean-team — reforçado solicita cuidado extra em settings com contexto cruzado)
   - **Fatos-chave** (2–5 frases: o que é este caso, quem são os stakeholders, o que está em jogo)
   - **Overrides do caso ao playbook de prática** (ex.: "cliente exige notificação de incidente em 24h ao titular, mais restritivo que o playbook"; "contraparte é parceira estratégica — tom preservador da relação")
   - **Casos relacionados** (slugs de quaisquer casos conectados)
3. Escreva `matters/<slug>/matter.md` usando o template abaixo.
4. Semeie `matters/<slug>/history.md` com uma única entrada "Aberto".
5. Crie `matters/<slug>/notes.md` vazio.
6. **Não** troque automaticamente para o novo caso. Pergunte: "Quer trocar para `<slug>` agora? (`/privacidade:matter-workspace switch <slug>`)"

### `list`

Enumere `matters/*/matter.md`. Leia o front-matter ou as primeiras linhas de cada arquivo para extrair status. Imprima tabela:

| Slug | Cliente | Tipo de caso | Status | Aberto em | Ativo |
|---|---|---|---|---|---|

Marque o caso atualmente ativo com `*`. Inclua `_archived/*` sob heading separado "Arquivados" se houver algum.

### `switch <slug>`

1. Confirme que `matters/<slug>/matter.md` existe. Se não, ofereça `/privacidade:matter-workspace new <slug>`.
2. Edite a linha `Caso ativo:` no CLAUDE.md de prática para `Caso ativo: <slug>`.
3. Mostre o resumo do matter.md para o(a) usuário(a) confirmar que está no caso certo.

### `close <slug>`

1. Confirme que `matters/<slug>/` existe.
2. Anexe entrada "Encerrado" ao `matters/<slug>/history.md` com a data de hoje.
3. Mova `matters/<slug>/` → `matters/_archived/<slug>/`.
4. Se o caso encerrado era o caso ativo, defina `Caso ativo:` como `none — somente contexto de prática`.

### `none`

Defina `Caso ativo:` no CLAUDE.md de prática como `none — somente contexto de prática`. Confirme com o(a) usuário(a).

## Template `matter.md`

```markdown
[CABEÇALHO DE MATERIAL DE TRABALHO — conforme config do plugin ## Saídas — varia por papel; veja `## Quem está usando` no CLAUDE.md de prática]

# Caso: [Cliente] — [descrição curta]

**Slug:** [slug]
**Aberto em:** [YYYY-MM-DD]
**Status:** ativo
**Confidencialidade:** [padrão / reforçado / clean-team]

---

## Partes

**Cliente:** [nome]
**Contraparte:** [nome(s)]

## Tipo de caso

[RIPD | revisão de contrato de tratamento | requisição de titular | fiscalização ANPD | revisão de transferência internacional | incidente | outro — com uma linha de racional]

## Fatos-chave

[2–5 frases. O que é este caso. Quem são os stakeholders. O que está em jogo. O que o diferencia do playbook padrão.]

## Overrides específicos do caso

*Qualquer desvio do playbook de prática que se aplique a este caso e somente a este.*

- [ex.: "Notificação ao titular: cliente exige em 24h, em vez do padrão da casa de 72h."]
- [ex.: "Tom: preservador da relação — contraparte é parceira estratégica."]
- [ex.: "Lei aplicável e foro: cláusula contratual escolhe foro de Curitiba/PR."]

## Casos relacionados

- [slug — uma linha do porquê é relacionado]

## Notas de confidencialidade

[Se reforçado ou clean-team, descreva por quê. Quem pode ver arquivos do caso. Se contexto cruzado é permissível mesmo quando globalmente ligado.]
```

## Semente do `history.md`

```markdown
# Histórico: [Cliente] — [descrição curta]

Log append-only. Mais recente no topo.

---

## [YYYY-MM-DD] — Caso aberto

Intake concluído. Slug: `[slug]`. Status: ativo.
[Qualquer contexto inicial digno de preservação além do matter.md — ex.: "Aberto em resposta a requisição de titular recebida em [data]."]
```

## Contexto cruzado entre casos

O CLAUDE.md de prática tem flag `Contexto cruzado entre casos:`. Quando `off` (padrão), uma skill trabalhando no caso A **nunca lê** arquivos em `matters/B/` para qualquer outro `B`. Ponto. Esta é a garantia de confidencialidade que o setting existe para oferecer.

Quando `on`, uma skill pode ler entre pastas de caso apenas quando o(a) usuário(a) pedir explicitamente (ex.: "compare nossa posição sobre limitação de responsabilidade em contrato de tratamento nos últimos cinco casos de fornecedor"). Mesmo quando `on`, o padrão é carregar somente o caso ativo a menos que o(a) usuário(a) peça uma visão cruzada.

## O que esta skill NÃO faz

- **Rodar checagem de conflitos.** Conflitos são trabalho do(a) advogado(a)/banca; o intake captura o que o(a) usuário(a) declara.
- **Aplicar política de retenção.** Encerrar arquiva o caso; não apaga. Política de retenção está fora do escopo.
- **Rotear saídas automaticamente.** A skill substantiva decide onde escrever; esta skill diz *qual pasta* está ativa, não o que colocar dentro.
- **Decidir se contexto cruzado é apropriado.** Ela lê a flag e obedece.
