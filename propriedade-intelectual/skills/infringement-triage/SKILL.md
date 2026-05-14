---
name: infringement-triage
description: >
  Triagem de infração em marca, direito autoral, patente e segredo de empresa
  — lista de flags com fatores que cortam para cada lado, NÃO uma decisão.
  Use para avaliar se alguém está infringindo sua PI ou se você pode estar
  infringindo a dos outros, quando aparece imitação/cópia, ou ao decidir se
  vale acionar e como.
argument-hint: "[descreva os fatos e qual direito — ou só os fatos e eu pergunto qual direito]"
---

# /infringement-triage

> **Nota de adaptação BR.** Esta skill foi originalmente concebida para o
> sistema norte-americano. No Brasil, aplicam-se primordialmente:
> - **Marca:** LPI (Lei 9.279/96) arts. 122–175, 189 (penal) e 195 III/V
>   (concorrência desleal, conjunto-imagem); LPI 125 (alto renome) e 126
>   (notoriamente conhecida).
> - **Direito autoral:** Lei 9.610/98 (titularidade, infração, sanções
>   civis arts. 102 ss.) e Lei 9.609/98 (software, regime autoral).
> - **Patente / MU / DI:** LPI arts. 6º–121, 42 (atos infringentes), 184–186
>   e 187–195 (penal).
> - **Segredo de empresa:** **LPI art. 195, XI e XII** (concorrência
>   desleal); não há "Defend Trade Secrets Act" ou "UTSA" no Brasil. CLT
>   arts. 482 g/h (justa causa). Não há ação federal autônoma análoga ao
>   DTSA; pretensão é cível (concorrência desleal + perdas e danos) e penal
>   (LPI 195 XI/XII).
> - **Remoção de conteúdo / safe harbor:** Marco Civil (Lei 12.965/2014)
>   art. 19 exige **ordem judicial** para responsabilizar provedor por
>   conteúdo de terceiro. **Não há equivalente direto ao 17 U.S.C. § 512
>   (DMCA notice-and-takedown).** Para direito autoral, a regra é a mesma —
>   judicial. Exceção: art. 21 (nudez/ato sexual sem consentimento),
>   notificação extrajudicial possível.
> - **Indenização:** LPI arts. 208–210 (perdas e danos por uma das três
>   métricas, mais favorável ao prejudicado, à escolha) + CC art. 944.
>   **Não há treble damages** automáticos. **Não há "Rule 11" — equivalente
>   é litigância de má-fé (CPC art. 80) e abuso de direito (CC 187).**
> - **Ação declaratória** (CPC art. 19 I) substitui a "declaratory judgment
>   action" americana e funciona como contra-ataque a ameaça injustificada
>   de PI.

**Esta é uma triagem, não decisão de infração ou de ausência de infração.**
Análise de infração é intensiva em fato e juridicamente complexa. Agir com
base em uma triagem — disparar notificação extrajudicial, recusar-se a
parar, ajuizar, ou decidir não — sem revisão por advogado(a) é como
empresas vão parar do lado errado de sucumbência, litigância de má-fé,
ações declaratórias de inexistência de infração e (no caso patente)
indenização agravada por dolo.

## Instruções

1. Leia `~/.claude/plugins/config/claude-for-legal/propriedade-intelectual/CLAUDE.md`. Se
   contiver `[PLACEHOLDER]`, pare e direcione para
   `/propriedade-intelectual:cold-start-interview`.
2. Siga o fluxo abaixo.
3. Pergunte qual direito está em jogo — marca / direito autoral / patente /
   segredo de empresa / misto. Se misto, rode cada um separadamente; não
   misture.
4. Faça intake comum (postura — titular ou acusado, jurisdição, timing,
   provas).
5. Percorra os fatores específicos do modo:
   - **Marca** — confusão (semelhança gráfica/fonética/ideológica +
     afinidade de produtos/serviços) + alto renome (LPI 125) + notoriamente
     conhecida (LPI 126) + concorrência desleal (LPI 195).
   - **Direito autoral** — titularidade + (registro facultativo) + acesso
     + substancial similaridade + limitações da Lei 9.610 arts. 46–48
     (rol exaustivo; NÃO há "fair use" no Brasil).
   - **Patente** — primeira passada de quadro de reivindicações (roteie
     para `fto-triage` para estrutura completa); literal + doutrina de
     equivalentes; indireta + dividida; defesas de nulidade a considerar.
   - **Segredo de empresa** — sigilo + medidas razoáveis + apropriação
     indevida (LPI 195 XI/XII); engenharia reversa como flag de defesa.
