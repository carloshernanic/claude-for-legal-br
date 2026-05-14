---
name: takedown
description: >
  Remoção de conteúdo on-line no Brasil — opera sob o Marco Civil da Internet
  (Lei 12.965/2014). MODO ENVIAR: minutar pedido judicial de remoção (art. 19,
  regra geral) OU notificação extrajudicial do art. 21 (única hipótese
  legal de notice-and-takedown extrajudicial — nudez/ato sexual sem
  consentimento). MODO TRIAGEM: avaliar pedido extrajudicial recebido (em
  regra, provedor NÃO é obrigado a cumprir sem ordem judicial). Use quando
  marca, direito autoral ou imagem estiver sendo infringida on-line.
argument-hint: "<--send | --respond> [contexto ou caminho da notificação recebida]"
---

# /takedown

> **Nota de adaptação BR.** Esta skill foi reescrita a partir da versão original (DMCA §512, direito norte-americano). **Não há equivalente brasileiro ao notice-and-takedown extrajudicial do DMCA.** A regra do Brasil é o **Marco Civil da Internet (Lei 12.965/2014, art. 19)**: o provedor de aplicações de internet só é responsabilizado civilmente por conteúdo de terceiros após **ordem judicial específica** não cumprida. A **única exceção** é o art. 21 (cenas de nudez ou ato sexual de caráter privado sem consentimento), em que a notificação extrajudicial obriga o provedor a remover. Não invente "DMCA brasileiro" — não existe.

Dois modos. Escolha um:

- `/propriedade-intelectual:takedown --send` — minutar o instrumento adequado: (a) **pedido judicial de remoção** (tutela de urgência, Marco Civil art. 19; competência cível comum) — regra geral para marca, direito autoral, difamação, etc.; **ou** (b) **notificação extrajudicial do art. 21** (única hipótese extrajudicial — nudez/ato sexual sem consentimento da vítima). A skill ajuda a identificar qual instrumento cabe.
- `/propriedade-intelectual:takedown --respond` — triagem de notificação extrajudicial que **você recebeu** (do suposto titular, alegando infração). Opções: cumprir / não cumprir e exigir ordem judicial / engajar / contra-notificar internamente.

> Não existe "contranotificação DMCA" no Brasil. Se houve remoção por ordem judicial, a defesa é processual (contestação, agravo, recurso). Se houve remoção voluntária pelo provedor (TOS), a defesa é contratual/consumerista.

## Instructions

1. **Ler o perfil de prática.** Carregue `~/.claude/plugins/config/claude-for-legal/propriedade-intelectual/CLAUDE.md`. Se contiver `[PLACEHOLDER]` ou não existir, pare e diga: "Este plugin precisa de setup antes de gerar saída útil. Rode `/propriedade-intelectual:cold-start-interview` — a skill de remoção depende da sua matriz de aprovação e perfil de prática."

2. **Checar matter workspaces.** Conforme `## Workspaces de matéria`: se `Habilitado` for `✗`, pule. Se habilitado e não houver matéria ativa, pergunte: "Para qual matéria é isto? Rode `/propriedade-intelectual:matter-workspace switch <slug>` ou diga `practice-level`."

3. **Despachar em `$ARGUMENTS`:**
   - `--send` → modo enviar. Identificar instrumento adequado (judicial via art. 19 OU extrajudicial via art. 21), portões de gate, minutar peça, gate alto antes de finalizar.
   - `--respond` → modo triagem. Ler a notificação recebida, avaliar (legitimidade, dever legal de cumprir, defeitos), apresentar opções, minutar memorando de triagem.
   - Sem flag → pergunte uma vez: "Estamos pedindo remoção de conteúdo ou triando uma notificação extrajudicial que recebemos?"

4. **Respeite os gates.** O **gate alto** roda antes de qualquer saída final.

