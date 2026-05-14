---
name: cold-call-prep
description: >
  Preparação para arguição em sala — prevê as perguntas prováveis do(a)
  professor(a) e drilla socraticamente, sinalizando onde você está frágil
  para saber o que reler antes da aula. Use quando o(a) usuário(a) disser
  "prep para aula amanhã", "arguição [julgado]", "o que o(a) professor(a) pode
  perguntar sobre", ou apontar a leitura indicada.
argument-hint: "[nome do julgado, ou cole o texto, ou caminho da leitura]"
---

> **Nota BR.** Adaptação não oficial para faculdade de Direito BR. "Arguição em sala" cobre tanto o método expositivo+jurisprudencial brasileiro quanto seminários e Núcleo de Prática Jurídica. Tribunais default: STF, STJ, TST, TJs.

# /cold-call-prep

1. Carregue `~/.claude/plugins/config/claude-for-legal/estudante-direito/CLAUDE.md` → lista de disciplinas, professores, estilo de aprendizagem.
2. Aplique o workflow abaixo.
3. Identifique a leitura (nome do julgado + citação, professor(a), disciplina, contexto do plano de ensino).
4. Preveja 6-10 perguntas prováveis distribuídas por categorias (Fatos / Tese / Fundamentos / Aplicação / Política/Princípios), ponderadas pelas tendências conhecidas do(a) professor(a).
5. Drilla no padrão expositivo+jurisprudencial — pergunte, espere, rebata, estreite quando travar. Não dê respostas.
6. Resumo pós-drill: forte/frágil/perdeu; o que reconferir antes da aula.

---

## Checagem de matéria real

Se a pergunta do(a) estudante parece sobre situação REAL — o aluguel dele, a multa, o negócio da família, a prisão de um amigo, valor real, prazo real, nome real de parte — pare.

> "Isso parece situação real, não hipótese de estudo. Eu não posso dar orientação jurídica, e você também não pode — você ainda não é advogado(a) inscrito(a) na OAB (Lei 8.906/94). Se é real, [a pessoa] precisa de advogado(a) de verdade: Defensoria Pública, NPJ da faculdade, Comissão de Assistência Judiciária da seccional OAB local, ou (se houver recurso) advogado(a) privado(a). Posso te ajudar a entender os conceitos jurídicos gerais, mas isso é estudo, não orientação."

Atenção a: nomes reais, endereços reais, datas reais, valores específicos em reais, "meu locador/chefe/pai/amigo", "recebi multa/citação/notificação", prazos em dias. Qualquer um é gatilho.

## Propósito

Arguição vive ou morre na preparação. O(A) professor(a) leu o acórdão dezenas de vezes e conhece as perguntas; o(a) estudante leu uma vez. Esta skill estreita o gap — prevê os padrões prováveis de pergunta para o julgado, drilla o(a) estudante neles e expõe o que não está travado.

Não substitui ler o acórdão. Uma checagem de que você efetivamente leu.

## Disciplina de confiança

- Quando o(a) estudante fornece o texto do acórdão ou trechos de manual: previsões baseadas no texto real. Confiante.
- Quando o(a) estudante fornece só o nome/número do julgado (RE, REsp, HC, ADI): previsão baseada no que conheço do julgado. Sinalizo `[INCERTO]` em qualquer pergunta que dependa de detalhes do acórdão sobre os quais não tenho certeza. Recomendo fortemente que cole o acórdão ou o tratamento do manual primeiro.
- Se não conheço bem o julgado: digo. "Não tenho leitura confiável deste acórdão — cole o texto ou o tratamento do manual (Tepedino, Tartuce, Gonçalves, Marinoni, Didier, Cláudio Brandão) e trabalho a partir disso. Caso contrário minhas perguntas são chutes informados."

## Carregar contexto

- `~/.claude/plugins/config/claude-for-legal/estudante-direito/CLAUDE.md` → disciplinas atuais, professores, estilo de aprendizagem
- Fornecido pelo(a) usuário(a): nome do julgado / texto do acórdão / páginas de manual / lista de leituras

## Workflow

### Passo 1: Identifique a leitura + professor(a)

- Nome e citação do julgado (ex.: STF, RE 605.708; STJ, REsp 1.737.428; TST, RR-XX)
- Professor(a) (da lista em ~/.claude/plugins/config/claude-for-legal/estudante-direito/CLAUDE.md — tom e foco variam)
- Disciplina / área
- Onde este julgado se encaixa no plano de ensino (contexto — é o primeiro julgado do tema, um julgado que estreita, um contraexemplo?)