6. Produza lista de flags com direção — o que corta para o titular, o que
   corta para o acusado, o que é misto. Nunca conclua.
7. Escreva o memorando na pasta da matéria ou na pasta de outputs. Aplique
   o cabeçalho de work-product conforme o papel.
8. Encerre com próximos passos, gate de não-advogado(a) se for o caso, e
   — se a postura de prática suportar assertividade — ofereça redigir a
   notificação extrajudicial via `/propriedade-intelectual:cease-desist` ou o pedido de
   remoção via `/propriedade-intelectual:takedown`. Não redija automaticamente.

Esta skill nunca conclui. Em qualquer dúvida, sinalize — quem decide é o(a)
advogado(a).

## Exemplos

```
/propriedade-intelectual:infringement-triage "concorrente lançou ferramenta chamada APEXSEED na classe 9 — temos APEXLEAF registrada na classe 9; confusão provável?"
```

```
/propriedade-intelectual:infringement-triage "ex-engenheiro levou anotações da arquitetura do nosso modelo para concorrente — possível segredo de empresa?"
```

```
/propriedade-intelectual:infringement-triage
```

(E a skill pergunta qual direito e pelos fatos.)

---

## ISTO É UMA TRIAGEM, NÃO UMA DECISÃO

**O guardrail mais alto do plugin. Diga isto no topo de toda saída. Não
descarte. Não suavize.**

> **Esta é uma triagem, não decisão de infração ou ausência de infração.**
> Análise de infração é intensiva em fato e juridicamente complexa. A
> triagem identifica fatores e sinaliza os mais relevantes; não conclui.
> Conclusão de que algo infringe ou não é juízo jurídico que exige análise
> de advogado(a) sobre os fatos, o escopo da reivindicação ou direito, a
> lei da jurisdição aplicável e as defesas prováveis. Agir com base em
> triagem — disparar notificação extrajudicial, recusar-se a parar,
> ajuizar, ou decidir não — sem revisão de advogado(a) é como empresas
> acabam do lado errado de sucumbência, litigância de má-fé (CPC 80), ação
> declaratória de inexistência de infração, e indenização agravada por
> dolo (LPI 208–210 + CC 944).

Sub-sinalizar conflito é porta de uma via — notificação não enviada e a
marca se diluiu no mercado; pretensão não exercida e a prescrição correu
(LPI art. 225 — 5 anos para reparação civil); obra autoral copiada
mantida no ar. Sobre-sinalizar é porta de duas vias — o(a) advogado(a)
afunila. Fique do lado da porta de duas vias.

---

## Contexto de matéria

**Contexto de matéria.** Cheque `## Workspaces de matéria` no CLAUDE.md de
prática. Se `Habilitado` for `✗` (default para in-house), pule o resto
deste parágrafo — skills usam contexto de nível de prática e a maquinaria
de matéria é invisível. Se habilitado e não houver matéria ativa,
pergunte: "Qual matéria? Rode `/propriedade-intelectual:matter-workspace switch <slug>`
ou diga `nível de prática`." Carregue o `matter.md` da matéria ativa para
contexto e overrides. Escreva saídas na pasta da matéria em
`~/.claude/plugins/config/claude-for-legal/propriedade-intelectual/matters/<matter-slug>/`.
Nunca leia arquivos de outra matéria, salvo se `Contexto cross-matéria`
for `on`.

Triagens de infração frequentemente desembocam em notificação extrajudicial
ou pedido judicial de remoção. Abra matéria se não houver uma ativa e a
prática for privada — triagem, notificação e qualquer resposta downstream
pertencem ao mesmo workspace.

---

## Carregue o perfil de prática primeiro

Leia `~/.claude/plugins/config/claude-for-legal/propriedade-intelectual/CLAUDE.md`. Puxe:

- **Papel** de `## Quem está usando`.
- **Postura de enforcement** de `## Postura de enforcement` — a saída da
  triagem deve terminar com sugestão de roteamento consistente com a
  postura declarada (agressiva / equilibrada / conservadora) e o(a)
  aprovador(a) indicado(a) para o tipo de peça.
- **Registrado em / Onde fazemos enforcement** de `## Perfil de prática
  PI` — determina o framework jurisdicional default.
- **Integrações** de `## Integrações disponíveis` — JusBrasil, STJ, STF,
  busca.inpi.gov.br, CourtListener (comparado), Solve Intelligence — afeta
  se a triagem pode citar jurisprudência, decisões anteriores ou anterioridade.