5. **Nota de jurisdição.** O Marco Civil aplica-se a provedor de aplicações que atue no Brasil ou que tenha pelo menos uma operação no país (Marco Civil art. 11). Para provedor 100% estrangeiro sem operação no Brasil, a execução é internacional — sinalize. Para conteúdo que possa também ser objeto de DMCA, DSA da UE etc., considere via paralela (sem fazer disso o instrumento primário no Brasil).

6. **Handoffs.** `--respond` com recomendação de exigir ordem judicial não encadeia automaticamente — a decisão é do(a) advogado(a).

## Examples

```
/propriedade-intelectual:takedown --send
/propriedade-intelectual:takedown --respond ~/Downloads/notificacao-extrajudicial.pdf
/propriedade-intelectual:takedown
```

## Notes

- O pedido judicial (petição inicial com tutela de urgência) e a notificação extrajudicial do art. 21 **não** carregam o cabeçalho de work-product. Memorandos internos, análises de mérito e triagens carregam.
- O **art. 19 Marco Civil** exige indicação clara e específica do conteúdo (URL) na ordem judicial — vide §1º. URL genérica não basta.
- O **art. 21** (nudez/ato sexual sem consentimento) é a única hipótese de notice-and-takedown extrajudicial — o provedor é responsabilizado se, após notificado pelo participante ou representante legal, deixar de remover diligentemente.
- Para direito autoral on-line, **não há análogo ao DMCA**: o titular deve obter **tutela inibitória** (CPC art. 497) ou **tutela de urgência** (CPC art. 300) com remoção; pedidos administrativos a provedores são, em regra, atendidos por liberalidade (TOS), não por dever legal.
- Para marca, vale o mesmo: aplicação da LPI arts. 189–209 com tutela judicial; provedores podem cumprir TOS, mas não há dever legal de cumprir extrajudicialmente.
- Usuários não-advogados(as) recebem um one-pager para a conversa com advogado(a) antes do gate fechar.

---

## Purpose

A internet brasileira opera sob o **Marco Civil (Lei 12.965/2014)**. A regra constitucional-base é liberdade de expressão (CF art. 5º IV e IX), e o Marco Civil traduz isso em **art. 19**: provedores de aplicações **não respondem civilmente** por conteúdo de terceiros, salvo se, **após ordem judicial específica**, deixarem de remover dentro do prazo assinalado. **Não há notice-and-takedown extrajudicial geral.** A única exceção legal é o **art. 21**.

Essa configuração tem três consequências práticas que o usuário precisa entender:

1. **Notificação extrajudicial enviada a um provedor (sem ordem judicial) NÃO cria dever de remoção** — exceto art. 21. O provedor pode cumprir por TOS/policy, mas é liberalidade. Não invente "DMCA brasileiro" porque não existe.
2. **A remoção é, na regra, via Poder Judiciário.** A peça típica é petição inicial com pedido de tutela de urgência (CPC art. 300) ou tutela da evidência (CPC art. 311), com obrigação de fazer (remoção) sob multa cominatória (CPC art. 537).
3. **Identificação do conteúdo deve ser específica** (URL, post, identificação inequívoca — Marco Civil art. 19 §1º; STJ Tema 987 / REsp 1.660.168 e jurisprudência subsequente). Ordem com objeto genérico ("toda menção a X") tende a ser denegada.

Três modos:

- `--send` — minutar pedido judicial OU notificação art. 21
- `--respond` — triagem de notificação extrajudicial recebida; produzir opções
- (não há modo `--counter` — "contranotificação" do tipo §512(g) não existe no Brasil; a defesa de remoção indevida é processual)

Se o usuário não passar flag, pergunte uma vez.

> **Entregáveis externos (modo enviar):** a petição judicial vai ao juízo cível competente; a notificação art. 21 vai ao provedor. **NÃO** inclua o cabeçalho `CONFIDENCIAL — TRABALHO SOB ORIENTAÇÃO DE ADVOGADO(A)` na peça externa. Minutas internas, análises de mérito, briefings para aprovação seguem o cabeçalho conforme `## Outputs` do plugin.

