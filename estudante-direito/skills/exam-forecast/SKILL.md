---
name: exam-forecast
description: >
  Analise provas anteriores do(a) mesmo(a) professor(a) para expor padrões —
  pesos de tema, armadilhas recorrentes de identificação de questões, tipos
  favoritos de hipótese, mix princípios-vs-doutrina — e preveja ênfases
  prováveis para a próxima prova. Use quando o(a) usuário(a) disser
  "o que cai na prova", "analise provas antigas", "preveja a prova", ou
  compartilhar provas anteriores.
argument-hint: "[nome da disciplina, com provas anteriores compartilhadas ou caminhos]"
---

> **Nota BR.** Adaptação não oficial. Provas de faculdade BR podem ser dissertativas, objetivas, mistas, com ou sem consulta ao Vade Mecum. A skill também funciona para provas anteriores do Exame OAB FGV — embora `/estudante-direito:bar-prep-questions` seja mais direto para isso.

# /exam-forecast

1. Carregue `~/.claude/plugins/config/claude-for-legal/estudante-direito/CLAUDE.md` → disciplina, professor(a), formato da prova, plano de ensino.
2. Aplique o workflow abaixo.
3. Receba provas anteriores (PDF, colagem, ou caminhos). Confirme tamanho da amostra.
4. Analise cada prova: formato, cobertura de temas, estilo de questão, densidade do enunciado fático, armadilhas recorrentes.
5. Análise de padrão cross-provas — o que é estável, o que varia.
6. Combine com o plano de ensino atual para gerar previsão: pesos de tema, formato, temas favoritos, ênfase de estudo.
7. Escreva `~/.claude/plugins/config/claude-for-legal/estudante-direito/exam-forecasts/[disciplina]/forecast-[YYYY-MM-DD].md`. Enquadrado como heurística de peso, não predição.

---

## Propósito

Cada professor(a) tem digital. As mesmas estruturas de hipótese recorrem. As mesmas armadilhas voltam. As mesmas proporções de tema se repetem. Estudantes com provas antigas estudam mais inteligente; sem elas, estudam mais duro. Esta skill analisa as provas antigas que você tem e expõe padrões.

Não é mágica. Previsão, não predição. A skill não pode te dizer o que cai na prova — pode dizer o que caiu antes e o que provavelmente recorre dado o plano de ensino.

## Disciplina de confiança

- Análise de padrão (quais temas apareceram, quantas questões por tema, com que frequência princípios vs. aplicação de norma) — confiante onde as provas estão à minha frente.
- Inferência sobre ênfase provável na próxima prova — `[INCERTO]` é o default; são previsões, não certezas. Enquadre explicitamente: "Com base nas [N] provas anteriores que você compartilhou, [tema] apareceu em [M]. Sua próxima prova pode enfatizar, ou o(a) professor(a) pode rodar — use como peso de tempo de estudo, não predição."
- Se só 1-2 provas anteriores disponíveis, diga explicitamente — qualquer padrão inferido de 1 prova é ruído.
- Se o(a) professor(a) é novo(a) (sem provas anteriores), a skill não consegue prever. Diga; recue ao plano de ensino: "estes são os temas cobertos" só.

## Carregar contexto

- `~/.claude/plugins/config/claude-for-legal/estudante-direito/CLAUDE.md` → disciplinas atuais, formatos de prova, plano de ensino se capturado
- Provas antigas fornecidas (PDF, texto colado, caminhos)
- Opcional: plano de ensino da disciplina atual (para "o que foi coberto até hoje")

**Se as provas antigas têm nome do(a) professor(a), use para casar padrões** (mesma-professor é a entrada de maior sinal). **Senão, case por disciplina e estrutura.** Não peça ao(à) usuário(a) que digite o nome do(a) professor(a) — use o que está nos materiais. Se voluntariar em conversa, tudo bem; não pergunte.

## Workflow

### Passo 1: Receber

- Para qual disciplina vamos prever?
- Quantas provas anteriores deste(a) professor(a) estão disponíveis?
- São da mesma disciplina, ou disciplinas diferentes do(a) mesmo(a) professor(a)?
- Algumas são em variantes (com consulta / sem consulta / oral / monitorada) diferentes do formato típico da próxima?
- Plano de ensino da disciplina atual?

Se menos de 3 provas: sinalize amostra magra. Inferência de padrão é mais fraca.
Se provas são de disciplinas diferentes: alguns padrões transferem (estilo de questão, princípios vs. aplicação); padrões específicos de tema não.

### Passo 2: Ler cada prova anterior

Para cada prova:

- Formato (número de questões, extensão, limite de tempo, com/sem consulta)
- Cobertura de tema (quais temas testados, em que proporção)
- Estilo de questão (caso para identificação, foco em questão única, ensaio principiológico, objetiva curta estilo OAB FGV, mista)
- Densidade do enunciado fático (caso longo, enunciado escasso com foco doutrinário, ou prompts principiológicos sem fatos)
- Armadilhas recorrentes (ex.: professor(a) sempre esconde questão de competência num caso fático limpo; sempre pergunta a exceção em vez da regra)
- Mix princípios vs. doutrina/jurisprudência
- Estruturas incomuns (objetiva + dissertativa híbrida, simulação de audiência, peça profissional, etc.)

### Passo 3: Análise cross-prova

