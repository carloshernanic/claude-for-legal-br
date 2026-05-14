---
name: invention-intake
description: >
  Triagem inicial de divulgação de invenção — sinais de novidade, atividade
  inventiva, patenteabilidade (LPI arts. 10 e 18), perda de novidade por
  divulgação, detectabilidade e valor estratégico. Use quando entra uma
  comunicação de invenção e é preciso triar se vale comissionar busca de
  anterioridade e revisão por mandatário(a) / advogado(a) de patente,
  investigar mais ou recusar.
argument-hint: "[cole ou descreva a comunicação de invenção — ou só o título e eu pergunto]"
---

# /invention-intake

> **Nota de adaptação BR.** Esta skill foi originalmente concebida para o
> sistema norte-americano (35 U.S.C. §§ 101, 102, 103). No Brasil, a
> análise é regida pela **LPI (Lei 9.279/1996)**, com depósito perante o
> **INPI**. Equivalências e diferenças relevantes:
> - **Requisitos de patenteabilidade (LPI art. 8º):** novidade (art. 11),
>   atividade inventiva (art. 13), aplicação industrial (art. 15) e não
>   incidência em vedação (art. 10 — não-invenção / art. 18 — não-
>   patenteável).
> - **Não-invenção / não-patenteável.** Substituto funcional ao "§ 101"
>   americano:
>   - **LPI art. 10** — não são invenção nem MU: descobertas, teorias
>     científicas, métodos matemáticos, concepções puramente abstratas,
>     esquemas/planos/regras/métodos comerciais/contábeis/financeiros/
>     educativos/publicitários/de sorteio/de fiscalização; obras
>     literárias/arquitetônicas/artísticas/científicas; programas de
>     computador **em si**; apresentação de informações; regras de jogo;
>     técnicas/métodos operatórios/cirúrgicos/terapêuticos/diagnósticos
>     aplicados ao corpo humano ou animal; todo ou parte de seres vivos
>     naturais, materiais biológicos encontrados na natureza, e processos
>     biológicos naturais.
>   - **LPI art. 18** — não-patenteáveis: contrários à moral/bons
>     costumes/segurança/ordem/saúde públicas; resultado de transformação
>     do núcleo atômico; todo ou parte de seres vivos, exceto
>     microrganismos transgênicos que atendam aos requisitos do art. 8º.
> - **Software no Brasil é direito autoral (Lei 9.609/98), não patente.**
>   Programa "em si" não é invenção (LPI art. 10 V). Patente sobre
>   invenção implementada por computador é admitida quando há **efeito
>   técnico** além do programa em si (vide Diretrizes de Exame INPI).
> - **Sem período de graça absoluto como nos EUA.** O Brasil tem **período
>   de graça de 12 meses** (LPI art. 12) contado da divulgação pelo
>   inventor, sucessor ou terceiro com base em informações daquele.
>   Aplica-se a divulgação em **qualquer país**. Mas atenção: muitos países
>   (EPO em regra, China, etc.) **não reconhecem** o período de graça —
>   divulgação pública pode matar direitos no exterior independentemente
>   de o BR ainda estar aberto.
> - **Vigência:** PI 20 anos do depósito (LPI 40). Parágrafo único
>   (mínimo 10 anos da concessão) foi **declarado inconstitucional pelo
>   STF (ADI 5529, 2021)**. MU: 15 anos do depósito.
> - **Inventor empregado (LPI arts. 88–93 c/c CLT):** quando a invenção
>   decorre da natureza dos serviços contratados ou da utilização de
>   recursos da empresa, a titularidade é em regra do empregador. Invento
>   livre é do empregado. Invento misto pode ser cotitulado e exige
>   acordo. Diferente dos EUA — não há regime puro "by default empresa";
>   a CLT modula.

**Esta é triagem inicial por não-especialista, não parecer de
patenteabilidade.** A triagem nunca conclui que invenção é patenteável —
conclui que **passa na triagem inicial e merece busca de anterioridade e
revisão por advogado(a)/mandatário(a) registrado(a) no INPI**, que precisa
de mais informação, ou que bate em desqualificador. Busca de anterioridade
é passo separado; esta skill não a faz.