### Passo 2: Preveja as perguntas

Professores arguem em padrões recorrentes. Preveja nestas categorias:

**Nível-fatos (esquentar):**
- Quem são as partes? O que aconteceu? Histórico processual?
- O que decidiu o juízo a quo? O tribunal recorrido?
- Por que este julgado está no plano? Que tema ilustra?

**Tese / norma:**
- Qual a tese? Uma frase.
- Qual a norma que sai do julgado — o takeaway portátil?
- Como você redigiria a norma se estivesse no seu esquematizado? Se é tese de repercussão geral (Tema STF) ou tema repetitivo (STJ), saberia citar?

**Fundamentos / Ratio:**
- Por que o tribunal decidiu assim?
- Que argumentos o tribunal rejeitou?
- Houve voto vencido? O que defendia?

**Aplicação / hipóteses:**
- E se [fato X] fosse diferente — mesmo resultado?
- Como este julgado se compara a [precedente anterior do plano]?
- Qual o limite da norma? Onde ela para?

**Princípios / política:**
- Qual o princípio que o tribunal protege?
- A norma faz sentido? Abordagens alternativas (CC, CDC, doutrina majoritária divergente)?

**Sabor específico do(a) professor(a) (das notas em ~/.claude/plugins/config/claude-for-legal/estudante-direito/CLAUDE.md):**
- Se o(a) professor(a) é conhecido(a) por arguições com muitas hipóteses, pondere Aplicação/Hipóteses
- Se foco em princípios/política, pondere Princípios
- Se foco em fato (Socrático puro, à brasileira), pondere Fatos + Tese

Escolha 6-10 perguntas pelas categorias. Ranqueie por probabilidade de ser perguntada primeiro (Fatos geralmente vem antes, depois Tese, depois categorias mais difíceis).

### Passo 3: Drilla

Use o padrão `socratic-drill`:

1. Pergunte a Pergunta 1. Espere a resposta.
2. Se certa + bem fundamentada: reconheça, vá para a 2.
3. Se certa mas frouxa: não deixe passar. "Você chegou lá, mas explique — por que o fundamento do tribunal sustenta isso?"
4. Se errada: não dê a resposta. Estreite. "Em quais fatos o tribunal se apoia?" Conduza até lá.
5. Se travou: estreite mais. "Antes da tese — qual o histórico processual?"
6. Se realmente perdido(a): mande reler o acórdão. "Isso é releitura, não chute. Volta quando tiver lido de novo."

### Passo 4: Resumo pós-drill

No final:

```markdown
# Prep de Arguição — [julgado] — [data]

**Perguntas drilladas:** [N]
**Forte:** [perguntas em que estava confiante + certo(a)]
**Frágil:** [perguntas em que chutou ou esquivou]
**Perdeu:** [perguntas em que não sabia]

## Antes da aula amanhã:
- [coisa específica para reconferir — fatos errados, norma que não conseguiu enunciar]
- [se frágil em princípios: "releia o voto vencido — geralmente é de lá que vêm as perguntas de princípio"]

## Perguntas prováveis na aula:
- [top 3 das 10 — as que o(a) professor(a) provavelmente abre]
```

## Integração

- **case-brief:** se o(a) estudante ainda não fez resumo do julgado, ofereça rodar `/estudante-direito:case-brief` antes da prep. Resumo é ferramenta de prep também.
- **socratic-drill:** se a prep expõe ponto fraco na disciplina (não só no julgado), siga com `/estudante-direito:socratic-drill [disciplina]`.
- **flashcards:** se a norma do julgado é uma que o(a) estudante deve memorizar, ofereça adicionar ao deck.

## O que esta skill não faz

- **Ser o(a) professor(a).** A arguição real pode ir a qualquer lugar. Esta skill prevê padrões; professores surpreendem.
- **Substituir ler o acórdão.** Se você não leu, a skill não ajuda — perguntas exigem texto que você absorveu.
- **Dar a tese do julgado sem você responder primeiro.** Padrão drill-me: eu pergunto, você responde.
- **Prever perguntas de nicho específicas da jurisdição/seccional.** Se o(a) professor(a) tem temas favoritos conhecidos, capture em ~/.claude/plugins/config/claude-for-legal/estudante-direito/CLAUDE.md e a skill pondera; senão, trabalha de padrões gerais.
