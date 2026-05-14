---
name: fto-triage
description: >
  Triagem de liberdade de operar (FTO — freedom-to-operate) — primeira leitura
  estruturada de patentes potencialmente bloqueantes, NÃO um parecer de FTO.
  Use quando produto, processo ou funcionalidade está sendo avaliado contra
  patentes bloqueantes, quando perguntarem se há algo que impeça um lançamento,
  ou para construir um primeiro quadro de reivindicações contra as patentes
  mais plausíveis antes da revisão por advogado(a)/mandatário(a) de patente.
  Esta skill nunca conclui que um produto está livre para lançamento.
argument-hint: "[descreva o produto / processo / funcionalidade e jurisdições — ou só o assunto e eu pergunto]"
---

# /fto-triage

> **Nota de adaptação BR.** Esta skill foi originalmente concebida para o
> sistema norte-americano (35 U.S.C., USPTO, IPR/PGR, treble damages).
> No Brasil, patente é regida pela **Lei de Propriedade Industrial — LPI
> (Lei 9.279/1996)**, com prossecução perante o **INPI**. Diferenças
> estruturais relevantes nesta triagem:
> - **Vigência.** PI: 20 anos contados do **depósito** (LPI art. 40). O
>   parágrafo único do art. 40 (mínimo de 10 anos da concessão) foi declarado
>   **inconstitucional pelo STF na ADI 5529** (2021), com modulação para
>   patentes ainda válidas naquela data. MU: 15 anos do depósito. DI: 10 anos
>   do depósito, prorrogável por 3 períodos de 5.
> - **Anuidades** (LPI art. 84) a partir do 3º ano; falta de pagamento gera
>   arquivamento/extinção, não "maintenance fee" trianual como nos EUA.
> - **Reivindicações** interpretadas conforme **LPI art. 41** (com base no
>   relatório descritivo e desenhos). A doutrina de equivalentes existe na
>   jurisprudência brasileira mas é aplicada com mais reserva que nos EUA.
> - **Software no Brasil é direito autoral (Lei 9.609/98), não patente.**
>   Patente de invenção implementada por computador é admissível somente
>   quando integrar efeito técnico além do programa em si (LPI art. 10 V e
>   Diretriz de Exame INPI).
> - **Nulidade administrativa** (LPI arts. 50–55, prazo 6 meses da concessão)
>   ou **judicial** (LPI art. 56, foro Justiça Federal, INPI litisconsorte).
>   **Não há equivalente direto a IPR/PGR.**
> - **Treble damages e willfulness** não existem. Reparação é por perdas e
>   danos (LPI arts. 208–210; CC art. 944), podendo incluir lucros cessantes
>   por uma das três métricas do art. 210 LPI (a critério do prejudicado, o
>   mais favorável). Não há triplicação automática; **a má-fé pesa na
>   fixação da indenização e em multa por litigância (CPC art. 80) e em
>   custas, mas não triplica indenização.**
> - **Atos infringentes** estão em LPI arts. 42 e 184–186 (penal); para
>   modelo de utilidade em art. 9º; para DI em arts. 187–195. Cada um pode
>   ser ato isolado de infração.

**Esta NÃO é um parecer de FTO.** Um parecer formal de FTO requer busca
abrangente, interpretação plena das reivindicações e análise elemento-a-
elemento por advogado(a)/mandatário(a) de patente. A infração de patente é
responsabilidade objetiva (LPI art. 42 — independe de dolo ou culpa para o
dever de cessação; culpa/dolo pesam na indenização). "Sem patentes
bloqueantes óbvias" significa que a triagem não achou — não significa que o
produto está livre.

## Instruções

1. Leia `~/.claude/plugins/config/claude-for-legal/propriedade-intelectual/CLAUDE.md`. Se
   contiver `[PLACEHOLDER]`, pare e direcione para
   `/propriedade-intelectual:cold-start-interview`.
2. Siga o fluxo abaixo.
3. Faça intake (produto/processo, detalhe técnico, jurisdições, patentes
   conhecidas, timing).
4. Faça busca preliminar se houver conector disponível (busca.inpi.gov.br,
   Espacenet, Google Patents, Solve Intelligence, PCT via WIPO). Se nenhum,
   diga isso explicitamente e prossiga com as patentes que o usuário forneceu.