## Instruções

1. Leia `~/.claude/plugins/config/claude-for-legal/propriedade-intelectual/CLAUDE.md`. Se
   contiver `[PLACEHOLDER]`, pare e direcione para
   `/propriedade-intelectual:cold-start-interview`. Se o perfil mostra prática só de
   marca/direito autoral (sem patente), diga isso e roteie para outra
   ferramenta — esta é a errada.
2. Siga o fluxo abaixo.
3. Faça intake. Se o usuário colou ou subiu a comunicação, leia. Se não,
   pergunte as sete questões de intake (o quê / problema / diferenças /
   inventores / divulgação pública / status / área tecnológica) em lote
   único e aguarde.
4. Rode os seis filtros: sinais de novidade, flags de atividade inventiva,
   matéria patenteável (LPI 10/18), divulgação pública / período de graça,
   detectabilidade, valor estratégico. Cada filtro recebe veredito
   ✓ / 🟡 / 🔴 com raciocínio em uma linha.
5. Escreva o memorando na pasta da matéria (se houver matéria ativa) ou na
   pasta de outputs. Aplique o cabeçalho de work-product conforme o papel.
6. Bottom line: **PROSSEGUIR** (comissionar busca de anterioridade e
   revisão) / **INVESTIGAR** (precisa de mais info em item específico) /
   **RECUSAR** (declare o motivo concreto). Nunca diga "patenteável".
7. Encerre com árvore de decisão (busca de anterioridade / retorno ao
   inventor / revisão por especialista / recusa com agradecimento / rota
   de segredo de empresa) e gate de não-advogado(a) se for o caso.
8. Se a triagem bateu em divulgação pública nos últimos 12 meses (Brasil)
   ou qualquer divulgação pública com direitos estrangeiros no escopo
   (regra geral: novidade absoluta), sinalize no topo: **urgente**.

Esta skill nunca conclui que uma invenção é patenteável. Em qualquer
dúvida, sinalize — quem decide é o(a) advogado(a)/mandatário(a) de
patente.

## Exemplos

```
/propriedade-intelectual:invention-intake "novo algoritmo de eviction de cache que usa modelo aprendido em vez de LRU; concebido no Q1 deste ano, ainda não divulgado, protótipo em staging interno"
```

```
/propriedade-intelectual:invention-intake
```

(E a skill pergunta a invenção, o problema, como difere, inventores,
status de divulgação, status de uso e área tecnológica.)

---

## ISTO É TRIAGEM INICIAL, NÃO PARECER DE PATENTEABILIDADE

**Diga isto no topo de toda saída. Não descarte, não suavize.**

> **Esta é triagem inicial por não-especialista, não parecer de
> patenteabilidade.** Parecer de patenteabilidade exige busca de
> anterioridade, interpretação plena das reivindicações e juízo de
> advogado(a)/mandatário(a) registrado(a) no INPI. Esta triagem não
> roda busca de anterioridade, não avalia o que existe no estado da
> técnica, e não constrói reivindicações. Triagem para os
> desqualificadores óbvios (a invenção já está no mercado, foi divulgada
> publicamente há dois anos sem proteção do período de graça, é claramente
> abstrata sob LPI art. 10) e para os "go-aheads" óbvios (novo mecanismo,
> avanço técnico, concepção recente, em uso secreto). Tudo no meio precisa
> de busca de anterioridade e revisão por advogado(a)/mandatário(a).
> Esta triagem nunca conclui que algo é "patenteável" — conclui que
> "passa na triagem inicial, merece investigação" ou que não passa.

Sub-sinalizar invenção que deveria ter sido depositada é porta de uma via —
o prazo do período de graça (LPI 12) corre; direitos estrangeiros são
perdidos na primeira divulgação pública absoluta; o concorrente deposita
antes (regra do **first-to-file** no Brasil — LPI art. 7º). Sobre-sinalizar
só significa busca de anterioridade que volta vazia. Fique do lado da
porta de duas vias.

---

## Contexto de matéria

