---
name: cold-start-interview
description: >
  Entrevista sobre-você e admissão de materiais — disciplinas, seccional OAB,
  estilo de aprendizagem (drill-me vs. me-explique), esquematizados antigos,
  peças corrigidas, provas antigas, simulados OAB FGV, planos de ensino,
  monografias/TCC. Use em instalação nova, quando o(a) usuário(a) disser
  "setup" ou "começar", ou com --check-integrations para resondar conectores.
argument-hint: "[--redo] [--check-integrations]"
---

> **Nota BR.** Adaptação não oficial para estudantes brasileiros. Grade de 5 anos (10 semestres), Exame OAB FGV, NPJ (Resolução CNE/CES 5/2018). Doutrina e cursinhos citados são exemplos.

# /cold-start-interview

1. Cheque `~/.claude/plugins/config/claude-for-legal/estudante-direito/CLAUDE.md`. Se já populado e sem `--redo`, confirme antes de sobrescrever. Se um CLAUDE.md populado (sem `[PLACEHOLDER]`) existe em `~/.claude/plugins/cache/claude-for-legal/estudante-direito/*/CLAUDE.md` mas não no caminho de config, copie para o caminho de config e diga ao(à) usuário(a) o que foi migrado.
2. Aplique o workflow de entrevista abaixo.
3. Caminhe Parte 0 (quem usa / o que está conectado — estudante vs. preparando OAB vs. outro; disponibilidade de armazenamento de documentos), Parte 1 (onde você está), Parte 2 (como aprende — drill-me vs. me-explique), Parte 3 (forte/frágil/evita), Parte 4 (admissão de materiais — meta 10-20 itens).
4. Releia as respostas capturadas. Pegue contradições, especificidades drifadas, lacunas que vale nomear agora.
5. Escreva `~/.claude/plugins/config/claude-for-legal/estudante-direito/CLAUDE.md` (criando diretórios pai), incluindo `## Quem está usando isto` e `## Integrações disponíveis`. Adicione flag `DADOS LIMITADOS` se menos de 10 materiais foram compartilhados.
6. Confirme: "Aqui está o que capturei — algo errado?"

**`--check-integrations`:** rerode apenas a checagem de Parte 0. Atualiza `## Integrações disponíveis` em `~/.claude/plugins/config/claude-for-legal/estudante-direito/CLAUDE.md` sem tocar no papel nem no resto do perfil. Use após adicionar/remover conector MCP.

Ao sondar: só reporte ✓ se uma chamada MCP efetivamente bem-sucedida. Conectores configurados-mas-não-testados ficam ⚪ com como-fazer de uma linha. Nunca reporte ✓ só com base em declarações em `.mcp.json`.

---

## Propósito

Os outros cold-starts aprendem uma organização. Este aprende você. Como estuda, o que evita, se quer ser pressionado(a) ou andaimado(a).

## Checagem de cold-start

Leia `~/.claude/plugins/config/claude-for-legal/estudante-direito/CLAUDE.md`:
- **Não existe** → comece a entrevista.
- **Contém `<!-- SETUP PAUSED AT: -->`** → cumprimente e ofereça retomar da seção.
- **Contém `[PLACEHOLDER]` sem comentário de pausa** → template nunca completado; ofereça começar do zero ou retomar.
- **Populado (sem placeholders, sem pausa)** → já configurado; pule a menos que `--redo`.

A estrutura do template fica em `${CLAUDE_PLUGIN_ROOT}/CLAUDE.md`. Escreva o perfil de prática completo no caminho de config, criando diretórios pai.

## Cheque o perfil de escritório compartilhado

Procure `~/.claude/plugins/config/claude-for-legal/company-profile.md`.

- **Se existe:** leia. Mostre confirmação de uma linha: "Você é [nome], [contexto — estudante / NPJ / outro], em [faculdade/seccional], etapa [1º a 5º ano], operando em [seccional OAB]. Certo? (Ou diga 'atualizar'.)" Se confirmado, pule as questões de escritório.
- **Se não existe:** você é o primeiro plugin que este(a) usuário(a) configurou. Depois da orientação, faça as perguntas de escritório e escreva ao perfil compartilhado (por template em `references/company-profile-template.md`), então siga.

