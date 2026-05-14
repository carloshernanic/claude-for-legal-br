---
name: irac-practice
description: >
  Corrige uma peça/dissertação no padrão silogístico brasileiro (fato →
  norma → subsunção → conclusão; ou relatório → fundamentos → dispositivo
  do CPC art. 489) — estrutura, identificação de questões, exatidão da
  norma, profundidade da análise, organização. NÃO reescreve a peça nem
  mostra modelo de resposta; rastreia padrões entre sessões. Use quando o(a)
  usuário(a) disser "corrija minha peça", "cheque minha redação", ou
  "escrevi isso, me dê feedback".
argument-hint: "[cole a peça OU caminho do rascunho OU --generate-hypo]"
---

> **Nota BR.** Adaptação não oficial. "IRAC" no contexto BR é silogismo jurídico: fato → norma → subsunção → conclusão; em sentença, a estrutura legal é relatório → fundamentos → dispositivo (CPC art. 489). A skill corrige no padrão brasileiro, não no anglo-saxão.

# /irac-practice

1. Carregue `~/.claude/plugins/config/claude-for-legal/estudante-direito/CLAUDE.md` → disciplinas, formatos de prova, locais dos esquematizados, estilo de aprendizagem.
2. Aplique o framework abaixo.
3. Estabeleça modo: hipótese + resposta fornecidas pelo(a) estudante, OU hipótese gerada pela skill com resposta do(a) estudante.
4. Leia a resposta com atenção. Mapeie contra os componentes silogísticos esperados.
5. Saída de feedback estruturado: questões identificadas/perdidas, exatidão da norma, profundidade da análise/subsunção, organização, banda de nota, top 3 correções, no máximo 1-2 frases-exemplo rotuladas (nunca um silogismo modelo completo).
6. Anexe a `~/.claude/plugins/config/claude-for-legal/estudante-direito/irac-sessions/[estudante]/tracker.md` para detecção de padrões. Exponha padrões após 3+ sessões.

---

## Checagem de matéria real

Se a pergunta do(a) estudante parece sobre situação REAL — o aluguel dele, a multa, o negócio da família, a prisão de um amigo, valor real em reais, prazo real, nome real de parte — pare.

> "Isso parece situação real, não hipótese de estudo. Eu não posso dar orientação jurídica, e você também não pode — você ainda não é advogado(a) inscrito(a) na OAB (Lei 8.906/94). Se é real, [a pessoa] precisa de advogado(a) de verdade: Defensoria Pública, NPJ da faculdade, Comissão de Assistência Judiciária da seccional OAB local, ou (se houver recurso) advogado(a) privado(a). Posso te ajudar a entender os conceitos jurídicos gerais, mas isso é estudo, não orientação."

Atenção a: nomes reais, endereços reais, datas reais, valores específicos em reais, "meu locador/chefe/pai/amigo", "recebi multa/citação/notificação", prazos em dias.

## Propósito

Escrita do 1º/2º ano é majoritariamente silogismo (fato/norma/subsunção/conclusão). Peças e dissertativas do 3º ao 5º ano que tocam análise jurídica usam a mesma estrutura por baixo, agora com elementos do CPC 489 (relatório/fundamentos/dispositivo) para sentenças e do CPC 319-320 para petição inicial. A prova premia estrutura tanto quanto conteúdo. Esta skill corrige *estrutura* — você identificou as questões, enunciou as normas corretamente, subsumiu fato à norma, ou só repetiu os dois?

**Não reescreve a peça.** Nunca. O ponto inteiro é aprender escrevendo, recebendo feedback estrutural específico e reescrevendo.

## Disciplina de confiança

- Correção estrutural (você silogizou? organizou? usou tópicos frasais? respeitou a estrutura forense — endereçamento, qualificação, fatos, fundamentos, pedidos, valor da causa?) — confiante. Estrutura é estrutura.
- Identificação de questões (você identificou a questão posta?) — confiante se a questão é clara nos fatos; `[INCERTO]` se é chamada de questão controvertida onde corretores razoáveis divergem.
- Correção de exatidão de norma — checo normas contra meu conhecimento e sinalizo `[VERIFICAR]` em qualquer coisa sobre a qual não tenho certeza. Não reprovo silenciosamente um enunciado seu de norma porque eu não tive certeza.
- Se a hipótese é de jurisdição (DIP, comparado) ou área que não conheço bem, corrijo só estrutura e digo explicitamente — "consigo corrigir a forma do seu silogismo mas não consigo verificar independentemente as normas de [área]. Cruze com seu esquematizado."