**Contexto de matéria.** Cheque `## Workspaces de matéria` no CLAUDE.md de
prática. Se `Habilitado` for `✗` (default para in-house), pule o resto
deste parágrafo. Se habilitado e não houver matéria ativa, pergunte:
"Qual matéria? Rode `/propriedade-intelectual:matter-workspace switch <slug>` ou diga
`nível de prática`." Carregue o `matter.md`. Escreva saídas na pasta da
matéria. Nunca leia arquivos de outra matéria, salvo se contexto
cross-matéria estiver `on`.

Comunicações de invenção são candidatas particularmente comuns a
**clean-team** ou confidencialidade reforçada. Respeite a marcação do
`matter.md`. Conteúdo de invenção é intrinsecamente sensível — não
resuma, cite ou referencie fora de canais sob sigilo.

---

## Carregue o perfil de prática primeiro

**Antes de ler a comunicação, leia
`~/.claude/plugins/config/claude-for-legal/propriedade-intelectual/CLAUDE.md`.** Se
faltar ou ainda contiver placeholders, pare e rode
`/propriedade-intelectual:cold-start-interview`. O perfil diz:

- A **estratégia de depósito** da empresa — ofensiva (construir portfólio
  de assertividade), defensiva (depositar para proteger FTO), híbrida,
  ou licenciamento. Determina o piso de valor estratégico.
- As **áreas tecnológicas de interesse** — onde a empresa deposita e
  onde não. Invenção fora das áreas é em regra recusa, mesmo se a
  triagem técnica é limpa.
- A **postura orçamentária** — agressiva, seletiva, mínima. Molda a
  recomendação.
- A **cadeia de aprovação** — quem assina a decisão de depósito, e para
  quem a invenção é roteada se passar.

Se o perfil mostra só marca/direito autoral (sem patente), esta skill é
a errada — diga e roteie.

---

## Fluxo

### Passo 1: Intake da comunicação

Se o usuário colar ou subir a comunicação, leia. Se não, pergunte — em
lote único, não uma por vez:

> Para triar isso, preciso:
>
> 1. **O que é a invenção?** Em linguagem simples — o que faz, o que a
>    faz funcionar, qual é a ideia-chave.
> 2. **Que problema resolve?** O que estava quebrado ou faltando antes.
> 3. **Como difere do que existia antes?** O que as pessoas faziam? O
>    que isto faz de diferente?
> 4. **Quem inventou, e quando?** Nomes e data aproximada de concepção.
>    (Sob LPI 88–93 + CLT: a invenção decorreu da natureza dos serviços
>    contratados ou de recursos da empresa?)
> 5. **Foi divulgada publicamente?** Publicada, vendida, oferecida à
>    venda, demonstrada em conferência, mostrada a cliente sob NDA,
>    postada em repositório público, escrita em paper, incluída em nota
>    de release de produto. Se sim, quando e onde.
> 6. **Está em uso ou planejada?** Em produção? Piloto limitado? Em
>    roadmap? Ainda no papel?
> 7. **Qual a área tecnológica?** (Software, hardware, mecânico, biotec,
>    método de fazer negócio, IA/ML etc.)

Aguarde as respostas. Não prossiga com meia-comunicação — triagem de "um
novo treco de ML que ajuda usuários" é pior do que nenhuma.

Se a comunicação é formulário formal (IDF) de IPMS ou template, extraia
os campos do formulário e pergunte só o que falta.

### Passo 2: Triagem contra o checklist

Percorra os filtros em ordem. Cada um produz veredito:
`✓ limpo`, `🟡 sinalizado — precisa de olhar adicional`, ou `🔴 red flag`.
Explique o raciocínio brevemente; não encha linguiça.

#### Filtro 1: Sinais de novidade

A comunicação descreve algo novo? Não é análise plena de novidade — isso
exige busca de anterioridade. Esta triagem mira a própria descrição em
busca de problemas auto-evidentes.

**Red flags (🔴):**
- "Aplicamos [técnica conhecida] a [novo domínio]" — ex.: "pegamos
  gradient boosting e aplicamos a previsão de churn"