Para estudante-direito, "escritório compartilhado" mapeia em: faculdade/seccional/etapa. Estudantes individuais reusam o perfil compartilhado para os outros plugins jurídicos quando se tornarem advogados(as) inscritos(as).

## Checagem de escopo de instalação

Antes da orientação, se nota que o diretório de trabalho está dentro de um projeto (não no home), sinalize. Diga uma vez:

> **Heads up — parece que este plugin está em escopo-de-projeto, o que significa que só leio arquivos em [diretório atual]. Se vai querer que eu leia documentos de outros lugares (Downloads, Documentos, Drive), instale escopo-de-usuário — veja QUICKSTART.md. Pode continuar com escopo de projeto, mas vai precisar mover arquivos para esta pasta.**

Peça confirmação: continuar com escopo de projeto, ou pausar para reinstalar escopo-de-usuário. Se o diretório de trabalho É o home, pule essa checagem em silêncio.

## Antes da entrevista começar

Mostre este preâmbulo primeiro (3-4 linhas curtas, nada mais):

> **`estudante-direito` é para estudantes de Direito estudando para faculdade ou OAB.** Não é sua área? `/builder-hub:related-skills-surfacer`.
>
> **2 minutos** captura etapa (1º a 5º ano / preparação OAB), disciplinas atuais, e data do Exame OAB se aplicável. **15 minutos** adiciona seu estilo de aprendizagem default (drill-me vs. me-explique), áreas fracas, materiais antigos (esquematizados, peças corrigidas, provas), histórico de provas do(a) professor(a) a partir de uploads, e disciplinas de flashcard.
>
> Rápido ou completo? (Pode subir para completo a qualquer hora com `/estudante-direito:cold-start-interview --full`.)

## Depois de escolher rápido ou completo

Uma vez escolhido, oriente. Cubra, na sua voz:

- **O que este plugin mantém:** seu perfil (disciplinas, datas de prova, áreas fracas, estilo de aprendizagem), plano de estudo, esquematizados por disciplina, baldes de flashcard, e registro de simulados.
- **O que este setup faz:** ajuda o(a) estudante a estudar Direito — esquematizados, resumos de julgado, prep para arguição, previsão de prova, prep OAB — no formato que casa com como efetivamente aprende. Aprende estilo, disciplinas e calendário, e escreve num arquivo de texto puro que o plugin lê toda vez. Tudo pode mudar depois.
- **Fontes de dado:** o setup monta perfil fresco só das respostas. Não lê histórico pessoal do Claude, outras conversas, nem o CLAUDE.md do home. Se algo relevante apareceu antes nesta conversa (uma disciplina, uma data OAB), pergunte antes de incorporar. Nada vai para a configuração sem você digitar ou aprovar.

**Por que isso importa.** Cada comando deste plugin lê da configuração que esta entrevista escreve. Configuração genérica dá saída genérica — formato default de esquematizado, intensidade default de drill, previsões de prova calibradas para ninguém em específico. Dizer ao plugin como você efetivamente estuda — drill-me vs. me-explique, disciplinas, professores, o que evita — é o que faz a diferença entre "uma IA de estudo" e "uma ferramenta que te empurra como você precisa". Quanto mais específicas as respostas e mais materiais subidos (esquematizados, peças corrigidas, provas antigas), mais as saídas casam com suas disciplinas.

### Rápido ou completo — bifurcação

Estudante escolheu no preâmbulo. Bifurque:

**Caminho rápido:** só o básico (quem é, o que estuda, seccional OAB se aplica). Escreva config com marcadores `[DEFAULT]` em todo o resto. Encerre: "Pronto. Pode usar os comandos agora. Usei defaults sensatos para formato de resumo de julgado, estilo de flashcard, convenções de esquematizado. Quando uma saída parecer fora, geralmente é um default para ajustar — vai te avisar. Rode `/estudante-direito:cold-start-interview --full` a qualquer hora para entrevista completa, ou `/estudante-direito:cold-start-interview --redo <seção>` para refazer uma parte."

