# Plugin Estudante de Direito

> Adaptação BR não oficial para estudantes de Direito. Calibrado para grade BR, Exame OAB FGV, NPJ. Doutrina e jurisprudência citadas são exemplos; verifique com seu(sua) professor(a). Veja `references/terminology-us-to-br.md`.

Modo aprendizado, não modo resposta. Drilling no estilo expositivo+jurisprudencial brasileiro que faz perguntas a VOCÊ e questiona raciocínio frouxo. Resumo de acórdãos/julgados, montagem de esquematizados, flashcards, correção de peças no padrão silogístico (fato → norma → subsunção → conclusão), preparo para arguição em sala, feedback de redação que nunca reescreve por você, e previsão de questões a partir de provas anteriores do(a) mesmo(a) professor(a). Calibrado para você — suas disciplinas, sua etapa (1º a 10º semestre), se você quer ser drillado(a) ou guiado(a).

**Toda saída é um andaime de estudo, não uma resposta-modelo. O plugin estrutura seu raciocínio, faz drilling no método tradicional brasileiro (expositivo + jurisprudência), e sinaliza onde você errou. Ele não escreve o esquematizado, o resumo de julgado, nem a peça por você — isso destruiria o propósito. Citações em materiais de estudo são marcadas para verificação (ABNT NBR 6023/10520).**

**Atenção — estudante não pode dar consulta.** A OAB exige inscrição (Lei 8.906/94, EOAB). Materiais aqui são para estudo; não use como parecer ou orientação a cliente real. Atendimento real só em NPJ supervisionado ou após inscrição na OAB.

## Para quem é

Estudantes de Direito brasileiros. Do 1º ao 10º semestre, incluindo preparação para o Exame da Ordem (OAB).

## Primeira execução: cold-start

Esse é sobre você, não sobre um escritório. Suas disciplinas, sua etapa (1º a 5º ano), seu estilo de aprendizagem — drill-me vs. me-explique. Traga materiais: resumos antigos, peças corrigidas, provas antigas (especialmente do(a) mesmo(a) professor(a)), simulados OAB 1ª fase, planos de ensino, monografias/TCCs. Dez a vinte itens é a meta; abaixo disso o perfil é sinalizado `DADOS LIMITADOS` e as skills subsequentes ficarão mais magras até você adicionar mais.

```
/estudante-direito:cold-start-interview
```

## Skills

Toda skill é invocada como `/estudante-direito:<nome-da-skill>`.

| Skill | Faz |
|---|---|
| `/estudante-direito:cold-start-interview` | Entrevista sobre-você + entrada de materiais — disciplinas, fase da OAB, estilo, materiais |
| `/estudante-direito:socratic-drill [disciplina]` | Drilling no método tradicional — ela pergunta, você responde, ela rebate. Não dá a resposta. |
| `/estudante-direito:case-brief [julgado]` | Resumo de acórdão/julgado no seu formato preferido (STF, STJ, TST, TJs) |
| `/estudante-direito:outline-builder [disciplina]` | Constrói ou estende um esquematizado no seu formato a partir de materiais de aula (estilo Pedro Lenza, Marcelo Novelino, Ricardo Alexandre) |
| `/estudante-direito:bar-prep-questions [disciplina]` | Questões padrão OAB FGV — 1ª fase (múltipla escolha) ou 2ª fase (peça + questões discursivas) — sinaliza posição majoritária vs. minoritária e súmulas vinculantes |
| `/estudante-direito:flashcards [disciplina]` | Gera ou drilla flashcards; baldes estilo Leitner; markdown por disciplina; modo `--session <n>` |
| `/estudante-direito:study-plan` | Constrói ou atualiza plano de estudo de longo prazo — fases, disciplinas por fragilidade, cronograma diário adaptativo a partir do histórico de sessões |
| `/estudante-direito:session <disciplina> <n>` | Sessão focada de N questões em uma disciplina; atualiza o plano com resultados |
| `/estudante-direito:irac-practice` | Corrige sua peça no padrão silogístico brasileiro (fato → norma → subsunção → conclusão, ou relatório → fundamentos → dispositivo do CPC art. 489) — estrutura, identificação de questões, normas, análise. Acompanha padrões entre sessões. Nunca reescreve. |
| `/estudante-direito:cold-call-prep [julgado]` | Prep para arguição em sala — prevê perguntas do(a) professor(a) e drilla |
| `/estudante-direito:legal-writing [path-ou-cole]` | Feedback estrutural em qualquer rascunho (peça, parecer, monografia, artigo) — nunca reescreve, em hipótese alguma. Apoia-se em Português Jurídico + Hermenêutica. |
| `/estudante-direito:exam-forecast [disciplina]` | Analisa provas anteriores do(a) mesmo(a) professor(a); prevê a próxima |