- **Postura de decisão** de `## Postura de decisão em juízos jurídicos
  subjetivos` — esta skill nunca conclui em limite subjetivo.

Se o CLAUDE.md tiver `[PLACEHOLDER]`, surface:

> Percebi que você não configurou seu perfil — é assim que calibro postura,
> jurisdições e cadeia de aprovação à sua prática.
>
> **Duas escolhas:**
> - Rode `/propriedade-intelectual:cold-start-interview` (2 min) para configurar.
> - Diga **"provisório"** e eu rodo contra defaults genéricos — jurisdição
>   BR, apetite médio, papel advogado(a), sem playbook — marcando tudo
>   `[PROVISÓRIO — configure o perfil]`.

### Modo provisório

Se o usuário disser "provisório", rode normalmente com defaults: apetite
médio, papel advogado(a), jurisdição BR, sem playbook. Marque a nota do
revisor e todo bloco de achado com `[PROVISÓRIO]`. No final, anexe:

> "Foi rodada genérica contra defaults. Rode `/propriedade-intelectual:cold-start-interview`
> para output calibrado à SUA prática. 2 minutos."

---

## Seleção de modo

Pergunte no topo, antes de qualquer coisa:

> Qual direito estamos triando?
>
> 1. **Marca** — confusão, alto renome, concorrência desleal/conjunto-imagem
> 2. **Direito autoral** — substancial similaridade, limitações Lei 9.610
>    arts. 46–48 (rol exaustivo; sem fair use)
> 3. **Patente / MU** — primeira passada de quadro, literal + doutrina de
>    equivalentes
> 4. **Segredo de empresa** — sigilo, medidas razoáveis, apropriação
>    indevida (LPI 195 XI/XII)
> 5. **Misto / não sei** — descreva os fatos e eu separo

Se "não sei", ajude a triar. Os mesmos fatos podem implicar múltiplos
direitos (concorrente usa nosso logo — marca; e o produto é cópia próxima
do nosso — possível patente, direito autoral em embalagem, possível
conjunto-imagem; e foi um ex-empregado que lançou — segredo de empresa).

**Se mais de um direito está em jogo, rode triagem para cada um,
separadamente.** Não amasse junto. Cada direito tem fatores, regras
jurisdicionais e remédios distintos.

---

## Intake (comum a todos os modos)

> Antes de percorrer fatores:
>
> 1. **Postura.** Você é a parte potencialmente titular (estão pegando o
>    seu) ou a potencialmente acusada (estamos sob olhar)? Os fatores são
>    simétricos mas o output difere — triagem "estão copiando o meu"
>    encaminha à notificação assertiva; triagem "podemos estar expostos"
>    encaminha a memorando de risco.
> 2. **Jurisdição.** Qual país / foro / tribunal? Default BR (Justiça
>    Estadual ou Federal — patente/marca infração em Federal por força do
>    INPI como litisconsorte em nulidade, mas infração pura é em geral
>    estadual). Sinalize se lei estrangeira pode incidir.
> 3. **Timing.** Há prazo prescricional ou decadencial correndo?
>    - Marca infração: 5 anos da ciência (LPI 225).
>    - Patente: 5 anos da ciência (LPI 225).
>    - Nulidade de marca: 5 anos da concessão (LPI 174).
>    - Nulidade de patente: prazo da vigência (LPI 56).
>    - Direito autoral: 3 anos para reparação (CC 206 §3º V).
> 4. **Que provas / evidências / documentos você tem?** Print, URL, foto
>    de embalagem, trecho de código, contrato de ex-empregado, ata
>    notarial (CPC 384), notificação extrajudicial recebida.

Aguarde a resposta antes de percorrer fatores.

---

## Modo marca

### Confusão

Aplique o **teste brasileiro de confusão**, conforme jurisprudência
consolidada (STJ; TJs; INPI Diretrizes de Análise de Marcas):

- **Semelhança de marca** — gráfica, fonética e ideológica.
- **Afinidade de produtos/serviços** — mesmo segmento mercadológico,
  classe NCL (Nice), público-alvo.
- **Canal de comercialização e público** — onde o consumidor encontra o
  produto, sofisticação.
- **Força do sinal anterior** — fantasia/arbitrário > sugestivo >
  descritivo (com distintividade adquirida — LPI 124 VI e c/c teoria do
  *secondary meaning*) > genérico (irregistrável).