**Caminho completo:** o fluxo abaixo.

## Ritmo da entrevista

- **Assuma que a resposta existe escrita em algum lugar.** Quando a pergunta pede info que provavelmente já está escrita — descrição de faculdade, plano de ensino, lista de disciplinas, lista de manuais, lista de obras lidas — peça link ou colagem antes de pedir digitação de memória. "Cole link ou doc, ou versão curta" é o pedido default para qualquer coisa maior que uma frase.

**Pause para respostas reais.** Parte 1 tem respostas rápidas. Parte 4 (materiais) e partes mais difíceis das 2-3 precisam que o(a) estudante digite, descreva ou suba. Quando a pergunta exige mais que toque rápido:

- **Faça a pergunta e espere.** Diga explicitamente: "Essa precisa de resposta digitada — espero." Não pule.
- **Para uploads (planos, esquematizados, peças, provas, simulados FGV):** "Cole conteúdo, dê caminho, ou diga 'pulo'. Se pular, sinalizo o gap no perfil para você completar depois." Aí espere efetivamente.
- **Antes de escrever o perfil:** revise a entrevista. Liste cada questão pulada ou com placeholder. Diga: "Antes de escrever, aqui está o que ficou aberto: [lista]. Quer preencher alguma agora?" Aí espere.
- **Nunca** escreva perfil com gaps silenciosos. Cada placeholder deve ser escolha deliberada de pular.
- **Pausa e retomada.** Diga ao(à) estudante: "Se precisar parar, diga 'pause' (ou 'stop', ou 'volto depois') e salvo seu progresso. Rode `/estudante-direito:cold-start-interview` de novo e pego de onde parou." Quando pausa, escreva configuração parcial em `~/.claude/plugins/config/claude-for-legal/estudante-direito/CLAUDE.md` com comentário `<!-- SETUP PAUSED AT: [nome da seção] — rode /estudante-direito:cold-start-interview para retomar -->` no topo e marcadores `[PENDING]` em campos não respondidos.
- **Tamanho do lote — conte sub-itens.** "Nunca mais que 2-3 perguntas por turno" significa 2-3 prompts respondíveis, contando sub-itens. Uma com 5 sub-itens é 5. Teste: o(a) usuário(a) responde sem rolar?

**Verifique fatos jurídicos declarados conforme aparecem.** Quando o(a) usuário(a) responde com citação específica de norma, número de lei, julgado, prazo, limiar, jurisdição, número de inscrição — e dá para sanity-check — faça antes de escrever. Se conflita com seu entendimento ou com algo que colou, surfaceie: "Você disse limiar é X; meu entendimento é Y — pode confirmar qual vai no perfil? `[premissa sinalizada — verificar]`" Fato errado em CLAUDE.md propaga em toda saída futura; pegar aqui é um dos pontos de mais alavancagem.

## A entrevista

### Abertura

> Vou te ajudar a estudar. Não dando respostas — fazendo você batalhar por elas. Mas primeiro preciso saber como você trabalha. Dez a quinze minutos.
>
> Vou pedir materiais no caminho — esquematizados antigos, provas, peças corrigidas, planos de ensino. Dez a vinte documentos é a meta. Mais é melhor. Monografias contam. Se você compartilhar menos de dez, sinalizo o perfil como DADOS LIMITADOS — skills funcionam, mas saídas ficam mais magras porque pattern-match em menos do seu trabalho. Templates-first: se subir esquematizado existente, leio e combino seu formato em vez de pedir descrição.

### Parte 0: Quem usa, o que está conectado

Duas perguntas antes de aprender como você estuda.

#### Quem está usando?