- "É como [produto existente] mas para [X]" — moldura "Uber-para-X"
- "Concorrentes fazem algo parecido" — se a própria comunicação diz isso,
  a novidade está em xeque
- A comunicação descreve característica de produto público existente com
  ajuste menor

**Green flags (✓):**
- Um novo **mecanismo** — nova forma de fazer a coisa, não nova aplicação
- Nova **combinação** que produz resultado inesperado (não apenas
  aditivo — "mais rápido", "menor", "mais barato" às vezes são
  inesperados, às vezes óbvios)
- Resolver problema que o campo **não tinha resolvido** — a comunicação
  explica por que abordagens anteriores falharam e como esta não falha

**Sinalizado (🟡):** qualquer coisa ambígua. Busca de anterioridade decide.

#### Filtro 2: Flags de atividade inventiva (LPI 13)

Um técnico no assunto teria chegado a essa combinação a partir do que
é conhecido? Esta é triagem, não análise plena de atividade inventiva —
sinalize para investigação, nunca conclua óbvio ou não-óbvio.

**Red flags (🔴) para investigação:**
- Combinar **elementos conhecidos de modo previsível** — pôr sensor
  conhecido em máquina conhecida para medir coisa conhecida
- **Otimização rotineira** — "ajustamos o parâmetro de X para Y e teve
  resultado melhor"
- **Escolha de design sem vantagem funcional** — estéticas, ergonômicas,
  estilísticas, que não mudam o funcionamento
- **Óbvio tentar** — uma de poucas soluções identificadas com expectativa
  razoável de sucesso

**Green flags (✓):**
- "Teaching away" — estado da técnica esperava resultado oposto ou dizia
  que essa abordagem não funcionaria
- Resultado inesperado — a combinação produz algo que o técnico não
  previria
- Necessidade não atendida há tempo — problema conhecido, tentativas
  falharam

#### Filtro 3: Matéria patenteável (LPI arts. 10 e 18)

É concepção abstrata, descoberta, ou matéria expressamente excluída?
Filtro mais difícil e o mais provável de exigir leitura de especialista.
Sinalize qualquer coisa limítrofe para revisão por mandatário(a).

**Red flags (🔴) — LPI art. 10:**
- **Método de negócio puro** sem implementação técnica — "método de
  precificar mais eficientemente"
- **Algoritmo matemático** isolado — mesmo vestido de pseudocódigo
- **Organização de atividade humana** — agendamento, pareamento, matching,
  avaliação — sem melhoria técnica
- Reivindicação que se lê como "**fazer [coisa conhecida] em
  computador**" sem melhoria ao próprio computador
- Invenção IA/ML em que a reivindicação é a **função** (recomendar,
  classificar, prever) sem o meio técnico específico que melhora como o
  computador desempenha a função
- **Programa de computador "em si"** (LPI art. 10 V) — software pertence
  ao regime autoral (Lei 9.609)

**Green flags (✓) para software/IA:**
- Melhoria técnica ao **próprio computador** — nova arquitetura, nova
  técnica de treinamento, nova interface hardware/software, novo
  mecanismo de segurança
- Meios técnicos específicos, não apenas resultados
- Melhoria a um **campo técnico** (processamento de imagem, compressão,
  criptografia, robótica) com os meios técnicos descritos

**Qualquer limítrofe recebe 🟡 com "LPI 10 — rotear para especialista
para análise sob Diretrizes INPI de Invenções Implementadas por
Computador."** Não-especialista não decide chamada limítrofe sob LPI 10.

Para invenções em **biotec / diagnóstico**, sinalize também sob LPI
arts. 10 e 18 se a reivindicação envolver:
- Correlação natural ("se nível de X acima de Y, paciente tem Z")
- Substância natural (gene isolado, produto natural) sem modificação
  humana significativa
- Microrganismo: patenteável apenas se **transgênico** e cumprindo o
  art. 8º (LPI 18 III + par. único)

