---
name: customize
description: >
  Customização guiada do seu perfil de estudo estudante-direito — mude uma coisa
  sem rerodar a entrevista cold-start inteira. Ajuste disciplinas atuais,
  estilo de aprendizagem, preferências de esquematizado, áreas da OAB,
  materiais-semente, ou cadência das sessões. Use quando o(a) usuário(a)
  disser "muda meu(minha) [coisa]", "adiciona uma disciplina", "atualiza
  meu perfil", "novo semestre", ou "customize".
argument-hint: "[nome da seção, ou descreva o que quer mudar]"
---

> **Nota BR.** Adaptação não oficial. Estrutura calibrada para grade brasileira (5 anos / 10 semestres), Exame OAB FGV, NPJ.

# /customize

## Quando isto roda

O(A) usuário(a) digitou `/estudante-direito:customize`. Quer mudar algo no
perfil de estudo — uma disciplina, preferência de estilo, área da OAB —
sem rerodar a entrevista cold-start e sem editar YAML na mão.

## O que fazer

1. **Leia a config.** Leia
   `~/.claude/plugins/config/claude-for-legal/estudante-direito/CLAUDE.md`.
   Se a config do plugin não existir ou ainda contiver `[PLACEHOLDER]`,
   diga:

   > Você ainda não rodou o setup. Rode `/estudante-direito:cold-start-interview`
   > primeiro — customize é para ajustar um perfil que você já tem.

2. **Mostre o mapa customizável.** Liste o que está no perfil, agrupado, com
   resumo de uma linha do valor atual:

   - **Perfil do(a) estudante** — nome, faculdade, etapa (1º a 5º ano / 1º a 10º semestre),
     seccional OAB alvo, NPJ/núcleo de prática em que está inscrito(a), monografia/TCC
   - **Disciplinas atuais** — nome, professor(a), caminho do plano de ensino, formato da
     prova (com/sem consulta, dissertativa/objetiva estilo OAB 1ª fase/peça + questões estilo
     2ª fase/mista), estilo de arguição
   - **Estilo de aprendizagem** — drill-me (expositivo+jurisprudencial puxado) vs.
     me-explique (esquematizado primeiro), quanto rebatimento quer, se o plugin
     reescreve a peça ou só critica estruturalmente
   - **Preferências de esquematizado** — formato (silogístico fato→norma→subsunção→conclusão,
     CPC 489 relatório/fundamentos/dispositivo, estilo Pedro Lenza/Marcelo Novelino/Ricardo Alexandre),
     profundidade de norma, se inclui princípios/policy, templates de esquematizado salvos
   - **Preparação OAB** — fase (1ª/2ª), área de opção da 2ª fase, disciplinas em rotação,
     sinalização de disciplina fraca, cadência 1ª fase (objetiva) vs. peça/discursiva
   - **Materiais-semente** — caminhos de manuais (Tepedino, Tartuce, Gonçalves, Marinoni,
     Didier, Cláudio Brandão), esquematizados prévios, peças corrigidas, provas antigas,
     simulados FGV, planos de ensino, monografias/TCC
   - **Workflow de estudo** — duração de sessão, cronograma Leitner de flashcards,
     cadência do exam-forecast, timing do cold-call-prep
   - **Integrações** — armazenamento de documentos (Google Drive / SharePoint / Box /
     Dropbox) status, fallbacks

3. **Pergunte o que quer mudar.**

   > O que você gostaria de ajustar? Escolha uma seção, ou descreva a mudança
   > nas suas palavras.

4. **Faça a mudança.** Mostre o valor atual, peça o novo, explique o que muda
   downstream, confirme, escreva na config.

   Exemplos:
   - *Adicionando nova disciplina:* "`/outline-builder` vai esquematizar para esta
     disciplina. `/flashcards` adiciona um deck. `/cold-call-prep` vai perguntar a leitura
     e o tema quando você invocar para esta disciplina."
   - *Estilo drill-me → me-explique:* "`/socratic-drill` não vai mais pedir que você
     responda primeiro — vai apresentar a norma e exemplo, depois testar aplicação."
   - *Adicionando área da OAB:* "`/bar-prep-questions` vai incluir esta disciplina na
     rotação e ponderar mais se marcada como fraca."

5. **Encerre.**

   > Pronto. Sua próxima saída vai refletir a mudança. Mais alguma coisa? Você pode
   > rodar `/estudante-direito:customize` a qualquer momento.

## Guardrails

- **Nunca delete uma seção.** Se o(a) usuário(a) quer "tirar" uma disciplina, ofereça
  marcar `[Arquivada — materiais-semente preservados]` e explique o que muda no
  comportamento de flashcards e esquematizados.
- **Sinalize inconsistência interna.** Se a mudança torna o perfil inconsistente
  (ex.: "me-explique" + "rebatimento máximo" socrático), sinalize a tensão.
- **Sinalize degradação de guardrail.** A regra "sem reescrever sua peça/redação"
  em `/legal-writing` e `/irac-practice` é load-bearing — o valor da skill é
  feedback estrutural, não ghostwriting. Se o(a) usuário(a) pedir para desligar,
  confirme que entende que o plugin não vai escrever o trabalho.
- **Uma mudança por vez.** Não rerode a entrevista inteira.