## Pressuposto jurisdicional

O **Marco Civil da Internet** rege provedores de conexão e de aplicações que ofertem serviços ao público brasileiro (art. 11). Provedor estrangeiro sem operação no Brasil pode estar fora do alcance prático imediato — sinalize. Convenções internacionais (Berna, TRIPS) garantem direito material; execução on-line transfronteiriça depende de cooperação ou litígio internacional. Quando a infração também tocar DMCA (EUA), DSA (UE) ou regime similar, é possível instrumento paralelo na jurisdição do provedor.

## Carregar contexto

- `~/.claude/plugins/config/claude-for-legal/propriedade-intelectual/CLAUDE.md` → `## Perfil de prática PI` (registros INPI; obras autorais), `## Postura de enforcement` → linha de aprovação para remoção on-line (em regra exige medida judicial) e linha do art. 21 quando aplicável, `## Outputs` (cabeçalho de work-product, papel), `## Quem está usando` (papel — advogado(a) vs não).
- **Contexto de matéria.** Cheque `## Workspaces de matéria` no CLAUDE.md de prática. Se `Habilitado` é `✗` (in-house default), pule. Se habilitado e sem matéria ativa, pergunte. Escreva em `~/.claude/plugins/config/claude-for-legal/propriedade-intelectual/matters/<matter-slug>/takedown/<slug>/` (ou `takedown/<slug>/` em nível de prática). Nunca leia outra matéria salvo `Cross-matter context` `on`.

## Modo enviar — escolha do instrumento

### Passo 1: Triagem do tipo de violação

Antes de escolher instrumento, pergunte:

> O conteúdo a ser removido é:
> 1. **Imagem íntima sem consentimento** (nudez, ato sexual, partes íntimas do corpo) divulgada sem o consentimento da pessoa retratada?
>    → cabe **notificação extrajudicial do art. 21 Marco Civil** (única hipótese extrajudicial). Vide também Lei 13.718/2018 (criminaliza divulgação) e Lei 14.811/2024.
> 2. **Outro tipo** — uso indevido de marca, violação de direito autoral, difamação/calúnia/injúria, dado pessoal vazado, falsificação, pirataria, deepfake não-sexual, etc.?
>    → cabe **pedido judicial** (tutela de urgência ou da evidência) baseado em LPI, Lei 9.610, Marco Civil art. 19, e/ou LGPD conforme o caso.

Se a resposta for ambígua (ex.: foto que envolve marca + imagem privada), instrumente os dois trilhos.

### Passo 2a: Caminho judicial (art. 19 — regra geral)

#### Identificar o direito violado

> Qual é o direito violado? Tudo aplicável:
> - **Marca registrada no INPI** (LPI arts. 129–130, 189–195) — número do registro, classe NCL, situação no INPI.
> - **Direito autoral** (Lei 9.610/98) — obra (texto, imagem, fonograma, audiovisual, software), titularidade, eventual registro (BN/EBA/CBC/ANCINE/INPI software) — registro é facultativo, prova de anterioridade.
> - **Direito de imagem/honra/privacidade** (CF art. 5º X; CC art. 20).
> - **Dado pessoal indevidamente exposto** (LGPD art. 18 VI direito de eliminação; ANPD).
> - **Concorrência desleal / segredo de empresa** (LPI art. 195).

A petição precisa indicar fundamento legal específico — não basta "uso indevido".

#### Identificar o conteúdo e a localização

> Onde está o conteúdo?
> - **Provedor de aplicação** (rede social, marketplace, hospedagem, blog) — qual?
> - **URL(s) específicas** — Marco Civil art. 19 §1º exige identificação clara e específica.
> - **Descrição** — o que é o conteúdo e como infringe (cópia literal, similaridade marcária, exposição de imagem etc.)?
> - **Prints / evidência** preservados com data, hora e URL visível — preferencialmente com **ata notarial** (CPC art. 384) para data certa e fé pública. Cartório de Notas → e-Notariado (Provimento CNJ 100/2020).