Junte o que é consistente:

**Padrões estáveis (apareceram na maioria das provas):**
- Pesos de tema (ex.: "responsabilidade civil objetiva e dano moral somam 30% dos pontos consistentemente")
- Estilo de questão (ex.: "sempre um caso longo para identificação + duas hipóteses curtas")
- Temas favoritos do(a) professor(a) (ex.: "sempre testa terceiro de boa-fé em CC mesmo sendo tema secundário em aula")

**Padrões variáveis (apareceram em algumas, não todas):**
- Ensaios principiológicos (ex.: "apareceu em 2 de 4 — geralmente quando o semestre cobriu tema principiológico tarde")
- Com consulta vs. sem consulta
- Diferenças avaliação contínua vs. prova final

**Padrões ausentes que vale notar:**
- Temas cobertos em aula que NUNCA caíram em provas anteriores — não pule, mas não pondere pesado
- Temas em provas anteriores que NÃO estão no plano de ensino atual — provavelmente não voltam

### Passo 4: Previsão para a próxima prova

**Cabeçalho — obrigatório, primeira linha da previsão, tanto em chat quanto no arquivo salvo.** Por config do plugin `## Saídas`, toda saída de estudo carrega o cabeçalho verbatim. A previsão é saída de estudo. Não omita, reformule ou realoque. O cabeçalho não é disclaimer descartável; é a identidade da saída e impede que a previsão seja confundida com prova predita ou com orientação jurídica:

```
NOTAS DE ESTUDO — NÃO É ORIENTAÇÃO JURÍDICA
```

Combine análise de padrão com plano de ensino atual:

```markdown
NOTAS DE ESTUDO — NÃO É ORIENTAÇÃO JURÍDICA

# Previsão de Prova — [disciplina / professor(a)] — [data]

**Provas analisadas:** [N]
**Confiança da amostra:** [magra (<3) / moderada (3-5) / forte (6+)]
**Ressalvas:** [ex.: "uma das provas era com consulta; a próxima é sem. Transferência parcial."]

---

## Pesos de tema (histórico)

| Tema | Peso médio histórico | No plano atual? | Peso previsto |
|---|---|---|---|
| [tema 1] | [%] | [sim/parcial/não] | [maior / estável / menor] |

## Previsão de estilo

- **Formato provável:** [X casos para identificação + Y objetivas curtas + Z dissertativas, ou similar]
- **Densidade do enunciado:** [denso / esparso / misto]
- **Estilo do comando:** [uma única instrução ampla / múltiplas específicas / sub-itens em bullets]

## Temas favoritos do(a) professor(a) a observar

- [tema A] — apareceu em [M de N] provas. Pondere 3-5x o peso no plano.
- [tema B] — [padrão]
- [armadilha] — ex.: "esconde questão de competência (CPC 21-25) num caso de responsabilidade civil"

## Temas cobertos neste semestre mas raramente testados

[lista — não pule, mas não sobre-pondere]

## Recomendação de ênfase de estudo

Com base em padrões + cobertura atual do plano:

**Pesado:** [temas que provavelmente ancoram a prova — 40-50% do tempo]
**Moderado:** [temas de apoio — 30-40%]
**Sanity check:** [temas cobertos mas historicamente sub-representados — 10-20%, por garantia]

## [INCERTO — moldura]

Esta previsão deriva de [N] provas anteriores. Professores variam. Professores rodam. Temas enfatizados em anos anteriores podem ser despriorizados quando o plano muda. Trate como heurística de peso de tempo, não predição. A prova terá surpresas.
```

### Passo 5: Local de saída

Escreva em `~/.claude/plugins/config/claude-for-legal/estudante-direito/exam-forecasts/[disciplina]/forecast-[YYYY-MM-DD].md`. Versionada — se o(a) estudante consegue outra prova antiga no meio do semestre, rerode e anexe.

## Integração

- **outline-builder:** pesos da previsão alimentam profundidade do esquematizado — pondere profundidade nos temas pesados
- **flashcards:** temas pesados ganham mais cards gerados
- **bar-prep-questions:** irrelevante para OAB (tem modelo próprio); exam-forecast é para provas de faculdade
- **irac-practice:** use temas da previsão como áreas para drillar hipóteses no padrão silogístico

## Encerre com a árvore de decisão de próximos passos

Termine com a árvore de decisão por CLAUDE.md `## Saídas`. Customize opções para o que esta skill produziu — os cinco ramos default (rascunhar X, escalonar, obter mais fatos, observar e esperar, outra coisa) são ponto de partida, não amarra. A árvore é a saída; o(a) advogado(a)/professor(a) escolhe.

## O que esta skill não faz

- **Prever questões específicas.** Provas anteriores mostram padrões; não mostram o enunciado de amanhã.
- **Funcionar sem provas anteriores.** Sem provas antigas do(a) professor(a), recua a "aqui está o que o plano de ensino cobre, estude isso."
- **Substituir estudar tudo do plano.** Previsão é peso, não eliminação. Pular tema porque historicamente sub-representado é como estudante se queima.
- **Considerar mudanças que você não sabe.** Se o(a) professor(a) mudou foco neste ano (enfatizou um julgado novo nas aulas), a skill não vê a menos que você conte.
- **Funcionar confiavelmente com 1-2 provas.** Amostra magra. Sinalize.
