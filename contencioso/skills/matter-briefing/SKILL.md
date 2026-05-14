---
name: matter-briefing
description: Briefing profundo de uma matéria — postura atual, o que mudou, próximo prazo, perguntas em aberto, e recheck de reavaliação de risco, pronto antes de update ao GC ou call com escritório externo. Use quando o(a) usuário(a) disser "me dá um briefing de [matéria]", "onde estamos em [matéria]", ou precisar de leitura sobre matéria específica.
argument-hint: "[slug]"
---

# /matter-briefing

1. Carregar `~/.claude/plugins/config/claude-for-legal/contencioso/CLAUDE.md` → calibração de risco + stakeholders relevantes.
2. Seguir o workflow e referência abaixo.
3. Ler `~/.claude/plugins/config/claude-for-legal/contencioso/matters/[slug]/matter.md` + `~/.claude/plugins/config/claude-for-legal/contencioso/matters/[slug]/history.md` + linha do log em `_log.yaml`.
4. Produzir briefing: postura atual, o que mudou desde o último update, próximo prazo, perguntas em aberto, recheck de reavaliação de risco ("o campo `risk:` ainda reflete a realidade?").
5. Flag staleness: se `last_updated` > 30 dias, diga.

---

# Briefing de Matéria

## Propósito

Dar ao(à) advogado(a) leitura limpa de uma matéria no tempo que leva andar até uma sala de reunião. Postura atual, o que mudou, o que vem, o que vale reconsiderar.

## Carregar contexto

- `~/.claude/plugins/config/claude-for-legal/contencioso/matters/_log.yaml` — linha estruturada
- `~/.claude/plugins/config/claude-for-legal/contencioso/matters/[slug]/matter.md` — intake narrativo
- `~/.claude/plugins/config/claude-for-legal/contencioso/matters/[slug]/history.md` — log de eventos
- `~/.claude/plugins/config/claude-for-legal/contencioso/CLAUDE.md` — calibração de risco (para que "risk: high" signifique algo específico, não genérico)

**Portão de conflitos — não-bypassável.** Antes de briefar, conferir `_log.yaml` para o slug. Se não estiver em `_log.yaml`, recuse e roteie:

> "Não vejo [matter slug] no registro. Rode `/contencioso:matter-intake` primeiro para a conferência de conflito rodar e o workspace ser criado. Não construo briefing em matéria que não foi intaken — a conferência de conflito é o portão."

## Input

Slug (obrigatório). Se ambíguo ou ausente, peça ao(à) usuário(a) que escolha de lista de matérias ativas.

## O briefing

```markdown
[CABEÇALHO DE MATERIAL DE TRABALHO — conforme config do plugin ## Saídas — varia por papel; ver `## Quem está usando`]

# [Nome da Matéria] — Briefing em [hoje]

**Status:** [status / estágio]
**Risco:** [rating] ([severidade] × [probabilidade])
**Materialidade:** [categoria]
**Escritório externo:** [banca — líder]
**Último update:** [data] [flag ⚠️ STALE se >30d]
**Conflitos:** [status — flag ⚠️ se `pending` ou `not-run`]

---

## Resumo em um parágrafo

[Postura atual. O que estamos fazendo e por quê. Nomeie o fato pivô se um foi capturado.]

## O que mudou recentemente

[Últimas 3-5 entradas de history.md, mais recente primeiro. Se o histórico está fino, diga.]

## O que vem

- **Prazo imediato:** [next_deadline + o que é]
- **Marcos próximos:** [qualquer coisa datada em matter.md ou histórico recente]
- **Decisões pendentes:** [perguntas em aberto flagadas em matter.md]

## Exposição

[Faixa + qualquer mudança desde intake. Se reservada, provisão atual + se recalibração está atrasada — CPC 25.]

## Responsáveis internos

[Quem está envolvido; se alguém deveria estar e não está]

## Recheck de reavaliação de risco

*Um prompt, não uma resposta.*

- `risk: [rating]` ainda parece certo, ou o caso se moveu?
- `materiality: [categoria]` ainda combina? (Novos fatos podem empurrar para provisão ou divulgação.)
- Algum novo stakeholder que a matéria precisa (ex. CISO vira relevante após desenvolvimento de incidente)?

## Perguntas em aberto

[De matter.md e qualquer coisa não resolvida em history]

## Para a conversa

[Se o(a) usuário(a) especificou propósito — "me briefe antes da call com o escritório externo" — customize a seção final: perguntas a fazer, decisões a obter, updates a extrair. Se nenhum propósito dado, omita esta seção.]
```

## Staleness

Se `last_updated > 30 dias atrás`: flag no topo E sugira rodar `/contencioso:matter-update [slug]` depois da reunião para capturar o que foi discutido.

## Tom

Não é marketing. Diga o que se sabe; flag o que não. Se a matéria tem histórico fino e foi recém-aberta, o briefing é curto — e é correto. Não infle.

## Feche com a árvore de próximos passos

Feche com a árvore de próximos passos conforme CLAUDE.md `## Saídas`. Customize as opções ao que esta skill acabou de produzir — os cinco ramos default (redigir o X, escalar, buscar mais fatos, observar e esperar, outra coisa) são partida, não trava. A árvore É a saída; o(a) advogado(a) escolhe.

## O que esta skill NÃO faz

- Prever desfechos. Rating de risco é julgamento capturado, não previsão.
- Recomendar estratégia. Traz perguntas à tona; o(a) advogado(a) responde.
- Re-triagem. Se o(a) usuário(a) quer re-triagem, é um `/matter-update` com mudanças de campo — esta skill lê, não escreve.