#### Avaliar limitações e excludentes

Para direito autoral, considere antes de prosseguir:
- **Limitações da Lei 9.610 (arts. 46–48)** — uso para crítica, comentário, citação, paródia, deficiência visual, fins didáticos em pequena monta, etc. **NÃO existe "fair use" no Brasil** — as limitações são taxativas. Se a hipótese cabe em uma das limitações, a remoção é improcedente.
- **Domínio público** — prazo geral pós-mortem 70 anos (Lei 9.610 art. 41).

Para marca:
- **Uso descritivo / nominativo de boa-fé** — referência ao produto/serviço sem intuito de confundir (LPI art. 132).
- **Uso comparativo** — admissível dentro do CONAR e da boa-fé.

Para imagem:
- **Pessoa pública em local público em assunto de interesse público** — STJ relativiza o art. 20 CC.

Registre a análise. Se houver dúvida razoável sobre limitação ou excludente, **pare a peça** e roteie para revisão por advogado(a): "Há dúvida sobre limitação/excludente. Pedir remoção sobre uso protegido vira improcedência e potencial responsabilização por litigância de má-fé (CPC art. 80) ou abuso de direito (CC art. 187)."

#### Minutar a petição (estrutura mínima)

Peça típica: **Petição inicial com pedido de tutela de urgência antecipada (CPC art. 300) cumulada com obrigação de fazer (remoção) sob multa cominatória.**

Elementos:

1. **Endereçamento** — juízo cível competente. Para pessoa física, foro do domicílio do(a) autor(a) ou do dano (CDC art. 101 se houver relação de consumo; CPC art. 53). Para pessoa jurídica, regra geral CPC art. 46.
2. **Qualificação das partes** — autor(a) (com OAB do(a) procurador(a)); ré (provedor de aplicações — cite o representante legal no Brasil; provedor estrangeiro pode ser citado por carta rogatória ou via filial — vide jurisprudência STJ).
3. **Dos fatos** — relato cronológico, com URL e prints anexados.
4. **Do direito** — fundamentos: Marco Civil art. 19 (responsabilidade e ordem específica); LPI / Lei 9.610 / CC art. 20 conforme o caso; jurisprudência aplicável (STJ Tema 987 quanto a identificação específica do conteúdo; demais).
5. **Da tutela de urgência** — CPC art. 300:
   - **Probabilidade do direito** — titularidade demonstrada (certidão INPI, registro autoral, prova de uso); ilicitude evidenciada.
   - **Perigo de dano ou risco ao resultado útil** — viralização, dano à imagem, dilapidação de marca, exposição contínua.
   - **Reversibilidade** — remoção é reversível por restabelecimento.
6. **Dos pedidos** —
   - **Liminar:** determinar ao provedor a remoção das URLs específicas em prazo curto (24h a 72h), sob pena de multa diária (CPC art. 537) de R$ [valor].
   - **Mérito:** confirmar a tutela, declarar a ilicitude, condenar o réu a indenizar danos morais e materiais (se houver), preservação de logs (Marco Civil art. 15 — provedor de aplicações guarda 6 meses; art. 22 procedimento para requisição).
   - Custas, honorários, prova.
7. **Valor da causa** — pretensão econômica + danos.
8. **Documentos** — procuração, atos constitutivos, certidão INPI, prints com ata notarial, documentos de titularidade.

### Passo 2b: Caminho extrajudicial (art. 21 — nudez/ato sexual sem consentimento)

#### Pressupostos do art. 21

> Marco Civil art. 21: "O provedor de aplicações de internet que disponibilize conteúdo gerado por terceiros será responsabilizado subsidiariamente pela violação da intimidade decorrente da divulgação, sem autorização de seus participantes, de imagens, de vídeos ou de outros materiais contendo cenas de nudez ou de atos sexuais de caráter privado quando, após o recebimento de notificação pelo participante ou seu representante legal, deixar de promover, de forma diligente, no âmbito e nos limites técnicos do seu serviço, a indisponibilização desse conteúdo."

