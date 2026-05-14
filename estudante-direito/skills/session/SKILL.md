---
name: session
description: >
  Roda uma sessão focada de N questões em uma disciplina — 1ª fase OAB
  (objetiva), 2ª fase (peça/discursiva), ou flashcards. Rastreia
  desempenho e atualiza o plano de estudo. Use quando o(a) usuário(a)
  disser "roda 10 questões de [disciplina]", "faz uma sessão em
  [disciplina]", "vamos 5 cards de [disciplina]", ou quer drillar número
  fixo de questões com o plano adaptando.
argument-hint: "<disciplina> <n> [--1afase | --2afase | --flashcards]"
---

> **Nota BR.** Adaptação não oficial. Flags renomeadas para o Exame OAB FGV: `--1afase` (80 questões objetivas, 17 disciplinas) e `--2afase` (peça profissional + 4 questões discursivas por área de opção).

# /session

1. Parseie `$ARGUMENTS` — disciplina e N. Se faltando, pergunte:
   > Qual disciplina e quantas questões? (ex.: `Civil 10` ou `Penal 5 --2afase`.)
2. Carregue `~/.claude/plugins/config/claude-for-legal/estudante-direito/CLAUDE.md` → seccional OAB, fase do exame, área de opção 2ª fase, disciplinas fracas.
3. Carregue `~/.claude/plugins/config/claude-for-legal/estudante-direito/study-plan.yaml` se existir. Leia `session_history` desta disciplina para ponderar subtópicos onde o(a) estudante errou.
4. Roteie pela flag:
   - `--1afase` (default para disciplinas em rotação 1ª fase OAB): carregue skill `bar-prep-questions`, rode N questões objetivas. Aplique tratamento de divergência (veja `## Tratamento de divergências` daquela skill). Rotule cada uma `[lei seca]`, `[jurisprudência consolidada]`, ou `[divergência majoritária/minoritária]`.
   - `--2afase`: carregue `bar-prep-questions`, rode N prompts de peça ou questão discursiva da área de opção. Corrija pelo rubrica 2ª fase.
   - `--flashcards`: carregue skill `flashcards`, rode N cards em modo `--drill`.
5. Rode N questões uma por vez. Após cada, explique certo/errado e sinalize a norma quando posições divergem (doutrina vs. jurisprudência STF/STJ/TST).
6. No fim da sessão, escreva resultados:
   - Se `study-plan.yaml` existe: anexe a `session_history` pelo schema da skill `study-plan`.
   - Senão: escreva em `~/.claude/plugins/config/claude-for-legal/estudante-direito/session-history.yaml`.
7. Reporte:
   - Score: X/N (percentual)
   - Erradas: lista com tags de subtópico
   - Subtópicos fracos desta sessão
   - Padrão vs. sessões anteriores nesta disciplina (se histórico tem 2+ anteriores)
   - O que o plano agora recomenda em seguida