5. Para as 2–5 patentes mais plausíveis, construa um primeiro quadro de
   reivindicações contra cada reivindicação independente — elemento a
   elemento. Leitura literal primeiro; doutrina de equivalentes em passada
   separada; flag de infração indireta/induzida.
6. Liste perguntas em aberto que um estudo FTO real responderia
   (exequibilidade, histórico de prossecução perante o INPI, processos de
   nulidade administrativa/judicial, licenciamento disponível, histórico de
   enforcement do titular).
7. Escreva o memorando de triagem na pasta da matéria ou na pasta de outputs.
   Aplique o cabeçalho de work-product conforme o papel.
8. Encerre com próximos passos recomendados, nota sobre indenização agravada
   por dolo (LPI art. 210 + CC art. 944), e gate para não-advogado(a) se for
   o caso.

Esta skill nunca conclui que um produto está livre para lançamento. Em
qualquer dúvida, sinalize — quem decide é o(a) advogado(a)/mandatário(a)
de patente.

## Exemplos

```
/propriedade-intelectual:fto-triage "modelo de reconhecimento de fala on-device para wearable de consumo, lançamento BR primeiro"
```

```
/propriedade-intelectual:fto-triage
```

---

## ESTA NÃO É PARECER DE FTO

**O guardrail mais alto do plugin. Diga isto no topo de toda saída. Não
descarte. Não suavize. Não deixe o(a) leitor(a) passar batido(a).**

> **Esta não é um parecer de freedom-to-operate.** Um parecer FTO é juízo
> profissional, em regra de advogado(a)/mandatário(a) de patente, baseado em
> busca abrangente, interpretação plena das reivindicações (LPI art. 41) e
> análise elemento-a-elemento contra cada reivindicação de cada patente
> relevante. Esta triagem é primeira olhada estruturada do que pode estar
> por aí. "Sem patentes bloqueantes óbvias" significa que a triagem não
> achou — não que o produto está livre. **Infração de patente é
> responsabilidade objetiva quanto ao dever de cessação (LPI art. 42); dolo
> ou culpa qualificam a indenização (LPI arts. 208–210; CC art. 944).** A
> decisão de fabricar, usar, vender, oferecer à venda ou importar é
> decisão de negócio informada por estudo formal de FTO e pelo juízo do(a)
> advogado(a)/mandatário(a) — não por esta triagem. Advogado(a)/mandatário(a)
> de patente revê antes que qualquer um confie nisto para decisão de produto.

Sub-sinalizar patente bloqueante é porta de uma via — produto lançado, ação
de infração um ano depois, indenização agravada por dolo/má-fé na mesa.
Sobre-sinalizar é porta de duas vias — o(a) revisor(a) afunila a lista numa
leitura. Fique do lado da porta de duas vias. Sempre.

### Nota sobre indenização agravada por dolo

Ler esta triagem é ler algo sobre patentes. Ler algo sobre patentes pode,
em alguns casos, ser relevante para qualificar dolo na fixação da
indenização (LPI arts. 209–210; CC arts. 187, 944). Por isso o output é
marcado como confidencial sob sigilo profissional quando rodado por
advogado(a)/mandatário(a), e o output a não-advogado(a) é enquadrado como
pesquisa para levar ao(à) advogado(a)/mandatário(a). Não discuta patentes
específicas surgidas nesta triagem fora de canais sob sigilo.

---

## Contexto de matéria

**Contexto de matéria.** Cheque `## Workspaces de matéria` no CLAUDE.md de
prática. Se `Habilitado` for `✗` (default para in-house), pule o resto deste
parágrafo — skills usam contexto de nível de prática e a maquinaria de
matéria é invisível. Se habilitado e não houver matéria ativa, pergunte:
"Qual matéria? Rode `/propriedade-intelectual:matter-workspace switch <slug>` ou diga
`nível de prática`." Carregue o `matter.md` da matéria ativa para contexto
e overrides. Escreva saídas na pasta da matéria em
`~/.claude/plugins/config/claude-for-legal/propriedade-intelectual/matters/<matter-slug>/`.
Nunca leia arquivos de outra matéria, salvo se `Contexto cross-matéria` for
`on`.