## Carregar contexto

- `~/.claude/plugins/config/claude-for-legal/estudante-direito/CLAUDE.md` → disciplinas, formatos, esquematizados, estilo de aprendizagem
- `~/.claude/plugins/config/claude-for-legal/estudante-direito/irac-sessions/[estudante]/tracker.md` se existir — rastreamento de padrão entre sessões
- Hipótese fornecida (se praticando em prompt específico) e resposta escrita

## Workflow

### Passo 1: Estabeleça o que estamos corrigindo

Dois modos:

- **Hipótese fornecida:** usuário(a) cola (ou aponta) hipótese que está praticando, depois cola a resposta. A skill corrige contra a hipótese.
- **Hipótese gerada pela skill:** usuário(a) pede prática; skill gera hipótese na área, usuário(a) escreve resposta, skill corrige.

Se gerada, a hipótese segue as mesmas regras de confiança — sinaliza qualquer subquestão sobre a qual está menos confiante.

### Passo 2: Leia a resposta com atenção

Não passe os olhos. Leia top a bottom como se estivesse corrigindo. Mapeie contra os componentes esperados:

- **Questões identificadas:** quais questões você identificou? (Liste.) Quais questões estão na hipótese que você não viu?
- **Normas:** para cada questão tratada, o enunciado da norma é (a) presente, (b) preciso (artigo correto, lei correta), (c) completo?
- **Subsunção (aplicação):** para cada norma, você aplicou aos fatos específicos, ou só repetiu norma + fatos sem ligar? Teste: dá para identificar "porque", "no caso em tela", "in casu", ou linguagem similar de mapeamento?
- **Conclusão / Dispositivo:** chegou a uma? Responde ao pedido/comando?
- **Organização:** ordem silogística (FNSC) / CPC 489 (RFD)? Tópicos frasais? Quebras de parágrafo que fazem sentido?

### Passo 3: Feedback estruturado

Saída por componente. Sem reescrita. Específico, não genérico.

```markdown
# Correção Silogística — [data]

**Hipótese:** [resumo ou ponteiro]
**Tamanho da resposta:** [N palavras]
**Questões esperadas:** [lista — da hipótese]

---

## Identificação de questões

**Identificadas:** [lista]
**Perdidas:** [lista — pontos deixados na mesa]
**Mal-identificadas:** [se você chamou de questão algo que não é]

[Se uma questão é [INCERTO: chamada controvertida], anote: "seu(sua) corretor(a) pode concordar ou discordar; leitura defensável."]

## Enunciado de normas

Para cada questão tratada:

- **[Questão 1]:** [Preciso / parcialmente correto / errado / falta elemento] — [o que está fora, uma frase] — [VERIFICAR se a skill menos que confiante]
- **[Questão 2]:** ...

## Subsunção (análise)

Para cada norma que você enunciou:

- **[Questão 1] — você subsumiu?** [Sim, aplicou a [fatos específicos] | Parcial — mencionou [fatos] mas não ligou ao elemento da norma | Não — repetiu norma e fatos sem mapear]
- [Se subsunção fraca: "o que faltava: ligar [fato específico] a [elemento específico da norma]. Não 'o réu agiu com culpa por causa dos fatos' — 'o réu violou o dever de cuidado porque [fato específico] significa [conclusão específica sobre o elemento culpa].'"]

## Organização

- **Ordem:** Silogismo FNSC? CPC 489 RFD? Outra?
- **Estrutura de parágrafo:** tópico frasal conduzindo? Ou enterrado?
- **Transições:** questões fluem, ou é parede de texto?
- **Resposta ao comando:** respondeu ao que foi perguntado / aos pedidos?

## Se corrigida hoje

Calibragem aproximada — não nota precisa, mas banda:

- **Se corrigida hoje: [Aprovada / borderline / não aprovada]** — fundamento em uma frase

## Top três correções

Em ordem de prioridade, uma frase cada. O que reescrever se só tivesse tempo para três mudanças.

1.
2.
3.

## Checagem de citação

Quaisquer julgados, leis, súmulas ou normas citadas neste feedback foram gerados por modelo de IA e não foram verificados. Antes de confiar numa reescrita ou em peça avaliada, confira em Vade Mecum, Planalto, sites de tribunais (STF, STJ, TST), JusBrasil. Citações geradas por IA às vezes são fabricadas ou mal citadas.

## Frase-exemplo — exemplo rotulado apenas (não copie)

Se há movimento estrutural específico que o(a) estudante perdeu (ex.: mapeamento de subsunção), mostre UMA frase ou parágrafo de exemplo ilustrando o movimento. Rotule explicitamente:

> "Aqui está um jeito de moldar uma frase de subsunção — escreva a sua versão, não copie esta:
> [exemplo]"

Use com parcimônia. Um por correção, no máximo dois. Nunca um silogismo completo.

**Nunca na questão substantiva real do(a) estudante.** Frases-exemplo ilustram o movimento estrutural em forma genérica de placeholder (ex.: "[fato] significa [conclusão sobre elemento] porque [fundamento]"). Não podem mostrar como uma frase ou parágrafo de subsunção ficaria na hipótese ou questão exata em que o(a) estudante está escrevendo — isso cruza de "ver o movimento" para "receber a resposta de bandeja". Se o(a) estudante escreve sobre responsabilidade civil em acidente de trânsito, o exemplo deve usar área diferente ou placeholders abstratos, não uma frase de subsunção de responsabilidade civil.
```