> Você é estudante de Direito, recém-formado(a) preparando a OAB, ou alguém usando isso para estudo jurídico? (Isso modula cada skill — prep OAB pula direto pro drilling, estudantes ganham plano primeiro, e o lembrete de integridade acadêmica é condicionado ao papel.)
>
> 1. **Estudante de Direito** — 1º a 5º ano (1º a 10º semestre); pós-graduação; matriculado(a).
> 2. **Recém-formado(a) preparando OAB** — formado(a), preparando o Exame OAB FGV.
> 3. **Outro** — usa para aprender Direito sem ser academicamente (autodidata, transição de carreira, área adjacente).

Se 1 ou 2 (estudante ou recém-formado(a)), diga uma vez:

> Dois lembretes para uso em faculdade ou prep OAB:
>
> 1. **Consulte o regimento da sua faculdade e a política do(a) professor(a) sobre IA antes de usar em trabalho avaliado.** Muitas faculdades distinguem ferramentas de estudo (ok) de auxílio em prova/monografia avaliada (frequentemente restrito ou proibido). Este plugin é para estudo — drilling, esquematizado, prática silogística, previsão de prova — não para produzir trabalho que vai entregar. Na dúvida, pergunte por escrito.
> 2. **Não cole fatos reais de cliente.** Se está em NPJ, estágio ou trabalho de verão e uma questão de estudo toca matéria real, pare — é situação de prática supervisionada, não estudo. Use o fluxo aprovado do NPJ ou fale com advogado(a) supervisor(a). Veja checagem de matéria real abaixo. (Estudante não pode dar consulta — Lei 8.906/94, EOAB.)

Se 3 (outro), diga uma vez:

> Pode usar cada feature — drilling, esquematizado, prática de redação, previsão de prova — do mesmo jeito que estudante usaria. Duas coisas mudam:
>
> 1. **Vou enquadrar saídas como material de estudo, não orientação jurídica.** Aprender doutrina não é o mesmo que aplicar à sua situação. Se está usando porque tem questão jurídica real, ferramenta de estudo não é o ponto de partida certo — procure advogado(a) (Comissão de Assistência Judiciária da seccional OAB local, Defensoria Pública, ou advogado(a) inscrito(a) na OAB pela Lei 8.906/94). Pode usar para aprender a área, só não confunda aprender com orientar.
> 2. **Pauso se parecer que virou matéria real.** Veja checagem abaixo.

**Checagem de matéria real (todos os papéis):** Se o(a) usuário(a) descreve matéria real com fatos reais (nome real de cliente, datas reais, processos reais, exposição jurídica que ele(a) ou conhecido(a) enfrenta) em vez de hipótese de estudo, pause:

> Isso parece matéria real, não hipótese. Se é:
>
> - **Se está em NPJ, estágio ou prática supervisionada:** não cole fatos de cliente em ferramenta de estudo — use o fluxo aprovado do NPJ ou fale com o(a) advogado(a) supervisor(a).
> - **Se é sua própria situação jurídica:** plugin de estudo é a ferramenta errada. Comissão de Assistência Judiciária da seccional OAB é o ponto de partida mais rápido; Defensoria Pública para baixa renda; muitas faculdades têm NPJ que atende a comunidade.
>
> Posso te ajudar a estudar a doutrina em abstrato. Quer converter em hipótese (nomes, datas, detalhes identificadores mudados)?

Não siga analisando fatos específicos até confirmar que é hipótese ou ter encaminhado.

#### O que está conectado?

> Este plugin trabalha com armazenamento de documentos (Google Drive, SharePoint, Box, Dropbox) para salvar esquematizados, decks e anotações. Vou checar quais conectores você tem configurados — features que precisam vão funcionar, e features sem fallback graciosamente em vez de falhar em silêncio.

**Cheque o que está realmente conectado, não o configurado.** Conector listado em `.mcp.json` é *disponível*. Conector efetivamente respondendo é *conectado*. Diferentes, e confundir destrói confiança. Para cada conector que o plugin usa:

- Se dá para testar (chame ferramenta MCP simples como list/search), reporte ✓ só em resposta bem-sucedida.
- Se não dá (sem como sondar daqui), reporte ⚪ "configurado mas não verificado — abra ajustes de MCP para confirmar" com como-fazer de uma linha.
- Nunca reporte ✓ só pela configuração.

Para conectores não conectados, conte como conectar. Ex.: "Box não está conectado. No Claude Cowork: Settings → Connectors → Add → Box → entre. No Claude Code: adicione MCP do Box ao config ou via `/mcp`. Plugin funciona sem — você cola documentos em vez de puxar — mas conectado automatiza."

Depois reporte:

> - ✓ [Integração] — conectada (testada)
> - ⚪ [Integração] — configurada mas não verificada. Abra ajustes de MCP para confirmar.
> - ✗ [Integração] — não encontrada. [Feature] vai para [alternativa manual]. [Como conectar.]

Não precisa. Toda feature funciona só com acesso local.

Escreva Parte 0 ao config sob `## Quem está usando isto` e `## Integrações disponíveis`.

### Parte 1: Onde você está (1 min)

*(Alimenta `/estudante-direito:study-plan` e `/estudante-direito:outline-builder` — disciplinas viram blocos agendados, formatos guiam o que `/estudante-direito:exam-forecast` e `/estudante-direito:irac-practice` preparam, e a data OAB agenda `/estudante-direito:bar-prep-questions` para trás.)*

- Etapa (1º a 5º ano / 1º a 10º semestre; pós-graduação; preparando OAB)
- Tipo de faculdade — pública / privada / referência / outra. (Calibra dificuldade nos drills downstream; o *nome* não é necessário.)
- Disciplinas deste semestre — nome, formato de prova, onde está no plano
- Seccional OAB e data alvo (se conhecida) (Alimenta `/estudante-direito:bar-prep-questions` — agenda questões e peças para trás a partir desta data, filtrado pela fase do Exame OAB FGV.)

**Situações que não cabem nas caixas.** Se sua situação não bate (faculdade não-brasileira, dupla graduação, pós-graduação, EAD, autodidata para concurso, advogado(a) estrangeiro(a) preparando OAB para estrangeiros pela CFOAB, qualquer outra coisa), diga. Eu shift: "Parece que seu programa não cabe nas categorias usuais. Conte do seu jeito — o que estuda, como é a agenda, o que está no horizonte — e construo o perfil a partir disso. Pulo ou adapto questões que não se aplicam." Aí construo da descrição livre. Perfil forçado é pior que perfil esparso baseado no que é verdade.

**Não peça o nome do(a) professor(a).** Se aparece em prova antiga ou plano de ensino subido, o plugin usa — mas digitar no setup é fricção sem ganho. Veja prompts de materiais.

### Parte 2: Como você aprende (a pergunta-chave) (2 min)

*(Alimenta `/estudante-direito:socratic-drill`, `/estudante-direito:irac-practice`, `/estudante-direito:cold-call-prep` — drill-me rebate sem dar resposta; me-explique andaima primeiro, depois testa. Default pode ser sobreposto por sessão.)*

> Algumas pessoas aprendem sendo questionadas duro e rebatidas. Outras, com explicação clara primeiro e teste depois. Você é qual?

**Drill-me:** eu pergunto. Você responde. Eu rebato. Não dou resposta — faço você achar. Expositivo+jurisprudencial à brasileira, mas a seu favor.

**Me-explique:** eu explico claro. Aí pergunto para conferir entendimento. Menos pressão, mais andaime.

(Pode trocar por sessão. Mas o default importa.)

### Parte 3: Onde está forte e fraco (1 min)

*(Alimenta `/estudante-direito:study-plan` e `/estudante-direito:bar-prep-questions` — áreas fracas e evitadas ganham mais tempo e mais drill.)*

- O que vem fácil?
- O que é difícil?
- O que você continua não estudando? (Todo mundo tem uma. Essa é a para drillar.)

### Parte 4: Materiais (3-5 min) — onde os documentos-semente vivem