Matérias de FTO de patente são candidatas particularmente comuns a
**clean-team** ou confidencialidade reforçada na abertura da matéria.
Respeite a marcação de confidencialidade do `matter.md`.

---

## Carregue o perfil de prática primeiro

Antes da triagem, leia
`~/.claude/plugins/config/claude-for-legal/propriedade-intelectual/CLAUDE.md`. Puxe:

- **Papel** de `## Quem está usando` (advogado(a) vs. não-advogado(a) muda
  o cabeçalho de work-product e o gate de não-advogado(a) abaixo).
- **Registrado em** e **postura de enforcement** de `## Perfil de prática
  PI` e `## Postura de enforcement` (úteis para cruzar com portfólio
  defensivo e para defaults de jurisdição).
- **Escritório de patente** de `## Perfil de prática PI` → `Roster de
  escritórios externos` para o passo de roteamento.
- **Integrações** de `## Integrações disponíveis` — especialmente
  busca.inpi.gov.br, Solve Intelligence, Espacenet ou qualquer MCP de
  pesquisa de patente. Determina quais buscas estão disponíveis.
- **Postura de decisão** de `## Postura de decisão em juízos jurídicos
  subjetivos` — esta skill nunca conclui "não infringe".

Se o CLAUDE.md contiver `[PLACEHOLDER]` ou `[Razão social]`, surface:

> Percebi que você não configurou seu perfil de prática — é assim que
> calibro postura, jurisdições e cadeia de aprovação à sua prática.
>
> **Duas escolhas:**
> - Rode `/propriedade-intelectual:cold-start-interview` (2 min) para configurar, e eu rodo
>   isto calibrado para SUA prática.
> - Diga **"provisório"** e eu rodo contra defaults genéricos — jurisdição
>   BR, apetite de risco médio, papel advogado(a), sem playbook — marcando
>   todo output `[PROVISÓRIO — configure o perfil para output calibrado]`,
>   para você ver o que eu faço antes de comprometer.

### Modo provisório

Se o usuário disser "provisório", rode a triagem FTO normalmente usando
estes defaults: apetite de risco médio, papel advogado(a), jurisdição BR,
sem playbook (faça a análise completa em vez de bater contra lista de
posições). Marque a nota do revisor e todo bloco de achado com
`[PROVISÓRIO]`. No fim do output, anexe:

> "Foi rodada genérica contra defaults. Rode `/propriedade-intelectual:cold-start-interview`
> para obter output calibrado para SUA prática — seu playbook, sua
> jurisdição, seu apetite de risco. 2 minutos."

---

## Intake

Pergunte em lote único:

> Vou rodar triagem FTO. Algumas perguntas primeiro:
>
> 1. **Produto, processo ou funcionalidade.** O que está sendo fabricado,
>    usado, oferecido à venda, vendido ou importado? Descreva em palavras
>    simples — a essência técnica, não o pitch de marketing.
> 2. **Detalhe técnico.** Há diagramas arquiteturais, specs relevantes a
>    reivindicação, página pública do produto ou documento técnico que possa
>    compartilhar? (Quanto mais detalhe, mais real a triagem.)
> 3. **Jurisdições.** Onde será fabricado, usado, vendido, ofertado à venda,
>    importado? Cada um é ato infringente autônomo sob **LPI art. 42**.
>    Defaulto para BR se nada for especificado.
> 4. **Patentes conhecidas.** Há patentes já no radar — portfólio de
>    concorrente, pool SEP conhecido, carta de NPE/troll, algo que um
>    engenheiro mencionou?
> 5. **Timing.** Quão perto do lançamento? Se faltam meses, a triagem é
>    cedo e design-around está na mesa. Se já está embarcado, estamos em
>    modo cobrir o downside.