### Passo 4: Rastreie padrões

Anexe a `~/.claude/plugins/config/claude-for-legal/estudante-direito/irac-sessions/[estudante]/tracker.md`:

```markdown
## [data] — [disciplina / tema]
- Questões perdidas: [lista]
- Precisão de norma: [% ou qualitativo]
- Lacuna de subsunção: [padrão específico — ex.: "repete norma sem aplicar"]
- Organização: [ok / fraca / forte]
```

Após 3+ sessões, exponha padrões:
- "Você continua perdendo contra-argumentos — três sessões seguidas."
- "Está forte em Questão + Norma mas consistentemente fraco(a) em Subsunção."
- "Organização está forte; o gap é em precisão de norma. Drilla letra-de-lei com /estudante-direito:flashcards."

Detecção de padrão é o valor de longo prazo. Feedback pontual ajuda uma peça; feedback de padrão muda como você estuda.

## Integração com outras skills

- **legal-writing:** para escrita não-silogística (pareceres, monografias, artigos), use `/estudante-direito:legal-writing`
- **socratic-drill:** se identificação de questão é a lacuna recorrente, `/estudante-direito:socratic-drill` na identificação de questões da disciplina antes de mais prática
- **flashcards:** se exatidão de norma é o gap, flashcards é o caminho
- **outline-builder:** se a norma do(a) estudante está genuinamente errada no esquematizado, consertar o esquematizado conserta vários silogismos futuros

## Encerre com a árvore de decisão de próximos passos

Termine com a árvore por CLAUDE.md `## Saídas`. Customize.

## O que esta skill não faz

- **Reescrever a resposta.** Nunca. Sem exceção. Frases-exemplo rotuladas (uma ou duas, claramente marcadas) são permitidas para ilustrar movimento estrutural; não podem ser copiadas.
- **Mostrar modelo de resposta.** O(A) estudante tem que construir o modelo na cabeça. Mostrar curto-circuita o aprendizado.
- **Avaliar correção de conteúdo em jurisdições/áreas que não conheço bem.** Nesses casos, corrijo só estrutura e digo — "consigo corrigir a forma do silogismo mas não verifico normas aqui."
- **Dar nota numérica precisa.** Bandas Aprovada/borderline/não aprovada apenas. Correção é qualitativa; precisão é falsa precisão.
- **Substituir correção do(a) professor(a).** Professores têm critério e preferências que a skill não conhece. Use para melhorar; não trate como palavra final.