- **Intenção** — provas de cópia, conjunto-imagem, marca quase-igual.
- **Confusão efetiva** — qualquer indício (pesquisa, redirecionamentos,
  postagens em redes).
- **Convivência setorial e potencial expansão.**

> Frameworks comparados (apenas referência): du Pont (USPTO), Polaroid
> (2º Circ.), Sleekcraft (9º Circ.) — usar somente se a matéria envolver
> jurisdição estrangeira.

### Alto renome e notoriamente conhecida

- **Alto renome (LPI 125):** proteção em todas as classes. Exige
  reconhecimento INPI específico (Resolução INPI/PR 107/2013; vide
  Resolução INPI vigente). Sem reconhecimento, sem alto renome — não basta
  "ser famosa".
- **Notoriamente conhecida (LPI 126; art. 6º bis CUP):** proteção no ramo
  de atividade, dispensa registro no Brasil. Não confunda com alto
  renome.

Se a marca anterior não tem reconhecimento de alto renome, sinalize que
alto renome é estiramento.

### Concorrência desleal / conjunto-imagem

LPI **art. 195, III** — concorrência desleal por imitação de **conjunto-
imagem** (trade dress brasileiro). Pode subsistir independentemente de
registro de marca. Exige distintividade, não-funcionalidade e risco de
confusão/parasitismo.

LPI art. 195, IV e V — uso indevido de nome empresarial; uso de marca
alheia para fim de cooptação de clientela.

### Publicidade comparativa / propaganda enganosa

CDC (Lei 8.078/90) arts. 36–38; CONAR (autorregulamentação publicitária);
Lei 9.279/96 art. 195 (atos de concorrência desleal). Não há "Lanham Act
§ 43(a)" no Brasil — a base é CDC + LPI 195 + CC (responsabilidade civil)
e, eventualmente, CADE para condutas anti-concorrenciais.

### Saída

Tabela de fatores; o que corta para cada lado; linha de conclusão "isto
não é decisão". Encerre com sugestão de roteamento contra a postura de
enforcement do perfil de prática.

---

## Modo direito autoral

### Titularidade

A reivindicante é a titular (ou licenciada com legitimidade)?
Trabalhos sob CLT (Lei 9.610 art. 4º): obras criadas sob contrato de
trabalho — em regra, transferência por contrato ao empregador; obras de
empregado público têm regime próprio. Co-autoria (Lei 9.610 art. 5º
VIII a, art. 23); cessões (art. 49); direitos morais inalienáveis e
irrenunciáveis (art. 27).

Para **software (Lei 9.609/98)** — em regra, direitos pertencem ao
empregador/contratante quando o desenvolvimento decorre da relação de
trabalho/serviço (art. 4º Lei 9.609).

### Registro

**Registro é facultativo no Brasil** (Lei 9.610 art. 18 e 19). Não é
pré-condição para ajuizar. O registro funciona como prova de
anterioridade. Para software, registro no INPI também é facultativo (Lei
9.609 art. 2º §1º + art. 3º).

> Diferente do regime norte-americano (17 U.S.C. § 411) onde registro é
> pressuposto para ação federal. No Brasil, ausência de registro **não
> impede a ação**; apenas afeta carga probatória de anterioridade.

### Acesso + substancial similaridade

Dois caminhos para provar cópia:

- **Acesso + similaridade probatória** — réu teve acesso à obra e há
  semelhanças que indicam cópia.
- **Similaridade marcante** — mesmo sem prova de acesso, a similaridade é
  tal que criação independente é improvável.

Para substancial similaridade, jurisprudência brasileira analisa
elementos protegíveis (forma de expressão, não ideias subjacentes —
Lei 9.610 art. 8º) e o todo da obra.

### Limitações ao direito autoral (Lei 9.610 arts. 46–48)

**Não há "fair use" no Brasil.** As limitações estão em **rol exaustivo**
nos arts. 46, 47 e 48 da Lei 9.610. Verificar individualmente:

- Art. 46: pequenos trechos para uso privado do copista; citação para
  estudo/crítica/polêmica com indicação de autor e fonte; representação
  teatral e execução musical no recesso familiar etc.
- Art. 47: paráfrases e paródias que não forem verdadeiras reproduções
  da obra originária nem lhe implicarem descrédito.
- Art. 48: obras situadas permanentemente em logradouros públicos podem
  ser representadas livremente por pinturas, desenhos, fotografias e
  procedimentos audiovisuais.