Aguarde a resposta. Se a descrição for vaga ("um agente de IA", "um banco
de dados"), puxe uma vez:

> Me dê a essência técnica — o que faz, como faz, qual é a parte que você
> acha que pode ser nova? Reivindicações de patente vivem nesse nível.

---

## Escopo — patentes de invenção e MU; DI fora de escopo

**Esta skill analisa patentes de invenção (PI) e modelos de utilidade
(MU).** Para outros títulos, sinalize e roteie, não monte quadro de
reivindicações:

- **DI — Desenho Industrial (LPI arts. 94–121).** Teste diferente —
  semelhança de **conjunto-imagem** ao observador médio (jurisprudência BR
  por analogia + LPI art. 100; ausência de quadro reivindicatório
  tradicional). Roteie para a ramificação de DI em
  `infringement-triage` e para advogado(a) de DI. **DI não é analisada
  nesta triagem FTO** — sobreposição de DI deve ser sinalizada como
  workstream paralelo.
- **Marca registrada** — analisada em `/propriedade-intelectual:clearance` ou
  `/propriedade-intelectual:infringement-triage`, modo marca.
- **Programa de computador (Lei 9.609/98)** — não há reivindicação;
  proteção é por direito autoral. Triagem é cópia/derivação, não FTO.
  Roteie para análise de obra autoral. Patente com reivindicação que
  implementa software é PI normal e fica no escopo (atentando para LPI
  art. 10 V — "programas de computador em si" não são invenção).

Cross-flag também **conjunto-imagem / trade dress brasileiro**: se a
aparência do produto é o risco, os mesmos fatos podem dar pretensão por
concorrência desleal (**LPI art. 195, III** — imitação de elementos que
permitam identificar o produto/serviço). Sinalize como track paralelo.

---

## Busca

### O que o usuário tem conectado

Leia `## Integrações disponíveis`:

- **busca.inpi.gov.br conectado:** rode busca preliminar contra a descrição
  técnica. Anote a data da busca, a query usada, as classes IPC/CPC
  cobertas e a janela temporal (PI/MU em vigor + pedidos publicados).
- **Espacenet / Google Patents / Solve Intelligence / WIPO PATENTSCOPE
  disponíveis:** use.
- **Nenhum:** diga explicitamente. Não infira patentes a partir de
  conhecimento do modelo e as apresente como resultados de busca.

### Fallback quando não houver base de patente conectada

Escreva esta declaração exata no output:

> **Nenhuma base de patentes foi pesquisada.** Esta triagem não consultou
> busca.inpi.gov.br, Espacenet, Google Patents, WIPO PATENTSCOPE, Solve
> Intelligence ou qualquer outro corpus de patente. Uma busca estruturada
> nas jurisdições em escopo é necessária antes de confiar nesta triagem
> para qualquer decisão de lançamento. A análise abaixo limita-se às
> patentes nomeadas pelo usuário ou citadas na conversa.

Então prossiga. O primeiro quadro de reivindicações ainda agrega valor —
apenas rotule o escopo com honestidade.

### Sinais complementares (não substituem busca)

Se disponíveis e o usuário permitir, varra sinais não-patentários que
levantem suspeita:

- **Depósitos de concorrentes** no INPI na área do produto (via RPI ou
  busca.inpi.gov.br).
- **NPEs / trolls conhecidos** atuando na classe tecnológica.
- **Declarações SEP** (IEEE, ETSI, 3GPP) se o produto tocar standard
  relevante.
- **Litígios reportados** na área (JusBrasil, PJe-TRFs, STJ, CourtListener
  para comparado).

Cada sinal é razão para olhar mais fundo, não hit confirmado. Marque como
sinal no output, não como patente identificada.

---

## Para cada patente relevante encontrada ou fornecida

Capture:

- **Número de processo / pedido / carta-patente** e **jurisdição**
- **Título**
- **Titular e inventores** (cf. LPI arts. 88–93 — regime CLT + invento de
  empregado; ao titular pode caber a empresa, depende do contrato)
- **Data de depósito e data de concessão**
- **Data de expiração** — cuidado:
  - PI: 20 anos do depósito (LPI art. 40); parágrafo único declarado
    **inconstitucional** pelo STF (ADI 5529, 2021) — não confiar em
    "mínimo de 10 anos da concessão" para patentes posteriores à
    modulação. Para patentes anteriores ainda em vigor naquela data,
    cheque o quadro de modulação.
  - MU: 15 anos do depósito.
- **Status de anuidades** (LPI art. 84) — se em atraso ou arquivada, a
  patente pode estar **extinta** (LPI art. 78 III) e não bloqueia.
  Verificar no e-INPI / RPI antes de confiar.
- **Contagem de reivindicações — independentes e dependentes**
- **Reivindicações independentes como concedidas** (e eventuais
  modificações pós-concessão em nulidade administrativa)
- **Procedimentos correlatos** — nulidade administrativa (LPI 50–55),
  nulidade judicial (LPI 56), ações de infração, oposições conhecidas.
- **Histórico de prossecução INPI** — exigências, alterações que
  estreitaram reivindicações, manifestações sobre escopo.

**Não suplemente em silêncio.** Se uma busca trouxe patente, atribua o
resultado. Se o usuário mencionou patente, diga isso. Nunca invente
número, nunca "preencha" elemento de reivindicação que o documento não
suporta, nunca imagine data de expiração. Se o status de anuidade não
puder ser verificado na busca, escreva "status de anuidades não verificado
no resultado de busca — confirmar no e-INPI antes de confiar na
vigência".

---

## Primeiro quadro de reivindicações

Este é o núcleo da triagem. Escolha as patentes com leitura mais plausível
sobre o produto — em geral as 2–5 com mapeamento técnico mais próximo — e
percorra cada reivindicação independente elemento a elemento.

**Para cada patente selecionada, escreva um quadro por reivindicação
independente:**

| Elemento da reivindicação | O produto pratica isto? | Base |
|---|---|---|
| "Um/uma [preâmbulo]" | [sim / não / possivelmente / depende de interpretação] | [uma frase — o que do produto mapeia; o que não mapeia; o que é ambíguo] |
| "compreendendo [elemento 1]" | [sim / não / possivelmente] | [mapeamento ou lacuna] |
| "em que [elemento 2]" | [sim / não / possivelmente] | [mapeamento ou lacuna] |
| [continue para cada elemento] | | |

**Regras do quadro:**

- **Todo elemento importa.** A reivindicação é infringida apenas se o
  produto pratica TODOS os elementos de pelo menos uma reivindicação
  (regra da equivalência integral). Falta de um elemento literal afasta
  infração literal naquela reivindicação. Não pule.
- **Doutrina de equivalentes é passada separada.** Primeiro a leitura
  literal. Depois, para elementos "não", anote se cabe leitura por
  equivalentes (diferenças insubstanciais / função-modo-resultado). A
  doutrina existe na jurisprudência brasileira (TJs, STJ por reflexo) mas
  é aplicada com mais reserva que nos EUA — marque como sujeita a juízo
  do(a) advogado(a)/mandatário(a).
- **Interpretação da reivindicação é tarefa do(a) advogado(a)/mandatário(a).**
  Onde um termo possa ser lido restrita ou amplamente e o resultado mude
  a infração, sinalize o termo e anote ambas as leituras. Não escolha em
  silêncio. A LPI art. 41 ancora a interpretação no relatório descritivo
  e desenhos.
- **Infração indireta (induzida, contributiva) e infração dividida** são
  apenas sinais. Não tente análise completa; anote que podem se aplicar e
  exigem juízo de advogado(a)/mandatário(a).

> **Sistemas de patente diferem por jurisdição.** O quadro construído sob
> LPI BR não transfere automaticamente para outros sistemas:
> - **EUA:** all-elements rule, doutrina de equivalentes mais robusta,
>   estoppel por histórico de prossecução, 35 U.S.C. §§ 284/289 para
>   danos.
> - **Europa (EPO/UPC):** procedimento UPC desde 2023; doutrina alemã
>   (Schneidmesser/Kunststoffrohrteil) na Alemanha, com bifurcação
>   validade/infração.
> - **China:** modelos de utilidade (shiyong xinxing), exame CNIPA,
>   interpretação distinta.
> - **Japão:** modelo de utilidade, exame JPO, doutrina de equivalentes
>   mais estreita.
>
> Quando jurisdições não-BR estiverem em escopo: "Esta análise usa o
> framework BR de reivindicação (LPI arts. 41–42). Produto fabricado na
> China e vendido na UE exige análise CNIPA e EP, não quadro BR. Posso
> sinalizar as questões que a análise BR levanta, mas a chamada de
> infração e validade exige revisão sob a lei de [jurisdição]."

**Postura de decisão:** pelo perfil de prática, esta skill nunca conclui
"sem infração". Use:

- "Produto pratica todos os elementos da Reivindicação X conforme redigida;
  revisão por advogado(a)/mandatário(a) necessária antes de prosseguir."
- "Um ou mais elementos não estão claramente presentes; revisão por
  advogado(a)/mandatário(a) necessária para avaliar leitura literal e
  doutrina de equivalentes."
- "A interpretação da reivindicação é dispositiva no elemento [Y];
  interpretação por advogado(a)/mandatário(a) necessária antes de
  prosseguir."

---

## Perguntas em aberto

Toda patente surgida na triagem deve gerar lista de perguntas em aberto que
um estudo FTO real responderia. Exemplos:

- A patente é exequível — o titular está correto, há questões de
  legitimidade, defeito de inventoria, cessões averbadas?
- O que o requerente disse sobre o termo [X] no exame INPI, e isso limita
  a reivindicação?
- A reivindicação foi objeto de nulidade administrativa (LPI 50–55) ou
  judicial (LPI 56) — qual o status?
- Há licença disponível (pool de standards, declaração de não-asserção,
  política de licenciamento)?
- Qual o histórico real de enforcement deste titular no Brasil?

Liste em texto simples.

---

## Próximos passos recomendados

Agrupe pelo que a triagem encontrou:

- **Se todo elemento de reivindicação independente mapeia no produto
  (leitura literal):** *Pare e procure advogado(a)/mandatário(a) de
  patente.* Opções típicas: parecer formal de FTO, design-around, licença,
  desafio de validade (nulidade administrativa LPI 50–55 ou judicial LPI
  56), ou (raramente) prosseguir com risco. A escolha é decisão de
  negócio informada por advogado(a)/mandatário(a).
- **Se elementos cortam para os dois lados ou interpretação é dispositiva:**
  Estudo FTO completo por advogado(a)/mandatário(a) de patente. Não lance
  com base nesta triagem.
- **Se a patente parecer expirada, arquivada ou inexequível:**
  Advogado(a)/mandatário(a) confirma o status — a triagem não.
- **Se nenhuma patente foi identificada na busca mas não havia acesso a
  base:** Busca formal é o próximo passo, não decisão de lançamento.
- **Sempre:** sinalize risco de indenização agravada por dolo. Se a
  triagem surfou patente específica, a empresa agora tem conhecimento dela.
  Prosseguir sem análise adicional pode pesar como dolo na fixação da
  indenização (LPI arts. 209–210; CC arts. 187, 944). Advogado(a)/
  mandatário(a) documenta o caminho adiante.

---

## Formato de saída

Prefixe o cabeçalho de work-product do CLAUDE.md `## Saídas / Outputs`.
Marque como confidencial sob sigilo profissional se o papel for
advogado(a); cf. gate de não-advogado(a) abaixo.

```markdown
[CABEÇALHO DE WORK-PRODUCT]

# Triagem FTO — Primeira Passada (NÃO É PARECER)

**Esta não é um parecer de freedom-to-operate.** Parecer formal de FTO
requer busca abrangente, interpretação plena das reivindicações e análise
elemento-a-elemento por advogado(a)/mandatário(a) de patente. Infração
gera dever objetivo de cessação (LPI art. 42); dolo/culpa qualificam a
indenização (LPI arts. 208–210; CC art. 944). "Sem patentes bloqueantes
óbvias" significa que a triagem não achou — não que o produto está livre.
Advogado(a)/mandatário(a) de patente revisa antes que se confie nisto
para decisão de produto.

**Resultado da triagem:** [VERDE / AMARELO / VERMELHO — uma frase porquê]

## Objeto

- **Produto / processo / funcionalidade:** [descrição, essência técnica]
- **Detalhe técnico examinado:** [o que foi revisto — spec, diagrama,
  página pública, código, descrição de engenheiro]
- **Jurisdições em escopo:** [fabricar / usar / vender / oferecer / importar
  — por LPI art. 42]
- **Timing:** [pré-lançamento / próximo ao lançamento / já embarcado]

## Escopo da busca

- **Bases pesquisadas:** [busca.inpi.gov.br / Espacenet / Google Patents /
  WIPO PATENTSCOPE / Solve Intelligence — ou "nenhuma base pesquisada"]
- **Query / abordagem:** [texto da query, classes IPC/CPC, palavras-chave,
  classificações]
- **Data / janela temporal:** [data da busca; patentes em vigor + pedidos
  publicados desde YYYY-MM-DD]
- **Jurisdições cobertas pela busca:** [lista]
- **O que NÃO foi pesquisado:** [varredura por titular, declarações SEP,
  portfólios de NPE, DI, equivalentes em outras jurisdições — conforme o
  caso]

*Se nenhuma base foi pesquisada:* **Nenhuma base de patentes foi
pesquisada.** Esta triagem não consultou busca.inpi.gov.br, Espacenet,
Google Patents, WIPO PATENTSCOPE, Solve Intelligence ou qualquer outro
corpus de patente. Uma busca estruturada nas jurisdições em escopo é
necessária antes de confiar nesta triagem para qualquer decisão de
lançamento.

## Patentes identificadas

| Patente | Jurisdição | Titular | Depósito / Concessão | Expiração | Em vigor? | Fonte |
|---|---|---|---|---|---|---|
| [número] | [BR/EP/...] | [titular] | [datas] | [data] | [sim/não/não verificado] | [link do resultado ou "fornecido pelo usuário"] |

## Quadros de reivindicação — primeira passada

### [Patente nº] — Reivindicação independente [N]

> "[Texto exato da Reivindicação N]"

| Elemento | Praticado pelo produto? | Base |
|---|---|---|
| [elemento 1] | [sim/não/possivelmente] | [mapeamento ou lacuna] |
| [elemento 2] | [sim/não/possivelmente] | [mapeamento ou lacuna] |

**Leitura literal:** [todos os elementos mapeiam / um ou mais elementos
não mapeiam claramente / interpretação da reivindicação é dispositiva no
elemento [Y]]

**Doutrina de equivalentes (apenas flag):** [leitura por equivalentes
plausível no elemento [Y] — exige interpretação de advogado(a)/mandatário(a)
/ não plausível nos elementos surgidos / histórico de prossecução sugere
limitação]

**Infração indireta / dividida (apenas flag):** [anote se a leitura
depende de teoria de indução, contribuição ou divisão — análise por
advogado(a)/mandatário(a) necessária]

*(Repita para cada reivindicação independente de cada patente selecionada.)*

## Perguntas em aberto

- [pergunta 1]
- [pergunta 2]

## Sinais (não patentes confirmadas)

- [depósitos de concorrente / atividade de NPE / declarações SEP / litígios
  na área — cada um, razão para buscar mais, não patente identificada]

## Próximos passos recomendados

- [estudo FTO completo por advogado(a)/mandatário(a) — primeira
  recomendação salvo se a busca abrangente já tiver sido feita e nada
  surgiu]
- [opções de design-around se foi encontrada leitura literal]
- [licença / nulidade administrativa / nulidade judicial / análise sob
  risco conforme orientação do(a) advogado(a)/mandatário(a)]
- [roteamento conforme CLAUDE.md — escritório de patente nomeado no perfil
  de prática]

## Nota sobre indenização agravada

Esta triagem surfa patentes específicas. Prosseguir com o produto sem
revisão adicional após este conhecimento pode pesar como dolo na fixação
da indenização (LPI arts. 209–210; CC arts. 187, 944). O caminho adiante
deve ser documentado por advogado(a)/mandatário(a) de patente; a decisão
de negócio de lançar, fazer design-around ou licenciar é informada por
parecer formal de FTO e pelo juízo profissional — não por esta triagem.

## Verificação de citações

Todo número de patente, citação literal de reivindicação, data e fato de
prossecução neste memorando deve ser verificado contra a fonte autorizada
(e-INPI / RPI, EPO Register, equivalente nacional) antes de qualquer
confiança. Citações literais de reivindicação são o ponto de erro mais
comum — uma palavra muda a análise. Não cite resultado que não consiga
abrir.
```

---

## Gate de não-advogado(a)

Antes de emitir o output, leia `## Quem está usando`. Se o Papel for
Não-advogado(a):

> Este output é triagem de pesquisa, não parecer jurídico. Lançar,
> continuar a vender ou investir nesse produto com base apenas nesta
> triagem tem consequências jurídicas — incluindo responsabilidade objetiva
> pelo dever de cessação por infração de patente, e indenização que pode
> ser agravada por dolo (LPI arts. 208–210; CC art. 944). Advogado(a)/
> mandatário(a) precisa avaliar antes que você avance.
>
> Aqui está um brief para levar ao(à) advogado(a) — vai cortar o tempo da
> conversa:
>
> [Gere resumo de 1 página: descrição do produto, jurisdições em escopo,
> busca rodada (e o que não foi pesquisado), patentes surgidas e leituras
> de primeiro quadro, perguntas em aberto, e três perguntas para fazer
> ao(à) advogado(a)/mandatário(a).]
>
> Para localizar profissional habilitado: para prossecução INPI, um(a)
> **mandatário(a) registrado(a) no INPI** ou advogado(a) inscrito(a) na
> OAB com atuação em patente. Para contencioso, advogado(a) OAB com
> experiência em PI. Recursos: **OAB-seccional** (Comissão de PI), **ABPI**
> (Associação Brasileira da Propriedade Intelectual), **ASIPI** (regional),
> **NPJ** universitário se o caso comportar, **Defensoria Pública** quando
> houver hipossuficiência. Para outras jurisdições, registros das
> respectivas oficinas (EPO, UKIPO, USPTO etc.).

Entregue o memorando completo junto do brief. Não esconda a análise.
Sinalize que a própria triagem é documento de pesquisa sob sigilo
profissional e não deve ser encaminhada a terceiros não-advogados(as).

---

## Local de saída

Se workspaces de matéria estiverem habilitados e houver matéria ativa,
escreva em
`~/.claude/plugins/config/claude-for-legal/propriedade-intelectual/matters/<matter-slug>/outputs/fto-triage-<assunto-slug>-YYYY-MM-DD.md`.
Caso contrário, escreva em
`~/.claude/plugins/config/claude-for-legal/propriedade-intelectual/outputs/fto-triage-<assunto-slug>-YYYY-MM-DD.md`
e indique o caminho.

Acrescente uma linha ao `history.md` da matéria se houver matéria ativa.

---

## Encerre com a árvore de decisão de próximos passos

Termine com a árvore de decisão conforme CLAUDE.md `## Saídas / Outputs`.
Customize as opções ao que esta skill produziu — os cinco ramos default
(redigir X, escalar, buscar mais fatos, observar e esperar, outra coisa)
são ponto de partida, não trava. A árvore É a saída; o(a) advogado(a)/
mandatário(a) escolhe.

## O que esta skill NÃO faz

- **Emite parecer de FTO.** Nunca. Guardrail mais alto do plugin.
- **Interpreta reivindicações.** Onde a interpretação é dispositiva,
  sinaliza o termo e as duas leituras plausíveis. Não escolhe.
- **Decide validade.** Pode anotar processos de nulidade conhecidos; não
  opina sobre novidade, atividade inventiva, suficiência descritiva ou
  matéria não-patenteável (LPI arts. 10 e 18).
- **Redige reivindicações de patente.** Esse plugin não vai lá; roteie
  para advogado(a)/mandatário(a) de prossecução.
- **Modela exposição de danos.** Modelagem de danos é trabalho de perito.
- **Trata análise de segredo de empresa ou de marca** — use
  `/propriedade-intelectual:infringement-triage` com o modo certo.
- **Cita outputs a contrapartes ou audiências fora do círculo de sigilo.**
  É documento sob sigilo profissional.

---

## Tom

Tecnicamente preciso. Elemento a elemento. Cada flag é específico a um
elemento de reivindicação ou a uma patente conhecida. Sem prosa hedge no
corpo — os guardrails no topo e fundo fazem o trabalho de escopo, e a
análise faz a análise. Quem lê deve sair sabendo o que a triagem olhou,
o que não olhou, e qual o próximo passo.