Antes de minutar, confirme:

- **A imagem/vídeo é de natureza privada** (não é cena pública, performance pública, etc.).
- **Há nudez, ato sexual ou exposição íntima** análoga.
- **Não houve consentimento** do participante para a divulgação.
- **Quem notifica é o(a) próprio(a) participante ou seu representante legal** (responsável por menor, curador(a), procurador(a) com poderes específicos).

Se algum pressuposto não estiver presente, **art. 21 não cabe** — vai para o trilho judicial (art. 19) baseado em direito de imagem/privacidade (CC art. 20; CF art. 5º X) e, eventualmente, LGPD (se houver dado pessoal sensível).

#### Estrutura da notificação extrajudicial

1. **Cabeçalho** — identificação do(a) notificante, do(a) representante legal se for o caso, advogado(a) com OAB se houver.
2. **Endereçamento** — ao representante legal da plataforma no Brasil ou ao canal oficial de denúncia indicado pelo provedor; preferencialmente protocolar via **cartório de títulos e documentos** para data certa (Lei 8.935/94).
3. **Fundamento** — Marco Civil da Internet art. 21; Lei 13.718/2018 (criminaliza divulgação de cena de sexo, nudez ou pornografia sem consentimento — CP art. 218-C); CC art. 20; CF art. 5º X.
4. **Identificação inequívoca do conteúdo** — URL(s) específicas; descrição mínima necessária; opcionalmente, hash/identificador de conteúdo se o provedor utilizar.
5. **Declaração da(o) participante** — titularidade da imagem, ausência de consentimento, identificação como pessoa retratada (ou vínculo de representação legal).
6. **Pedido** — indisponibilização diligente do conteúdo nos limites técnicos do serviço, dentro de prazo razoável (24h a 72h conforme política da plataforma).
7. **Cominação** — responsabilização subsidiária do provedor pela manutenção da violação, com perdas e danos a serem apurados em juízo, e adicionalmente perante a ANPD se houver tratamento de dado sensível em desconformidade.
8. **Sigilo** — pedido de sigilo no tratamento da notificação (a vítima pode pedir que sua identidade não seja exposta).

### Passo 3: Gate alto (antes de finalizar)

```
┌─────────────────────────────────────────────────────────────┐
│  ANTES DESTA PEÇA SAIR                                      │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  Remoção on-line no Brasil rege-se pelo Marco Civil:        │
│                                                             │
│  • Art. 19 (regra) — provedor só responde após ORDEM        │
│    JUDICIAL ESPECÍFICA descumprida. Notificação             │
│    extrajudicial NÃO obriga o provedor (salvo art. 21).     │
│    STJ Tema 987 / REsp 1.660.168 — identificação clara e    │
│    específica do conteúdo (URL).                            │
│                                                             │
│  • Art. 21 (exceção) — nudez/ato sexual sem consentimento.  │
│    NESTA hipótese a notificação extrajudicial obriga;       │
│    provedor responde subsidiariamente se não retirar.       │
│                                                             │
│  • Pedido sobre uso amparado por LIMITAÇÃO (Lei 9.610       │
│    arts. 46–48 — citação, paródia, crítica, etc.) ou        │
│    excludente (uso nominativo de marca; pessoa pública      │
│    em interesse público) tende a ser denegado e pode        │
│    configurar litigância de má-fé (CPC art. 80) ou abuso    │
│    de direito (CC art. 187).                                │
│                                                             │
│  • Marco Civil art. 19 §1º — a ordem judicial deve          │
│    indicar URL específica. Pedido genérico é denegado.      │
│                                                             │
│  Confirme antes de a peça sair:                             │
│                                                             │
│    1. Você é titular do direito (marca registrada / obra    │
│       autoral / imagem própria), ou tem mandato/legitimi-   │
│       dade para postular.                                   │
│    2. Não há limitação ou excludente que ampare o uso.      │
│    3. URL(s) específicas identificadas; prints preservados  │
│       (preferencialmente com ata notarial — CPC art. 384).  │
│    4. Para art. 21: participante ou representante legal     │
│       é a(o) notificante; ausência de consentimento clara.  │
│    5. Aprovador(a) competente da matriz aprovou.            │
│                                                             │
│  Aprovador(a) por seu perfil de prática: [linha "Pedido de  │
│  remoção on-line" ou "Notificação art. 21" da matriz]       │
│                                                             │
│  Escaladas automáticas aplicáveis: [da matriz]              │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

Se o usuário for não-advogado(a) (`## Quem está usando`), acrescente:

> Pedido judicial de remoção exige advogado(a) inscrito(a) na OAB (EOAB art. 1º). Notificação extrajudicial do art. 21 pode ser firmada pela vítima/representante legal, mas é altamente recomendável revisão por advogado(a) — incidem regras de imagem, dado sensível LGPD, e há crime correlato (CP 218-C). Pode trazer para revisão? Briefing para a conversa: [gere síntese de 1 página: titularidade, conteúdo, URL, fundamento, instrumento]. Custos profissionais agora são bem menores do que custo de pedido mal formulado. Caso precise localizar advogado(a): OAB-seccional do seu estado tem serviço de orientação; **Defensoria Pública** (vítimas de violência/imagem); **NPJs** (núcleos de prática jurídica das faculdades) atendem hipossuficientes; **MP** atua em crimes de divulgação não-consentida.

Não escreva a saída final sem engajamento explícito.

### Passo 4: Saída

**Primário:** `<matter-folder>/takedown/<slug>/peticao-v<N>.md` (caminho judicial) ou `<matter-folder>/takedown/<slug>/notificacao-art21-v<N>.md` (caminho extrajudicial).

**No chat:** preview em texto para revisão antes de gravar. Itere antes de fixar.

**Nota de fechamento para revisor(a)** (apenas no preview):

> Esta é minuta para revisão por advogado(a) inscrito(a) na OAB, não peça pronta para protocolar/notificar. A responsabilidade técnica e ética é do(a) advogado(a). Não envie/protocole sem revisão.

**Verificação de citações.** Toda citação (lei, jurisprudência) tem source-tag — `[Planalto]`, `[STJ]`, `[JusBrasil]`, `[fornecido pelo usuário]`, `[conhecimento do modelo — verificar]`. Sem suplementação silenciosa.

**Registro pós-protocolo / pós-envio.** Após protocolo da inicial ou envio da notificação, escreva `<matter-folder>/takedown/<slug>/submission.md`: instância (vara, tribunal, número do processo via PJe/eSAJ/etc.) ou provedor notificado, data, comprovantes, URLs alvo, datas de acompanhamento (prazo de cumprimento, eventual decisão liminar), preservação de logs (Marco Civil art. 15 — 6 meses para provedor de aplicações).

## Modo triagem — notificação extrajudicial recebida

Você é o(a) destinatário(a) de uma notificação extrajudicial alegando que conteúdo seu infringe direito de terceiro. **Importante:** no Brasil, em regra, o provedor que hospeda conteúdo de terceiros não está obrigado a remover por notificação extrajudicial (salvo art. 21). Se você é o **autor do conteúdo** (não o provedor), também não está obrigado a remover por uma notificação privada — pode ser pressão, pode ser legítima, e a decisão de cumprir é avaliação de risco.

### Passo 1: Leia a notificação

Extraia:

- **Notificante** — pessoa, signatário(a), advogado(a) com OAB, endereço.
- **Direito invocado** — marca, autoral, imagem, art. 21 etc.
- **Prova de titularidade alegada** — certidão INPI? registro autoral? identificação como pessoa retratada?
- **Conteúdo apontado** — URL(s) específica(s).
- **Data da notificação / prazo dado.**
- **Há ata notarial dos prints?** (data certa)
- **Foi protocolada via cartório de TD?** (data certa)
- **A notificação invoca o art. 21?** Se sim, tem peso legal (cumprimento obriga o provedor se cabíveis os pressupostos). Se não, é "carta amigável".

### Passo 2: Avaliar

- **O direito invocado existe e é titularizado pelo notificante?** Cheque INPI, base de obras, etc.
- **A conduta apontada cabe em alguma limitação ou excludente?** Lei 9.610 arts. 46–48 (autoral), LPI art. 132 (marca — uso descritivo, nominativo), CC art. 20 (imagem — interesse público), Marco Civil art. 19 §2º (jornalismo).
- **A notificação tem defeitos?** Falta de identificação do conteúdo, falta de fundamento, sem titularidade demonstrada, assinatura sem poderes.
- **Estamos diante do art. 21?** Se sim, decisão diferente (provedor responde subsidiariamente — diligência obrigatória).
- **Quem é o notificante?** Reincidente em notificações abusivas? Estratégia de censura via SLAPP/troll de marca?

### Passo 3: Opções

Apresente 4 com tradeoffs:

**A — Cumprir (remover)**
- Quando: titularidade clara, sem limitação aplicável, conteúdo de baixo valor, custo de litígio > custo de remover.
- Tradeoff: conteúdo sai; se for canal/site, pode afetar SEO/audiência; pode reforçar prática abusiva do notificante.
- Próximo passo: registrar evento, eliminar; opcional, contraposta amigável reservando direitos.

**B — Não cumprir e exigir ordem judicial**
- Quando: titularidade duvidosa, uso amparado por limitação, notificação defeituosa, ou estratégia de transferir ônus para o juízo.
- Tradeoff: conteúdo permanece; notificante pode (1) ajuizar, (2) abandonar, (3) escalar publicamente. Aqui você diz: "no Brasil, salvo art. 21, ordem judicial é exigida — apresente." Esse é o regime do Marco Civil art. 19.
- Próximo passo: resposta extrajudicial educada, reservando direito de defesa, citando Marco Civil art. 19.

**C — Engajar o notificante**
- Quando: há margem para acordo (licença retroativa, crédito, redução do escopo).
- Tradeoff: tempo, exposição ao notificante; sigilo das tratativas é apenas conciliatório (CPC art. 166 §3º limitado a mediação/conciliação formal).
- Próximo passo: carta-resposta propondo conversa; **não confunda** com cumprimento.

**D — Ignorar**
- Quando: notificação manifestamente abusiva, sem titularidade, sem fundamento, e custo de resposta > risco.
- Tradeoff: notificante pode ajuizar; silêncio pode ser usado como elemento (não conclusivo) de má-fé.

Recomende uma com duas frases de justificativa.

### Passo 4: Minutar memorando de triagem

Saída: `<matter-folder>/takedown/inbound/<slug>/triagem.md`.