A doutrina e parte da jurisprudência admitem a chamada "regra dos três
passos" (Convenção de Berna art. 9.2) para interpretação flexível em
casos não previstos — sinalize como `[review]` quando o caso não cair
literalmente no rol e o tribunal puder usar a "regra dos três passos".

### "Safe harbor" / responsabilidade de provedor

**Marco Civil art. 19 — responsabilidade do provedor de aplicação por
conteúdo de terceiro só após descumprimento de ordem judicial
específica.** **Não há regime de notice-and-takedown análogo ao 17
U.S.C. § 512.** Exceções:

- **Art. 21 Marco Civil:** divulgação não-autorizada de nudez/ato sexual
  privado — notificação extrajudicial obrigando à remoção (limites e
  procedimento conforme art. 21 e Lei 13.718/2018).
- **Direito autoral:** o STF reconheceu repercussão geral (Tema 987,
  RE 1.057.258) e ainda há debate sobre o regime aplicável a provedores
  diante de conteúdo infrator de direito autoral. Sinalize como
  `[review — Tema 987 / jurisprudência em evolução]`.

Se o acusado é provedor de aplicação hospedando conteúdo de usuário,
sinalize: (a) regra geral exige ordem judicial específica para remoção
e responsabilização (Marco Civil 19); (b) identificação específica do
conteúdo (URL) é requisito (STJ jurisprudência consolidada); (c) há
discussão sobre incidência do art. 19 ou regime especial em direito
autoral.

### Saída

Fatores sinalizados; balanço das limitações (arts. 46–48) com "a triagem
não conclui"; notas de titularidade / registro / responsabilidade de
provedor. Roteamento por postura.

---

## Modo patente

**Roteie para `/propriedade-intelectual:fto-triage` para o framework detalhado.** Este
modo é o espelho da skill de FTO — mesmos quadros de reivindicação,
mesmo flag de doutrina de equivalentes, mesma regra de equivalência
integral — aplicados a um produto acusado em vez do próprio.

### Tipo de título — ramificação antes do fluxo

**Cheque o título primeiro.** No Brasil, os tipos LPI são:

- **PI (Patente de Invenção) / MU (Modelo de Utilidade)** — fluxo abaixo
  se aplica.
- **DI (Desenho Industrial)** — LPI arts. 94–121. Teste diferente —
  comparação por **conjunto-imagem** ao observador médio (aplicação
  análoga à de marca, art. 95 LPI exige novidade e originalidade).
  **Não monte quadro de reivindicação para DI**; não rode doutrina de
  equivalentes; não faça mapeamento elemento-a-elemento. DI tem proteção
  conferida pelos desenhos, não por reivindicações no sentido patente.
- **Programa de computador** — Lei 9.609/98, regime autoral. Análise é
  cópia/derivação, não infração de patente.

**Teste de infração de DI no Brasil.** Não há "Egyptian Goddess" — a
jurisprudência (TJs e STJ) aplica análise de **conjunto-imagem** e
distintividade vs. anterioridades, observador médio do segmento.
Compare **aparência global**, não elementos individuais. Aspectos
**puramente funcionais** estão fora da proteção (LPI 100 II).

**Cross-flag concorrência desleal.** Os mesmos fatos de forma podem
configurar **concorrência desleal por conjunto-imagem (LPI 195 III)**.
Sinalize como track paralelo.

### Triagem de DI — saída

Como você não consegue ver os desenhos da patente nem o produto acusado
diretamente, a triagem de DI é, em grande parte, pedido de material e
moldura para análise:

- **Peça os desenhos.** "Não consigo rodar a análise de conjunto-imagem
  sem ver as figuras do título e o produto acusado. Cole ou anexe: (a)
  figuras do DI (incluindo linhas tracejadas que delimitam contexto não
  reivindicado), (b) fotos do produto acusado em ângulos comparáveis,
  (c) anterioridades de que tenha notícia."
- **Anterioridades.** O observador médio é "familiarizado com as
  anterioridades", então o escopo do DI estreita quanto mais lotada a
  área. Sinalize o que se sabe e o que falta.
- **Funcional vs. ornamental.** Percorra as características e sinalize
  quais são funcionais (não protegidas).
- **Linhas tracejadas.** Solid lines = reivindicado; tracejado =
  contexto. Sinalize se a alegação de cópia está em região reivindicada
  ou de contexto.
- **Cross-flag conjunto-imagem (LPI 195 III).**

**Roteie para especialista em DI para qualquer coisa além de primeira
passada.** Esta skill sinaliza; não decide infração.

### Fluxo de PI/MU

