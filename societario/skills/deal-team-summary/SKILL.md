---
name: deal-team-summary
description: >
  Agrega achados de diligência em um briefing ao time da operação na altitude
  certa para o público — sumário executivo para liderança, sumário operacional
  para o time. Use quando o(a) usuário(a) disser "brief o time da operação",
  "qual o estado da diligência", "sumariza achados para [público]",
  "atualização do deal" ou na cadência de briefing.
---

# Deal Team Summary

## Contexto de matéria

**Contexto de matéria.** Cheque `## Workspaces de matéria` no CLAUDE.md nível-prática. Se `Habilitado` é `✗` (padrão para usuários(as) in-house), pule o resto deste parágrafo — skills usam contexto nível-prática e o aparato de matéria fica invisível. Se habilitado e não houver matéria ativa, pergunte: "Para qual matéria é isto? Rode `/societario:matter-workspace switch <slug>` ou diga `nível-prática`." Carregue o `matter.md` da matéria ativa para contexto específico e overrides. Grave saídas na pasta da matéria em `~/.claude/plugins/config/claude-for-legal/societario/matters/<matter-slug>/`. Nunca leia arquivos de outra matéria a menos que `Contexto cross-matter` esteja `on`.

---

## Propósito

O(a) líder da operação não lê 200 achados. Lê: o que é material, o que mudou desde o último briefing, o que precisa de decisão. Esta skill compacta a saída de diligência no nível certo para o(a) leitor(a).

## Carregar contexto

- `~/.claude/plugins/config/claude-for-legal/societario/CLAUDE.md` → Briefing ao time da operação (cadência, formato, o que o negócio lê)
- `~/.claude/plugins/config/claude-for-legal/societario/deals/[code]/deal-context.md` → líder da operação, timeline
- Achados atuais da saída de diligence-issue-extraction

## Camadas de público

Conforme `~/.claude/plugins/config/claude-for-legal/societario/CLAUDE.md` — o que o negócio lê vs. o que vai para o arquivo. Camadas padrão:

| Público | Recebe | Não recebe |
|---|---|---|
| **Conselho / patrocinador exec** | Top 3-5 achados materiais, impacto em preço/estrutura, itens de decisão | Detalhe por categoria, achados verdes, processo |
| **Líder da operação** | Todos os reds, todos os yellows, progresso, itens de decisão, próximos passos | Detalhe de achados verdes |
| **Time operacional** | Tudo — achados completos, status por categoria, gaps | Nada retido |

Pergunte qual camada se não estiver óbvio.

## O sumário

### Camada exec

```markdown
[CABEÇALHO DE MATERIAL DE TRABALHO — conforme config do plugin ## Outputs — varia por papel; ver `## Quem está usando`]

> Este briefing agrega achados privilegiados de diligência e herda o sigilo e a confidencialidade das fontes. Distribuição fora do círculo de sigilo profissional (inclusive a times mais amplos do negócio) pode quebrar a proteção — confirme a lista de destinatários antes de enviar.

# [Código da operação] — Briefing de Diligência — [data]

**Status:** [No prazo / Achados identificados / Achados materiais]
**Cobertura:** [X]% do VDR revisado

## Achados materiais

[Máximo 3-5. Um parágrafo cada. O que é, por que importa para a operação, o que estamos fazendo.]

## Decisões necessárias

- [ ] [Decisão específica — ajuste de preço, pedido de indenidade, gatilho de walk-away]
  — [quem decide] — [até quando]

## Desde o último briefing

[O que mudou. Novos achados, achados resolvidos, progresso de cobertura.]
```

### Camada do(a) líder da operação

O mesmo, mais:

```markdown
## Todos os achados em aberto por categoria

### 🔴 Vermelho
[Título do achado + uma linha — link para achado completo para detalhe]

### 🟡 Amarelo
[idem]

## Progresso

| Categoria | Docs revisados | Cobertura | Vermelhos | Amarelos | Status |
|---|---|---|---|---|---|
| [nome] | [N/M] | [%] | [N] | [N] | [Completo / Em andamento / Bloqueado] |

## Gaps e follow-ups

- [Itens da lista suplementar em aberto]
- [Perguntas para a administração]

## Próximas 72 horas

[O que será revisado, quais briefings estão marcados]
```

### Camada do time operacional

Detalhe completo de cada achado. Mesma estrutura acima, mas todo achado recebe o bloco completo em formato da casa, não uma linha só.

## Deltas

Se este é briefing recorrente (conforme cadência do `~/.claude/plugins/config/claude-for-legal/societario/CLAUDE.md`), comece com o que mudou:

- Novos achados desde o último briefing
- Achados promovidos/rebaixados em severidade
- Achados resolvidos (consentimento obtido, questão esclarecida)
- Movimento de cobertura

Líderes de operação se importam mais com movimento do que com estado. "Ainda 12 amarelos" é menos útil do que "2 novos amarelos, 3 resolvidos".

## Handoffs

- **De diligence-issue-extraction:** Esta skill lê os achados acumulados.
- **Para closing-checklist:** Qualquer item de "decisão necessária" que vire condição de fechamento (CP) vai para o checklist.

## Encerre com a árvore de decisão de próximos passos

Encerre com a árvore de decisão de próximos passos conforme CLAUDE.md `## Outputs`. Customize as opções para o que esta skill acabou de produzir — os cinco ramos padrão (redigir o X, escalar, buscar mais fatos, observar e aguardar, outra coisa) são ponto de partida, não amarração. A árvore é a saída; o(a) advogado(a) escolhe.

## O que esta skill não faz

- Não faz a chamada de materialidade — reporta as chamadas que foram feitas na hora da extração.
- Não decide o que o time da operação faz sobre um achado — traz a decisão à tona.
- Não distribui o briefing — minuta, humano(a) envia.