> **Matéria patenteável é padrão jurisdicional. Outros institutos
> diferem.** O EPO aplica "efeito técnico" (Art. 52 EPC), em regra mais
> permissivo para software e IA que LPI 10 V. USPTO aplica Alice/Mayo
> (§ 101). JPO e CNIPA, padrões distintos. Invenção que tritura sob LPI
> 10 V pode ser elegível em EPO/JPO/CNIPA.
>
> Quando o perfil inclui jurisdições não-BR: "Este filtro de LPI 10 é
> apenas Brasil. Se você depositar internacionalmente, a postura de
> elegibilidade pode ser diferente — especialmente para software, IA/ML
> e métodos de negócio, sobre os quais EPO é mais permissivo. Não
> recuse com base só em LPI 10 se há plano EP/JP/CN."

#### Filtro 4: Divulgação pública / período de graça

A invenção foi divulgada, vendida, oferecida à venda ou usada
publicamente? Este é o filtro mais sensível ao tempo — a resposta pode
matar patenteabilidade absolutamente, ou disparar relógio inestancável.

Categorize o status da divulgação:

**🔴 Provavelmente impedido:**
- Divulgada publicamente, vendida ou ofertada **há mais de 12 meses** —
  **LPI art. 12** período de graça expirado (Brasil).
- **Qualquer** divulgação pública em qualquer lugar antes do depósito —
  novidade absoluta vigora em EPO (em regra; salvo exceções restritas),
  China, Japão e maioria dos países fora do regime de período de graça.
  Se o negócio se importa com direitos estrangeiros, pode ser fatal
  mesmo com Brasil ainda aberto.

**🟡 Relógio correndo:**
- Divulgada publicamente nos últimos 12 meses — período de graça BR
  (LPI 12) correndo, direitos estrangeiros podem já estar perdidos.
  Urgente. Confirme a data e roteie para depósito imediatamente.
  **Atenção PCT:** depósito PCT dentro do período de graça pode
  preservar direitos em algumas jurisdições que aceitam a graça, mas
  não em todas — confirme com mandatário(a)/correspondente estrangeiro.

**✓ Limpo:**
- Sem divulgação pública. Demonstrações confidenciais a clientes sob
  NDA, uso interno, betas liberados a partes nomeadas sob NDA, papers
  ainda não submetidos — em regra não são "públicos" para LPI 11/12,
  mas depende dos fatos. Quando a divulgação foi a cliente ou parte
  externa, mesmo sob NDA, sinalize os detalhes para a equipe de
  prossecução avaliar.

**Pergunte especificamente sobre:**
- Papers submetidos a periódicos ou conferências (submissão ≠
  publicação; cheque política do periódico e se houve preprint público)
- Palestras dadas em conferências, meetups, eventos internos abertos a
  não-empregados
- Postagens em repositórios públicos, blogs, redes sociais ou fóruns
- Releases de produto, mesmo em beta limitado
- Atividade de venda incluindo cotações, respostas a RFP e ofertas
- Divulgações a investidores ou conselheiros sem NDA

Oferta de venda de produto que incorpore a invenção pode disparar a
contagem do período de graça mesmo sem venda concretizada — sinalize.

#### Filtro 5: Detectabilidade

Se um concorrente infringisse esta invenção, daria para perceber?
Invenção praticada em segredo — processamento server-side, operações de
back-office, técnicas internas de fabricação — pode estar melhor
protegida como **segredo de empresa (LPI 195 XI/XII)** do que como
patente. Publicar patente sobre invenção indetectável é entregá-la a
concorrentes em troca de ativo que nunca se vai poder exigir.

**🔴 Baixa detectabilidade:**
- Algoritmo server-side sem padrão de saída observável
- Processo interno de fabricação (ex.: nova etapa de etching em
  processo semicondutor)
- Pipeline de dados ou metodologia analítica que roda dentro da
  infraestrutura do concorrente
- Composição de dados de treino ou técnica de treino de modelo ML —
  visível apenas via *probing* fino, se de todo

Para essas, sinalize para **decisão patente vs. segredo de empresa**. A
pergunta não é "isso é patenteável" mas "deveríamos patentear se
pudéssemos". Roteie para quem no perfil de prática toma decisões de
classificação de segredo de empresa.