> **Sistemas de patente diferem por jurisdição.** O quadro construído sob
> LPI BR não transfere automaticamente — vide nota em `/propriedade-intelectual:fto-triage`.
> Quando jurisdições não-BR estiverem em escopo, sinalize.

- Produto / processo / método acusado — descrito em detalhe técnico.
- Patente(s) em questão.
- Quadro de reivindicação para cada independente: mapeamento elemento-
  a-elemento ao produto acusado.
- Infração literal primeiro. Doutrina de equivalentes como flag.
- Infração indireta (induzida, contributiva) e dividida como flags.
- **Defesas de nulidade a considerar** — falta de novidade (LPI 11),
  falta de atividade inventiva (LPI 13), não-suficiência descritiva (LPI
  24), matéria não-patenteável (LPI 10 e 18 — programa "em si",
  método/regra puramente matemática, métodos cirúrgicos/terapêuticos,
  seres vivos exceto microrganismos transgênicos). Defesas em ação de
  infração via reconvenção/exceção, ou ação autônoma de nulidade (LPI
  56). Sinalize cada uma; não opine.
- **Defesas de inexigibilidade** — abuso de direito (CC 187), abuso de
  posição dominante (Lei 12.529/11 — patent ambush em SEPs), exaustão
  (LPI 43 IV) e licença compulsória (LPI 68 ss.). Cada uma é tarefa do
  advogado(a).
- **Postura de indenização** — perdas e danos por uma das três métricas
  do **LPI art. 210** (à escolha do prejudicado, a mais favorável: lucros
  do infrator; lucros que o prejudicado teria realizado; royalty que o
  infrator teria pago). Sinalize que **não há treble damages**, mas o
  dolo pesa na fixação (CC 944 par. único — não aplicável a infração que
  é objetiva quanto à cessação, mas relevante na fixação da
  indenização — `[review]`).

### Saída

Quadros de reivindicação. Flags de elemento. Flags de defesa. Roteamento
para advogado(a)/mandatário(a). Vide `fto-triage` para estrutura
completa — o modo patente desta skill usa o mesmo formato com "produto
acusado" no lugar de "produto próprio".

### Handoff para o claim chart completo

Para quadro detalhado elemento-a-elemento adequado a fundamentação de
infração ou nulidade, rode `/contencioso:claim-chart`. O quadro
desta triagem é primeira passada para identificar os mapeamentos mais
fortes e mais frágeis; o quadro de contencioso constrói a versão completa
com citações pinpoint, flags de interpretação, reivindicações
dependentes e o fluxo de verificação.

---

## Modo segredo de empresa

### Era segredo?

Aplique **LPI art. 195, XI e XII** — concorrência desleal por divulgação,
exploração ou utilização, sem autorização, de conhecimentos, informações
ou dados confidenciais, utilizáveis na indústria, comércio ou prestação
de serviços, a que teve acesso mediante relação contratual ou empregatícia
(inciso XI) ou por meios ilícitos (inciso XII).

**Não há DTSA, não há UTSA no Brasil.** A pretensão é:

- **Penal** — LPI 195 XI/XII (ação penal pública condicionada à
  representação, regra geral da concorrência desleal).
- **Cível** — perdas e danos (LPI 207–210; CC 186, 927); ação cominatória
  de cessação; tutela de urgência (CPC 300) para busca e apreensão (LPI
  200) e ordens de não-fazer.
- **Trabalhista** — justa causa (CLT 482 g — violação de segredo da
  empresa; CLT 482 h — ato de indisciplina ou de insubordinação).

Flag:

- **Não conhecido publicamente** — não acessível ao público nem aos
  concorrentes que dele poderiam obter valor.
- **Valor econômico decorrente do sigilo** — independente da forma de
  expressão; combinações de elementos públicos podem constituir
  segredo (acórdãos TJSP, TJRS, doutrina).
- **Esforço razoável para mantê-lo secreto** — vide próxima seção.

### Medidas razoáveis

- NDAs com empregados, prestadores, contrapartes. Escopo, assinados,
  exigíveis?
- Controle de acesso — técnico (RBAC), físico (portas, crachás),
  organizacional (need-to-know).
- Marcação — "confidencial" em documentos, código, dados.
- Entrevistas de desligamento / devolução de materiais.
- Política e treinamento de segredo de empresa.
- Cláusulas de confidencialidade pós-contratuais (CLT silencia sobre
  prazo; jurisprudência admite por tempo razoável).