```markdown
[CABEÇALHO DE WORK-PRODUCT — conforme config do plugin ## Outputs]

> **Herança de sigilo.** Esta triagem registra primeira avaliação de notificação adversa. É material sob sigilo profissional / preparatório. Não encaminhar fora do círculo profissional sem expurgo.

# Notificação Extrajudicial Recebida — Triagem

> **LEITURA PARA TRIAGEM, NÃO PARECER.** Levantamento estruturado, não opinião de mérito. Toda autoridade sinalizada para verificação SME; toda decisão de mérito é do(a) advogado(a) responsável.

**Slug:** [slug]
**Recebida:** [YYYY-MM-DD]
**Provedor / canal:** [se a notificação foi recebida por um provedor que hospeda nosso conteúdo, identifique; ou recebida diretamente por nós]
**Arquivo de entrada:** [caminho]

## A notificação

**Notificante:** [pessoa, signatário(a), OAB, escritório]
**Direito invocado:** [marca / autoral / imagem / art. 21 / outro]
**Prova de titularidade alegada:** [certidão INPI / registro autoral / declaração / nenhuma]
**Conteúdo apontado:** [URLs / descrição]
**Data da notificação:** [YYYY-MM-DD]
**Prazo dado:** [se houver]
**Há ata notarial dos prints?** [sim/não]
**Protocolada via cartório de TD?** [sim/não]
**Invoca art. 21 Marco Civil?** [sim/não]
**Defeitos aparentes:** [lista ou nenhum]

## Avaliação

**Direito invocado existe e é do notificante?** [análise]
**Conduta amparada por limitação / excludente?** [Lei 9.610 46–48 / LPI 132 / CC 20 / Marco Civil 19§2º — `[review]`]
**Há dever legal de cumprir (art. 21 cabível)?** [sim/não — análise]
**Notificante reincidente / padrão de abuso?** [análise]

## Opções

### A. Cumprir
### B. Não cumprir e exigir ordem judicial (Marco Civil art. 19)
### C. Engajar o notificante
### D. Ignorar

**Recomendação:** [A/B/C/D] — [duas frases — `[review]`: confirmação do(a) advogado(a) antes de executar]

## Prazos

- **Prazo da notificação:** [se houver]
- **Janela de exposição a ajuizamento contra nós:** dependendo do direito invocado (LPI tem prazo prescricional de 5 anos para reparação — art. 225; autoral 3 anos CC art. 206 §3º V; imagem CC 206 §3º V; art. 21 — diligência imediata sob risco subsidiário)
- **Marco Civil art. 15 — preservação de logs:** verificar se há requisição válida de logs a preservar (6 meses para aplicação; 1 ano para conexão — art. 13)

## Ações imediatas

- [ ] Preservação documental do conteúdo, prints, logs internos — [sim/não]
- [ ] Avaliação de impacto operacional (SEO, contas, faturamento) — [sim/não]
- [ ] Matéria registrada no log — [sim/não/a fazer]
- [ ] Advogado(a) atribuído(a) — [quem]
```

Encerre o preview com:

> Esta é triagem, não parecer. As avaliações acima são primeira leitura. Advogado(a) inscrito(a) na OAB decide antes de cumprir ou de exigir ordem judicial.

## Postura de decisão

Conforme `## Postura de decisão em juízos jurídicos subjetivos` no perfil de prática: quando incerto sobre limitação/excludente, sobre titularidade, sobre cabimento do art. 21 — **não decida silenciosamente**. Limitações da Lei 9.610 são tema clássico de incerteza. Sinalize para advogado(a); levante os fatores. Pedir remoção de uso protegido por limitação é porta de uma via — vira improcedência e potencial litigância de má-fé.

## O que esta skill NÃO faz

- **Protocolar peça em juízo.** Apenas minuta. Protocolo é via PJe/eSAJ pelo(a) advogado(a) constituído(a).
- **Enviar notificação ao provedor.** Apenas minuta; envio é decisão do(a) titular/advogado(a) (preferencialmente cartório de TD para data certa).
- **Decidir limitação/excludente autoralmente.** Sinaliza fatores; advogado(a) decide.
- **Validar a alegação do notificante (modo triagem).** Leitura estruturada; toda autoridade sinalizada.
- **Atalhar o gate.** O gate alto roda em todo `--send`.
- **Inventar citações.** Source-tag obrigatória; sem suplementação silenciosa.
- **Lidar com regimes não-brasileiros.** Marco Civil é nacional. Para DMCA (EUA), DSA (UE), regimes asiáticos — sinalize e encaminhe.
- **Minutar "contranotificação DMCA".** Não existe instituto análogo no Brasil. Defesa contra remoção é processual (contestação à inicial, agravo de instrumento) ou contratual (com o provedor que removeu por TOS).