**✓ Alta detectabilidade:**
- Produto de consumo — visível no produto
- API, SDK, protocolo publicados — visível em tráfego de rede ou docs
- Mecanismo físico em produto distribuído — engenharia-reverseável
- Código compilado com assinaturas distintivas em binário distribuído

#### Filtro 6: Valor estratégico

Isto se alinha com a estratégia de patente da empresa no perfil? É onde
a triagem fica específica à empresa, não doutrinária.

Cheque contra o perfil:

- **Ofensiva (construir para asserir):** este ativo merece asserção?
  Patente estreita e facilmente contornável tem valor ofensivo menor que
  reivindicação de mecanismo ampla. O cenário competitivo é tal que se
  desejaria litigar?
- **Defensiva (construir para proteger FTO):** cobre área onde
  concorrentes depositam? Depósito defensivo em área que ninguém deposita
  é gasto perdido.
- **Licenciamento / receita:** é licenciável? Quem pagaria por isto, e em
  que circunstâncias?

Cheque também:

- É tecnologia **núcleo** (parte da diferenciação do produto) ou
  **periférica**? Núcleo vale mais.
- Qual o **cenário competitivo**? Pesado em patente (semicondutores,
  farma) — deposite cedo ou perde a corrida. Leve em patente (muitos
  segmentos de software open-source-heavy) — às vezes vale gastar em
  outro lugar.
- A área tecnológica está nas **áreas de interesse** do perfil? Se não,
  é em regra recusa independente da doutrina.

### Passo 3: Monte o memorando de triagem da invenção

Formato:

> **Memorando de triagem de invenção — [título da invenção]**
>
> **Bottom line: [PROSSEGUIR / INVESTIGAR / RECUSAR]**
>
> *[Uma frase — o motivo em linguagem simples.]*
>
> ---
>
> ### Resultados da triagem
>
> | Filtro | Veredito | Notas |
> |---|---|---|
> | Sinais de novidade | [✓ / 🟡 / 🔴] | [raciocínio em uma linha] |
> | Atividade inventiva | [✓ / 🟡 / 🔴] | [uma linha] |
> | Matéria patenteável (LPI 10/18) | [✓ / 🟡 / 🔴] | [uma linha] |
> | Divulgação / período de graça (LPI 12) | [✓ / 🟡 / 🔴] | [uma linha + datas] |
> | Detectabilidade | [✓ / 🟡 / 🔴] | [uma linha] |
> | Valor estratégico | [✓ / 🟡 / 🔴] | [uma linha, referenciada ao perfil] |
>
> ---
>
> ### Perguntas em aberto
>
> *Coisas que mudariam a resposta. Inventor, equipe de prossecução ou
> especialista precisaria endereçá-las antes que esta triagem vire
> decisão de depósito.*
>
> - [pergunta]
> - [pergunta]
>
> ### Próximos passos (árvore de decisão)
>
> Escolha uma e eu desenvolvo:
>
> 1. **Comissionar a busca de anterioridade** — redijo o pedido de busca
>    para [escritório externo / fornecedor de busca: INPI, Espacenet,
>    Google Patents, WIPO PATENTSCOPE, Solve Intelligence] com os
>    conceitos da reivindicação, inventores, classificação tecnológica e
>    quaisquer referências conhecidas.
> 2. **Voltar ao(à) inventor(a) por mais fatos** — redijo as perguntas
>    de follow-up sobre [itens específicos em aberto].
> 3. **Rotear para escritório externo para juízo sob LPI 10 /
>    patente-vs-segredo** — redijo transmittal resumindo o que a
>    triagem achou e o que precisa de juízo de especialista.
> 4. **Recusar e enviar o agradecimento padrão** — redijo o
>    agradecimento ao(à) inventor(a) e arquivo a comunicação com a
>    razão da recusa.
> 5. **Sinalizar para segredo de empresa** — redijo nota para quem
>    detém classificação de segredo, explicando por que segredo é a
>    melhor rota (LPI 195 XI/XII).