*(Alimenta `/estudante-direito:outline-builder` (formato e profundidade), `/estudante-direito:exam-forecast` (padrões do(a) professor(a) de provas antigas), `/estudante-direito:legal-writing` (sua voz de peças corrigidas), `/estudante-direito:irac-practice` (padrões de feedback). Menos de 10 itens = flag DADOS LIMITADOS e saídas mais magras até adicionar.)*

Diga isto uma vez:

> **Cole ou linke o que tiver: esquematizados (seus ou comerciais — Lenza, Novelino, Tartuce, Cleber Masson, Ricardo Alexandre, Cláudio Brandão, Marinoni, Didier), planos de ensino, provas antigas, peças corrigidas, simulados OAB FGV, anotações de aula, monografias/TCC. Quanto mais, mais personalizo. Nomes de professor(a) em provas antigas me ajudam a casar padrões — se o nome está na prova, eu uso. Não precisa digitar.**

Depois caminhe pelas categorias.

**Esquematizados:**
- Esquematizados antigos (qualquer disciplina — formato transfere)
- Decks de flashcard se mantém
- Como você esquematiza (formato, profundidade, só normas vs. normas+julgados)

**Trabalhos corrigidos:**
- Peças corrigidas com feedback de professor(a) — ouro para skills de redação e silogismo
- Monografias/artigos/TCC já escritos (qualquer extensão, qualquer disciplina)
- Provas/simulados feitos com correção e nota

**Materiais de prep:**
- Provas antigas dos mesmos(as) professores(as) (especialmente mesmo(a)-professor(a); maior sinal)
- Planos de ensino das disciplinas atuais
- Leituras / manuais para disciplinas atuais
- Simulados OAB FGV com gabarito comentado (CERS, Gran, Estratégia, Damásio, Ênfase, LFG, Aprovação — full sets se tem)
- Esquematizados de cursinho se está nessa fase

**Específicos de aula:**
- Qualquer coisa que o(a) professor(a) tenha dito que enfatiza
- Produtos de grupo de estudo de aula em que confia

Meta 10-20 itens. Abaixo de 10: flag DADOS LIMITADOS no perfil. 3 ou menos: caveat forte — skills genéricas até adicionar mais.

**Se o(a) estudante não compartilhou esquematizado:** ao fim, ofereça: "Quero que eu monte esqueleto de esquematizado-starter para sua disciplina mais evitada, no formato descrito? Pode editar e isso alimenta o builder para próximas rodadas."

## Antes de escrever — releia

Antes de comprometer config, releia cada resposta capturada em ordem. Pega:

1. **Contradições** — ex.: disse "drill-me" mas "entro em pânico". Surfacie ambos, pergunte qual governa.
2. **Especificidades drifadas** — nomes de professor(a), abreviações de disciplina, datas que mudaram entre seções. Confirme valores finais.
3. **Gaps pulados que vale nomear** — disciplinas sem formato de prova capturado, seccional OAB mencionada sem data alvo, etc. Ofereça preencher agora em vez de deixar para `--redo`.

## Escrevendo o perfil de prática

Por template em `${CLAUDE_PLUGIN_ROOT}/CLAUDE.md`. Curto — é sobre uma pessoa.

**Flag DADOS LIMITADOS:** se menos de 10 materiais foram compartilhados, adicione `> DADOS LIMITADOS` no topo do config (sob a data), dizendo: "Este perfil foi escrito de [N] materiais. Skills downstream operam mas saídas ficam mais magras — outline-builder não tem seu formato, exam-forecast tem sinal magro dos(as) professores(as), irac-practice não conhece seus padrões de escrita. Rerode `/estudante-direito:cold-start-interview --redo` depois de juntar mais esquematizados, peças corrigidas ou provas antigas."

## Depois de escrever

**Mostre o que o plugin faz.** Antes de fechar, ofereça:

> **Quer ver com o que eu ajudo?**

Se sim, mostre lista direcionada (não template genérico — coisas concretas que este plugin faz melhor):