Sinalize o que existe e o que falta. *Razoável* é intensivo em fato; a
triagem não decide se as medidas foram razoáveis — lista.

### Apropriação indevida

Aquisição por meios impróprios ou divulgação/uso em violação de dever.
Meios impróprios incluem: subtração, suborno, declaração falsa, violação
ou indução de violação de dever de manter sigilo, espionagem (eletrônica
ou não).

- **Padrão de ex-empregado:** novo empregador, sobreposição de funções,
  timing da saída, documentos levados (devolvidos?), logs de acesso,
  canais de recrutamento, contratos de cessão de criação e invenção,
  cláusulas de confidencialidade. Atenção à LPI arts. 88–93 (invenção
  de empregado).
- **Divulgação inadvertida:** A divulgação foi feita por pessoa com
  dever? O destinatário sabia ou tinha razão para saber da violação?
- **Engenharia reversa** — defesa quando os meios foram lícitos. Sinalize
  se engenharia reversa é plausível nos fatos.

### Prescrição

LPI 225 — 5 anos para reparação civil; pretensão de cessação não
prescreve enquanto persiste a violação. Penal — prazos do CP segundo a
pena cominada.

### Saída

Três grupos de flags — sigilo, medidas, apropriação — cada um com o que
corta para cada lado. Roteamento por postura.

---

## Formato de saída (todos os modos)

Prefixe o cabeçalho de work-product do CLAUDE.md `## Saídas / Outputs`.

```markdown
[CABEÇALHO DE WORK-PRODUCT]

# Triagem de Infração — [Marca | Direito Autoral | Patente | Segredo de Empresa] (NÃO É DECISÃO)

**Esta é uma triagem, não decisão de infração ou ausência de infração.**
A triagem identifica fatores e sinaliza os mais relevantes; não conclui.
Conclusão exige análise de advogado(a) sobre fatos, escopo do direito,
jurisdição e defesas. Agir com base em triagem sem revisão de
advogado(a) é como empresas acabam do lado errado de sucumbência,
litigância de má-fé (CPC 80), ação declaratória de inexistência de
infração e indenização agravada por dolo.

**Resultado da triagem:** [VERDE / AMARELO / VERMELHO — uma frase porquê]

## Postura e escopo

- **Postura da parte:** [titular / acusado]
- **Direito em questão:** [marca / direito autoral / patente / segredo de
  empresa]
- **Jurisdição:** [BR — Estadual ou Federal / outra]
- **Framework jurídico aplicado:** [cite teste e lei aplicáveis]
- **Postura de prescrição / decadência:** [status do prazo]
- **Provas / evidências revistas:** [lista]

## Análise de fatores

[Tabela específica do modo — fatores de confusão / limitações Lei 9.610
/ quadro de reivindicação / elementos de segredo de empresa. Cada fator
tem flag e direção. É lista de flags, não veredito.]

## Defesas e patamares

[Específicos do modo: alto renome (LPI 125) / notoriamente conhecida (LPI
126) / responsabilidade de provedor (Marco Civil 19/21) / nulidade /
abuso de direito / exaustão / engenharia reversa / consentimento /
licença / prescrição. Sinalize cada um.]

## O que corta para qual lado — resumo

| Fator | Flag | Direção (titular / acusado / misto) |
|---|---|---|
| [fator 1] | [nota] | [direção] |

**Conclusão:** *Esta skill não conclui.* Análise de advogado(a) requerida
antes de agir. Os fatores cortando para [direção] são [resumo breve];
os fatores cortando para [direção] são [resumo breve].

## Próximos passos recomendados

- [parecer formal de advogado(a) / roteamento para escritório de PI do
  perfil de prática]
- [preservação de provas e *legal hold* — se prazo prescricional ou
  necessidade de ata notarial (CPC 384) está rodando]
- [desenvolvimento de fatos antes de decidir — ex.: logs de acesso,
  histórico de prossecução INPI, pesquisas de mercado, evidência
  pericial]
- [roteamento conforme `## Postura de enforcement`, se a postura é
  assertiva]

## Verificação de citações

Todo caso, lei, número de registro, citação de reivindicação e exhibit
citado aqui deve ser verificado contra fonte autorizada antes de
qualquer confiança. Testes jurisdicionais variam e mudam — confirme a
autoridade controlante atual.
```

---

## Gate de não-advogado(a)

Antes de emitir o output, leia `## Quem está usando`. Se o Papel for
Não-advogado(a):