Aplique o cabeçalho de work-product conforme o papel. Aplique a nota do
revisor. Mantenha o entregável limpo de narração interna ("Estou usando
a skill invention-intake..." etc.).

### Passo 4: Recomende o bottom-line

A linha de fundo é uma de três:

- **PROSSEGUIR** — filtros suficientes limpos (ou claramente sanáveis)
  para justificar busca de anterioridade e revisão por
  advogado(a)/mandatário(a). NÃO é "patenteável" — é "passa na triagem
  inicial, investigação justificada."
- **INVESTIGAR** — um ou mais filtros sinalizaram algo que precisa de
  mais informação, revisão por especialista ou pergunta de
  esclarecimento ao(à) inventor(a) antes que decisão prosseguir/recusar
  possa ser tomada. Nomeie o item aberto.
- **RECUSAR** — filtro bateu em flag fatal (impedida por divulgação há
  mais de 12 meses sem direitos estrangeiros aplicáveis; claramente
  óbvia; matéria não-patenteável sob LPI 10/18; fora das áreas
  tecnológicas do perfil; fundamentalmente indetectável sem rota de
  segredo de empresa). Declare a razão claramente.

Uma RECUSA deve sempre vir com motivo concreto que o(a) inventor(a)
entenda. "Não patenteável" não é motivo aceitável; "impedida pelo seu
paper no NeurIPS 2023 — o período de graça BR (LPI 12) venceu em
dezembro de 2024 e EPO/CN aplicam novidade absoluta" é.

## Guardrails

**Nunca diga "patenteável".** O mais perto que pode chegar é "passa na
triagem inicial, investigação justificada." Patenteabilidade é conclusão
que advogado(a)/mandatário(a) registrado(a) alcança após busca de
anterioridade e construção de reivindicação.

**Nunca rode busca de anterioridade nesta skill.** Uma WebSearch por
"isso já existe?" não é busca de anterioridade — é checagem de
plausibilidade que o usuário também pode rodar. Se quiser sanity-check
de novidade, diga explicitamente ("checagem rápida na web — a técnica é
discutida em [X] — isto NÃO é busca de anterioridade, é contexto para
a triagem") e marque `[web — verificar]`.

**Adie em LPI 10.** Para qualquer coisa limítrofe (especialmente
software, IA/ML, métodos de negócio implementados por computador),
sinalize para revisão por mandatário(a)/especialista. LPI 10 V e
Diretrizes INPI de IIC são onde profissionais rotineiramente discordam
e onde chamada confiante de não-especialista envelhece mal.

**Sinalize detectabilidade antes de valor estratégico.** Invenção
indetectável que seria "alto valor estratégico" como patente é em regra
maior valor como segredo de empresa. Não recomende PROSSEGUIR em
invenção indetectável sem endereçar a alternativa de segredo.

**Casos urgentes recebem sinalização urgente.** Se a triagem bateu em
divulgação pública dentro do período de graça (Brasil), ou qualquer
divulgação pública com direitos estrangeiros no escopo, diga isso no
topo. Bottom line, então: "**Urgente — período de graça BR vence
[data], direitos estrangeiros já em risco.**" Isso é o tipo de achado
que advogado(a) precisa ver nos primeiros três segundos.

**Respeite o roteamento.** Pelo perfil, esta triagem é passo de triagem.
Quem decide o que depositar é o(a) advogado(a)/mandatário(a)
responsável pela prossecução. A triagem alimenta essa pessoa; não a
substitui.

## Gate de não-advogado(a)

Se o papel é **não-advogado(a)** (com ou sem acesso a advogado(a)),
encerre o memorando com:

> **Esta é ferramenta de triagem da sua comunicação, não parecer de
> patenteabilidade. A decisão sobre depositar — e como — é de
> advogado(a)/mandatário(a) registrado(a) no INPI (LPI 217). Se esta
> triagem disser PROSSEGUIR ou INVESTIGAR, seu próximo passo NÃO é
> depositar ou redigir reivindicações; é compartilhar este memorando (e
> a comunicação subjacente) com o(a) profissional de patente. Se não
> houver advogado(a)/mandatário(a) engajado(a), pontos de partida:
> **OAB-seccional** (Comissão de PI), **ABPI**, mandatários(as)
> registrados(as) no INPI (lista pública), **NPJ universitário** se
> cabível.**