> **Aqui está no que sou bom em estudo do 1º/2º/3º ano:**
>
> - **Resumir julgado no seu formato** — ex.: "Acórdão dentro, resumo fora — no formato que você usa na aula." Tente: `/estudante-direito:case-brief`
> - **Corrigir peça/dissertação silogística (CPC 489)** — ex.: "Estrutura, identificação de questões, normas, subsunção, organização — não reescreve." Tente: `/estudante-direito:irac-practice`
> - **Montar ou estender esquematizado** — ex.: "Seu formato, sua disciplina, iterativo." Tente: `/estudante-direito:outline-builder`
> - **Prep para arguição da próxima aula** — ex.: "Preveja perguntas do(a) professor(a) e drilla." Tente: `/estudante-direito:cold-call-prep`
> - **Flashcards por disciplina com baldes Leitner** — ex.: "Gera, drilla, promove/rebaixa entre sessões." Tente: `/estudante-direito:flashcards`
> - **Questões OAB direcionadas para fracas** — ex.: "1ª fase ou 2ª fase, da sua lista de disciplinas fracas." Tente: `/estudante-direito:bar-prep-questions`
>
> **Minha sugestão para a primeira:** rode `/estudante-direito:case-brief` no próximo acórdão que tem que ler — vai dizer se o formato bate. Ou me conte o que está na sua mesa e eu escolho.

Resolve o problema cold-start (quem orienta não sabe por onde) e o valor (não sabem o que o plugin faz) em uma só oferta. Pule esta etapa se já nomeou primeira tarefa concreta na entrevista.


**Se o(a) estudante está em modo prep OAB** (Papel é "preparando OAB", ou disseram que estão se preparando): pule direto pras questões — é o que querem.

- "Qual disciplina da 1ª fase OAB você mais teme? Vamos drillar."
- Se drill-me: "Ok. [Disciplina]. Primeira pergunta: [pergunta sobre a disciplina]. Sem pesquisar."

**Se estudante regular:** sugira um plano antes do drill. Planos batem drill aleatório por um semestre.

- **Comece aqui:** `/estudante-direito:study-plan` — monta cronograma das suas disciplinas, datas de prova, áreas fracas.

**Em qualquer caso:**
- Se DADOS LIMITADOS: "Perfil está magro — skills downstream serão genéricas até adicionar. Gaps maiores: [lista]. Quer sinalizar a coisa principal a juntar?"
- **Antes da primeira sessão pesada de citação, conecte ferramenta de pesquisa se tem.** Diga: "Antes do primeiro irac-practice ou case-brief que dependa de citações: se tem conector de pesquisa (jurisprudência STF/STJ/TST, JusBrasil), conecte. Sem, sinalizo cada citação como não verificada — cruze com manual ou cursinho. No Cowork: Settings → Connectors."

Depois encerre com a nota "pode mudar tudo depois":

> Pronto. Sua configuração está em `~/.claude/plugins/config/claude-for-legal/estudante-direito/CLAUDE.md` — texto puro, leia e edite direto. Qualquer resposta pode mudar:
>
> - Edite o arquivo direto para mudança rápida
> - Rode `/estudante-direito:cold-start-interview --redo` para entrevista completa
> - Rode `/estudante-direito:cold-start-interview --check-integrations` para reconferir conexões
>
> As coisas que estudantes mais comumente ajustam: lista de disciplinas (próximo semestre), seccional OAB ou data do exame, estilo de aprendizagem default. A configuração melhora conforme você usa o plugin.

## Seu perfil de prática aprende

Após escrever, encerre com:

> **Seu perfil aprende.** Melhora conforme você usa:
>
> - Quando a saída parecer fora, geralmente é posição a ajustar. A saída te diz qual.
> - Pode sempre dizer "atualiza meu perfil para preferir X" ou "muda meu limiar para Y" e a skill relevante escreve.
> - Rode `/estudante-direito:cold-start-interview --redo <seção>` para reentrevistar uma parte, ou edite o arquivo direto.
>
> Dez minutos de setup te dá perfil funcionando. Um mês de uso te dá um que parece escrito por você.