> Este output é triagem de pesquisa, não parecer jurídico. Enviar
> notificação extrajudicial, decidir não parar, ajuizar ou se basear em
> "isso cabe em limitação da Lei 9.610" com base apenas nesta triagem tem
> consequências jurídicas — incluindo **litigância de má-fé (CPC 80)** por
> alegação sem base, exposição a **ação declaratória de inexistência de
> infração (CPC 19 I)** por carta ameaçadora, indenização agravada por
> dolo em matéria de patente, sucumbência em ações de concorrência desleal.
> Advogado(a) precisa avaliar antes que você avance.
>
> Aqui está um brief para levar ao(à) advogado(a):
>
> [Gere resumo de 1 página: direito em questão, postura, fatos e
> evidências, fatores surgidos, defesas sinalizadas, e três perguntas a
> fazer ao(à) advogado(a).]
>
> Para encontrar profissional habilitado: **OAB-seccional** (Comissão de
> PI), **ABPI**, **ASIPI** (regional), **NPJ universitário** se cabível,
> **Defensoria Pública** quando houver hipossuficiência. Para
> prossecução perante o INPI, mandatário(a) registrado(a) (LPI 217) ou
> advogado(a) inscrito(a) com atuação em PI. Para outras jurisdições,
> use o registro da respectiva oficina (EPO, UKIPO, USPTO, etc.) e da
> ordem profissional.

Entregue a triagem junto do brief.

---

## Local de saída

Se workspaces de matéria estiverem habilitados e houver matéria ativa,
escreva em
`~/.claude/plugins/config/claude-for-legal/propriedade-intelectual/matters/<matter-slug>/outputs/infringe-<modo>-<assunto-slug>-YYYY-MM-DD.md`.
Caso contrário, escreva em
`~/.claude/plugins/config/claude-for-legal/propriedade-intelectual/outputs/infringe-<modo>-<assunto-slug>-YYYY-MM-DD.md`
e indique o caminho.

Acrescente uma linha ao `history.md` da matéria se houver matéria ativa.

---

## Handoff para skills de enforcement

Se a saída da triagem aponta para assertividade e a postura do perfil
permite, ofereça:

> Quer que eu redija uma notificação extrajudicial sobre isso? Rode
> `/propriedade-intelectual:cease-desist`. Vou usar a lista de flags desta triagem como
> base factual e aplicar a cadeia de aprovação do perfil — a peça não
> sai sem o(a) aprovador(a) bater o martelo.

Ou, se o modo é direito autoral e o acusado é conteúdo hospedado:

> Quer que eu prepare pedido de remoção? Rode `/propriedade-intelectual:takedown`.
> Lembrando: no Brasil, regra geral é **ordem judicial** (Marco Civil
> 19); extrajudicial só nas hipóteses do art. 21 (nudez/ato sexual sem
> consentimento). A skill cobre os dois caminhos.

Não redija a peça automaticamente a partir da triagem. A decisão de
afirmar é do(a) aprovador(a), não da triagem.

---

## Encerre com a árvore de decisão de próximos passos

Termine com a árvore conforme CLAUDE.md `## Saídas / Outputs`. Customize
as opções ao que esta skill produziu — os cinco ramos default são ponto
de partida. A árvore É a saída; o(a) advogado(a) escolhe.

## O que esta skill NÃO faz

- **Conclui infração ou ausência de infração.** Nunca. Guardrail mais
  alto.
- **Substitui evidência de pesquisa de mercado, peritos em danos ou
  interpretação de reivindicação por especialista.**
- **Avalia defesas específicas de jurisdição fora do escopo da triagem.**
  Se os fatos atravessam fronteiras, sinaliza que análise de lei
  estrangeira é necessária.
- **Decide aplicabilidade de limitações da Lei 9.610 como matéria de
  direito.** O rol é exaustivo e a aplicação é intensiva em fato,
  reservada ao(à) advogado(a) e, em última instância, à corte.
- **Redige notificação extrajudicial, pedido de remoção ou petição
  inicial.** São skills separadas (`/propriedade-intelectual:cease-desist`,
  `/propriedade-intelectual:takedown`) gateadas pela cadeia de aprovação do perfil.
- **Cita outputs a contrapartes.** Sob sigilo profissional se o cabeçalho
  se aplicar.

---

## Tom

Fator a fator, flag a flag. Sem prosa hedge. O guardrail no topo faz o
trabalho de escopo; a análise faz a análise. Advogado(a) deve sair do
output sabendo quais fatores estão sinalizados, quais defesas se aplicam,
e o que precisa fazer a seguir para afirmar ou recuar.