## O que significa "modo aprendizado"

Várias skills aqui (socratic-drill, case-brief no modo drill-me, cold-call-prep, irac-practice, legal-writing) são deliberadamente construídas para *não* lhe dar a resposta ou escrever a coisa por você. O ponto é que você aprende fazendo. Se quer uma resposta ou um rascunho pronto, use outra ferramenta. Este plugin é para o esforço.

**legal-writing é a mais rigorosa.** Lê seu rascunho e diz o que está fraco, mas não reescreve. Pedir reescrita retorna uma recusa educada + oferta de feedback estrutural mais específico. Isso é uma feature.

**outline-builder e case-brief seguem a mesma regra de forma mais branda.** O outline-builder monta o andaime — árvore de tópicos, slots de subtemas, marcadores para acórdãos — e faz perguntas no estilo expositivo+jurisprudencial enquanto você preenche as normas a partir das suas próprias anotações e do manual de doutrina (Tepedino, Tartuce, Gonçalves, Marinoni, Didier, Cláudio Brandão etc.). Não vai gerar um esquematizado populado só a partir do plano de ensino. O case-brief funciona da mesma forma em todos os modos (drill-me e me-explique): a skill dá o template e questiona o que você escreveu; não resume o julgado por você. Se você colar o texto do acórdão, ela pode extrair a linguagem do tribunal para os slots — isso é apontar para a fonte, não escrever por você.

## Integridade acadêmica

Antes de usar este plugin em qualquer trabalho avaliado — provas para casa, peças avaliadas, monografias, TCC, artigos para periódico — consulte o regimento da sua faculdade e a política do(a) professor(a) sobre uso de IA. Muitas instituições brasileiras já têm normas internas (Resolução CNE/CES 5/2018 fala em formação ética); políticas variam por curso e docente. Este plugin é desenhado para estudo e prática; usá-lo onde sua faculdade proíbe é infração disciplinar — as consequências são suas, não da ferramenta. Na dúvida, pergunte ao(à) professor(a) por escrito.

As skills em modo aprendizado (socratic-drill, irac-practice, legal-writing, cold-call-prep) são deliberadamente desenhadas para não dar a resposta nem escrever por você — essa é a pedagogia. É também o pressuposto de design para tratar alguns usos permitidos (drilling de prática que parece não-assistido) de modo diferente dos proibidos (ghostwriting de memorial avaliado). Não contorne as guardrails.

## Marcadores de confiança

Skills que geram conteúdo sinalizam confiança inline. Uma norma ou flashcard sem marcador é algo em que a skill está confiante (mas ainda não substitui sua conferência da fonte primária — Vade Mecum, manual de doutrina, jurisprudência STF/STJ/TST — antes da prova). Marcadores usados no plugin:

- `[VERIFICAR: alegação — confira a fonte]` — afirmado como provavelmente correto, mas confirme contra seu esquematizado, manual, cursinho de OAB ou fonte primária antes de confiar. Usado em bar-prep-questions, case-brief, flashcards, legal-writing, irac-practice.
- `[INCERTO: motivo específico]` — a skill não está confiante nessa chamada específica (posição minoritária, questão controvertida, divergência STF/STJ). Use seu próprio julgamento; cheque a fonte.
- `[LACUNA — preencher das anotações]` — marcador do outline-builder para um tópico onde a skill não tem fonte confiável e não vai inventar regra. Você preenche das anotações.
- `[FALTA JURISPRUDÊNCIA — norma posta, sem julgado ilustrativo]` — marcador do outline-builder onde a regra está, mas falta o acórdão paradigma (STF/STJ/TST).
- `[CONFERIR ANOTAÇÕES — professor(a) pode ter dado ênfase]` — marcador do outline-builder para áreas onde ênfase docente importa e a skill não tem como saber.
- `[EXCEÇÃO IMPRECISA — manual menciona exceção, achar a regra]` — marcador do outline-builder para exceção conhecida com detalhe não resolvido.
- `[INCERTO — enquadramento]` — marcador do exam-forecast notando que previsão é peso para tempo de estudo, não predição.

Confie mais nos flags do que na ausência deles — uma regra sem flag é algo em que a skill está confiante, mas o estudo para a prova ainda exige conferência da fonte.

## Conectores e verificação de citações

**Conecte uma ferramenta de pesquisa primeiro — as guardrails de citação dependem disso.** Sem ela, toda citação é marcada `[verificar]` e a nota ao revisor acima do entregável registra que as fontes não foram verificadas. O plugin funciona dos dois jeitos; só faz mais da verificação por você quando a ferramenta de pesquisa está conectada.

Os conectores de pesquisa jurídica neste plugin não são apenas fontes de dados — são a diferença entre uma citação verificada e uma citação que você precisa checar. Uma citação obtida via base de jurisprudência conectada (jurisprudência STF/STJ/TST, Vade Mecum, repositórios de tribunais) é marcada com sua fonte e pode ser rastreada. Uma citação vinda do conhecimento do modelo ou de busca web é marcada `[verificar]` ou `[verificar-pinpoint]` e deve ser conferida contra fonte primária antes de qualquer confiança. O plugin gradua suas citações para que seu tempo de verificação vá onde importa.

## Armazenamento

Seu perfil de estudo fica em `~/.claude/plugins/config/claude-for-legal/estudante-direito/CLAUDE.md` e sobrevive a atualizações do plugin. Todo o resto fica no seu diretório de trabalho:

```
estudante-direito/
├── flashcards/
│   └── [disciplina]/cards.md             # decks de flashcards por disciplina
├── irac-sessions/
│   └── [estudante]/
│       ├── [data]-[topico].md            # feedback de sessão individual
│       └── tracker.md                    # rastreamento de padrões entre sessões
├── writing-feedback/
│   └── [estudante]/
│       ├── [data]-[trabalho].md          # feedback de sessão individual
│       └── tracker.md                    # rastreamento de padrões entre sessões
└── exam-forecasts/
    └── [disciplina]/
        └── forecast-[YYYY-MM-DD].md      # previsões versionadas
```

## Testes & QA


## Como aprende

Seu perfil de estudo em `~/.claude/plugins/config/claude-for-legal/estudante-direito/CLAUDE.md` não é estático — ele melhora conforme você usa o plugin. Skills avisam quando uma saída usou um default que você devia calibrar. Você pode rerodar o setup, editar o arquivo direto, ou pedir a uma skill que registre uma nova posição.

## Notas

- Drill-me vs. me-explique é definido no cold-start; troque por sessão.
- Resumos de julgado e esquematizados usam SEU formato. Se você tem esquematizados existentes, aponte o cold-start para eles.
- O prep para OAB mira suas disciplinas fracas em ~/.claude/plugins/config/claude-for-legal/estudante-direito/CLAUDE.md. Ele vai voltar a elas.
- Toda skill que gera conteúdo sinaliza quando está incerta. Confie mais nos flags do que na ausência deles — uma regra sem flag é algo em que estou confiante; confira a fonte (Vade Mecum, manual, súmulas, jurisprudência) antes da prova de qualquer forma.
- **Lembrete OAB:** estudante não está habilitado a dar consulta jurídica (Lei 8.906/94 — EOAB). Estágio em advocacia exige inscrição como estagiário(a) na OAB. Atendimento real só dentro do NPJ supervisionado da sua faculdade (Resolução CNE/CES 5/2018).
