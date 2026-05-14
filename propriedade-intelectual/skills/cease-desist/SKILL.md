---
name: cease-desist
description: >
  Minutar notificação extrajudicial (modo enviar) ou triar uma que você
  recebeu (modo receber). Use para constituir em mora e formalizar
  oposição a infrator(a) com peça calibrada à postura de enforcement, ou
  quando notificação extrajudicial recebida exigir triagem em memorando
  de opções com recomendação.
argument-hint: "<--send | --receive> [contexto, contraparte ou caminho da notificação recebida]"
---

# /cease-desist

> **Nota de adaptação BR.** Adaptado da versão original norte-americana (cease-and-desist + ameaças de declaratory judgment / TTAB). No Brasil, o equivalente funcional é a **notificação extrajudicial** — preferencialmente protocolada via **cartório de títulos e documentos** para data certa (Lei 8.935/94) — que constitui o(a) infrator(a) em mora (CC art. 397 par. único), interrompe a prescrição em algumas hipóteses (CC art. 202 II), e cria o registro probatório para futura ação. O instrumento ofensivo equivalente ao "DJ action" é a **ação declaratória de inexistência de infração** ou **ação de nulidade**.

Dois modos. Escolha um:

- `/propriedade-intelectual:cease-desist --send` — minutar notificação extrajudicial calibrada à postura de enforcement. Gate alto roda antes de finalizar.
- `/propriedade-intelectual:cease-desist --receive` — triar notificação extrajudicial que você recebeu. Produz memorando de opções com recomendação.

## Instructions

1. **Ler o perfil de prática.** Carregue `~/.claude/plugins/config/claude-for-legal/propriedade-intelectual/CLAUDE.md`. Se contiver `[PLACEHOLDER]` ou não existir, pare: "O plugin precisa de setup. Rode `/propriedade-intelectual:cold-start-interview` — esta skill depende da sua postura de enforcement, matriz de aprovação e mix de áreas."

2. **Checar matter workspaces.** Se `Habilitado` for `✗`, pule. Se habilitado sem matéria ativa, pergunte: "Qual matéria? Rode `/propriedade-intelectual:matter-workspace switch <slug>` ou diga `practice-level`."

3. **Dispatch em `$ARGUMENTS`:**
   - `--send` → modo enviar. Identificar direito, conduta, relação, demanda, calibrar à postura, minutar, gate.
   - `--receive` → modo receber. Pedir a notificação, avaliar, identificar exposição, apresentar opções, gravar memorando.
   - Sem flag → pergunte uma vez: "Estamos enviando notificação extrajudicial (afirmação) ou triando uma recebida (defesa)?"

4. **Respeite o gate.** Em modo enviar, o gate alto roda antes de qualquer minuta final ir a disco.

5. **Respeite a matriz de aprovação.** Puxe o(a) aprovador(a) da linha "Notificação extrajudicial (C&D)" em `## Postura de enforcement → Aprovação`. Surface também as escaladas automáticas.

6. **Handoffs.** Em modo receber, se a recomendação for "responder firmemente", ofereça encadear para `/propriedade-intelectual:cease-desist --send` pré-populado. Se for "preemptar com ação declaratória ou pedido de nulidade no INPI / JF", escale para escritório externo (linha "Contencioso de PI" do perfil) — **não** minute.

## Examples

```
/propriedade-intelectual:cease-desist --send
/propriedade-intelectual:cease-desist --receive ~/Downloads/notificacao-acme.pdf
/propriedade-intelectual:cease-desist
```

## Notes

- A notificação extrajudicial enviada **não** carrega o cabeçalho de work-product. Minuta interna, brief para aprovação e memorando de triagem carregam.
- Direito de marca é territorial (LPI; Convenção de Paris); o draft assume as jurisdições do `Registrado em:` do perfil de prática. Se a conduta ou contraparte está fora, sinalize.
- Toda citação `[CITAR:___]` é não-verificada até confirmação em base oficial (Planalto, INPI, STJ).
- Usuários não-advogados(as) recebem one-pager para conversa com advogado(a) antes do gate fechar.
- **Protocolo recomendado**: registrar a notificação em **cartório de TD** (Lei 8.935/94 art. 5º) garante data certa e fé pública — economiza prova futura. Notificação por e-mail simples vale (CC art. 397; CPC art. 274), mas dá margem a discussão probatória.

---

## Purpose

A notificação extrajudicial brasileira:

- **Constitui o(a) devedor(a) em mora** quando há dever ilíquido ou de fazer/não-fazer (CC art. 397 par. único).
- **Comunica oposição formal** ao uso indevido — substrato fático para ações de abstenção (LPI arts. 207–209), inibitórias (CPC art. 497) e indenizatórias.
- **Documenta a má-fé subsequente** do(a) infrator(a) que, ciente, continua a infração.
- **Em alguns casos, interrompe a prescrição** (CC art. 202 II) se acompanhada de protocolo idôneo.

Não é petição inicial, não é decisão. Não obriga o(a) destinatário(a) a nada por força própria — a obrigatoriedade vem da lei material que fundamenta o pedido. Mas é peça consequente: cria gancho probatório, ativa o(a) infrator(a) a buscar defesa preventiva (ação declaratória de inexistência de infração — CPC art. 19 I), e pode atrair litigância de má-fé se for amparada em direito inexistente ou usada para coação (CC art. 187 abuso de direito; CPC art. 80).

Dois modos:

- `--send` — você afirma. Minute, calibre à postura, gate antes de enviar.
- `--receive` — você defende. Triagem, opções, memorando, eventual encaminhamento.

Sem flag, pergunte.

> **Entregável externo (modo enviar):** a notificação vai à contraparte. **NÃO** inclua cabeçalho `CONFIDENCIAL — TRABALHO SOB ORIENTAÇÃO DE ADVOGADO(A)` no documento que sai. Minuta interna, brief pré-envio e memorando de triagem mantêm o cabeçalho conforme `## Outputs`.

## Pressuposto jurisdicional

Direito de marca é territorial (LPI; Convenção de Paris art. 6º bis para marcas notórias; **Protocolo de Madri — Brasil aderente desde 2019, Decreto 10.033/2019**). Direito autoral é multilateral via Berna (Brasil é parte; Decreto 75.699/1975), mas execução é territorial — Lei 9.610/98 rege fato cometido no Brasil. Esta skill assume o **`Registrado em:`** do perfil. Se a conduta ou contraparte está fora do Brasil, **sinalize** — pode caber notificação em outra jurisdição (USPTO/EUIPO/UKIPO etc.), ou via correspondente estrangeiro/Madrid.

## Carregar contexto

- `~/.claude/plugins/config/claude-for-legal/propriedade-intelectual/CLAUDE.md` → `## Postura de enforcement` (postura, gatilhos de notificação, critérios de carta amena, matriz de aprovação, escaladas automáticas), `## Perfil de prática PI` (mix de áreas, jurisdições registradas, escritórios externos), `## Outputs` (cabeçalho, papel), `## Quem está usando`.
- Eventual template ou playbook citado no perfil — leia e respeite a estrutura.
- **Contexto de matéria.** Conforme `## Workspaces de matéria`. Saídas em `~/.claude/plugins/config/claude-for-legal/propriedade-intelectual/matters/<matter-slug>/` ou nível de prática.

## Modo enviar — minutar a notificação

### Passo 1: Identificar o direito

Pergunte em bloco:

> Qual direito de PI estamos invocando?
>
> - **Marca** — registrada no INPI? Número de registro, classe(s) NCL (Nice), situação atual? Marca de alto renome ou notória (LPI art. 125; art. 126; Convenção de Paris art. 6º bis)? Ou só uso anterior comum/empresarial sem registro (proteção mais restrita — LPI art. 129 §1º, "usuário anterior de boa-fé")?
> - **Patente / modelo de utilidade / desenho industrial** — número de carta-patente / certificado, situação (anuidades em dia?), reivindicações (claims). Patente vigente — atenção ao prazo: PI 20 anos do depósito (LPI art. 40 — **STF declarou inconstitucional o parágrafo único do art. 40 na ADI 5529**, fim do prazo de 10 anos da concessão como mínimo); MU 15 anos; DI 10 + 3 períodos de 5 anos.
> - **Direito autoral** — obra (texto, software, imagem, audiovisual, fonograma), titularidade (originária ou cessão registrada?), eventual registro (BN/EBA/CBC/ANCINE/INPI software) — registro é facultativo no Brasil (Lei 9.610 art. 18; Lei 9.609 art. 3º).
> - **Software** — Lei 9.609/98 (regime autoral, **não** patentário); registro facultativo no INPI; titularidade — vínculo empregatício (Lei 9.609 art. 4º): empregador é titular se relacionado às funções; encomenda (art. 4º §2º): cessionário é titular se constar do contrato.
> - **Segredo de empresa** — LPI art. 195 XI e XII (concorrência desleal). Não há registro; o que protege é o sigilo + as medidas razoáveis de proteção.
> - **Conjunto-imagem (trade dress)** — STJ reconhece como repressão à concorrência desleal (LPI art. 195 III) e ao parasitismo; não há registro, prova é fato.

Anote cada direito. Direitos registrados são citados por número (LPI art. 129; art. 41). Direitos de uso anterior recebem parágrafo de evidência (data, território, prova). Obras sem registro recebem ressalva: "Obra sem registro — proteção independe de registro (Lei 9.610 art. 18); registro facilita prova de anterioridade `[review]`."

### Passo 2: Identificar a conduta

> Descreva a conduta infratora em concreto, sem adjetivos:
>
> - **Quem** — pessoa, empresa, plataforma (use razão social, CNPJ se possível).
> - **O quê** — a marca, cópia, produto acusado. Anexe ou descreva amostras.
> - **Onde** — URL, listagem de marketplace, varejo físico, redes sociais, embalagem.
> - **Desde quando** — data da primeira observação; data mais antiga documentável.
> - **Evidência** — prints (preferencialmente com **ata notarial** — CPC art. 384), recibos, hits de watch service, relatos de confusão de consumidor.

Específico ganha de adjetivo. "Você comercializou o produto X em [URL] usando a marca [Y] em [data]" supera "Você está infringindo nossos direitos."

### Passo 3: Identificar a relação

> Qual é a relação entre nós e o(a) destinatário(a)?
>
> - **Concorrente** (direto ou adjacente) — postura padrão.
> - **Distribuidor / parceiro de canal** — tom ajusta; considerar carta amena.
> - **Ex-licenciado(a) / ex-empregado(a) / ex-sócio(a)** — cláusula contratual provavelmente aplicável; citar. Inventor empregado (CLT) ou contratado: LPI arts. 88–93 + CLT arts. 88–93 — atenção ao regime de invenção de serviço, livre e comum.
> - **Estranho / infrator(a) avulso(a)** — padrão.
> - **Cliente / parceiro atual** — escalada automática por perfil de prática; sinalize antes de minutar.

Muda tom, aprovador(a) e se vale a pena minutar antes de escalada.

### Passo 4: Identificar a demanda

> O que o(a) cliente realmente quer?
>
> - **Cessar** o uso infrator.
> - **Prestação de contas** — declarar vendas, lucros, volumes (base para futura indenização — LPI art. 210; Lei 9.610 art. 102 ss.).
> - **Destruir / recolher** estoque infrator (LPI art. 209).
> - **Indenização** — perdas e danos (LPI art. 208; Lei 9.610 art. 102; CC arts. 944 ss.); inclui danos morais quando cabíveis (STJ admite — Súmula 388/STJ por analogia em direitos de personalidade; em PI, dano moral exige prova).
> - **Transferência** — cessão de domínio (.com.br via Saci-Adm/CGI; .br variantes), transferência de conta, cessão de direito.
> - **Retratação pública** — retirada de conteúdo, nota de esclarecimento (raramente concedida administrativamente — em regra exige decisão judicial).
> - **Confirmação por escrito** — compromisso de não-repetição com prazo, multa moratória.

Escolha remédios concretos. Demanda desproporcional vira evidência de má-fé / abuso de direito (CC art. 187) se a matéria for judicializada.

**Trilhos paralelos em marketplaces (infração on-line).** Se a conduta acusada está em marketplace (Mercado Livre, Amazon BR, Magalu, AliExpress, Shopee, Etsy, Shopify), sinalize o canal de denúncia da plataforma como **trilho paralelo** mais rápido — não substitui notificação/ação, complementa:

- **Mercado Livre — BPP / Programa de Proteção à Propriedade Intelectual**
- **Amazon BR — Brand Registry / Report Infringement**
- **Magalu / Magazine Luiza Marketplace — canal IP**
- **Shopee — IPR / formulário de denúncia**
- **AliExpress — IPP Platform**
- **TikTok Shop — IP Protection**
- **Etsy — IP form**
- **Shopify — formulário de marca / direito autoral**

Plataformas costumam atender denúncias em dias quando o pedido está bem-documentado (certidão INPI atualizada, listagens específicas). Trilho paralelo + notificação à própria infratora cobre on e off platform. Anote no brief pré-envio se o trilho paralelo foi acionado.

### Passo 5: Calibrar à postura

Leia `## Postura de enforcement → Postura padrão` e aplique:

- **Agressiva** — notificação firme, prazo curto (em regra 5–10 dias úteis), linguagem de consequência explícita (ação de abstenção com tutela de urgência, multa cominatória, indenização, busca e apreensão LPI art. 200).
- **Equilibrada** — firme mas profissional, prazo padrão (10–20 dias úteis), consequências noteadas sem teatro, abertura para diálogo se respondida.
- **Conservadora** — carta amena, prazo mais elástico ou sem deadline duro, "gostaríamos de conversar" como abertura, consequências discretas.

Também leia "Quando enviamos notificação extrajudicial", "Quando enviamos carta amena primeiro", "Quando ajuizamos direto". Se os fatos sugerem carta amena ou ajuizamento direto, sinalize: "Pela sua postura, este padrão casa com [carta amena / ajuizamento]. Ainda quer C&D ou prefere [alternativa]?"

Overrides em `matter.md` prevalecem sobre o padrão da prática.

### Passo 5.5: Due diligence da contraparte — PRÉ-REQUISITO

**Antes de minutar, rode DD da contraparte e mostre os achados ao(à) usuário(a).** Não é condicional ao "tamanho aparente". Toda C&D carrega risco de (a) ação declaratória de inexistência de infração (CPC art. 19 I) em foro escolhido pela ré, (b) reconvenção em ação que venhamos a ajuizar, (c) responsabilização por abuso (CC art. 187) ou litigância de má-fé (CPC art. 80) calibrado a *quem* é o(a) destinatário(a).

Coletar e apresentar — em bloco, para sign-off — antes de minutar:

- **Pessoa jurídica** — razão social exata, CNPJ, JUCESP/JUCERJA/Junta Comercial estadual de constituição, nomes fantasia. Cadastros: receita.fazenda.gov.br (CNPJ), JUCESP, busca de domínio no Registro.br, certidão simplificada da junta. Sinalize `[verificar]` se a fonte é incerta.
- **Porte e recursos** — headcount aproximado, faixa de faturamento (se pública), parent company se subsidiária. LinkedIn, imprensa, CVM se aberta. Sinalize honestamente quando porte não é determinável.
- **Portfólio de PI próprio** — eles têm marcas/patentes registradas no INPI em classes adjacentes? Contraparte com PI própria tende a (a) entender a postura, (b) contra-atacar, (c) ajuizar declaratória. Busca rápida no e-INPI (busca.inpi.gov.br).
- **Histórico de litigância** — JusBrasil / Escavador / consulta processual em TJ/TRF estaduais para casos de PI como autora ou ré. Litigante reincidente muda cálculo. Sinalize campanhas de C&D anteriores no setor.
- **Advogado(a)** — escritório/advogado(a) de PI conhecido(a) atuando para eles em peças anteriores? "Sem advogado(a) identificado(a)" é dado.
- **Risco de ação declaratória** — dado porte, portfólio, histórico, advogado(a) e foro: contraparte tende a receber a notificação como convite para ajuizar declaratória em foro favorável (geralmente sede da ré)? Marque alto / médio / baixo + frase de razão.
- **Risco de relacionamento** — somos cliente deles? compartilhamos investidor(es)? potenciais parceiros ou adquirentes? "Não somos cliente" confirmado no perfil; o resto sinalizado.

Apresente como memorando curto no chat ANTES de minutar:

```
## DD contraparte — [Razão social]

- **Pessoa jurídica:** [nome, CNPJ, estado de constituição, parent se houver]
- **Porte:** [headcount, faixa receita, estágio] — [fonte, `[verificar]` onde aplicável]
- **Portfólio PI:** [marcas/patentes em classes adjacentes — ou "nada encontrado"]
- **Histórico:** [casos PI prévios como autora/ré — ou "nada no levantamento rápido"]
- **Advogado(a):** [escritório/profissional conhecido — ou "não identificado"]
- **Risco de declaratória:** [alto/médio/baixo — razão]
- **Risco de relacionamento:** [overlaps — ou "nenhum"]

**Escaladas automáticas disparadas** (perfil de prática `## Postura de enforcement` → Escaladas automáticas):
- [cada gatilho que esta DD surfaceia]

**Confirme antes de eu minutar:**
- Quer prosseguir com notificação contra esta contraparte, dada a DD acima?
- Alguma escalada automática aplicável? Se sim, aprovador(a) nomeado(a) no perfil aprova ANTES da minuta, não depois.
```

**Não avance para o Passo 6 sem engajamento explícito com a DD.** "Ok" vazio é pior que sem confirmação — empurre: "Antes de eu minutar — algo na DD que muda o cálculo? Porte, histórico, advogado(a), relacionamento?"

Se a DD surfaceia gatilho de escalada automática (cliente, contraparte maior, matéria de patente, com risco de imprensa, etc.), encaminhe ao(à) aprovador(a) do perfil — não minute em nome do(a) revisor(a) sem a aprovação prévia.

Se itens críticos não puderem ser respondidos: "Não consegui confirmar [item] em fontes disponíveis. Você tem? Ou pausamos até alguém rodar a confirmação?"

### Passo 6: Minutar

Estrutura:

1. **Emitente / papel timbrado e data**.
2. **Endereçamento à destinatária** — razão social, CNPJ, endereço, A/C representante legal.
3. **Linha "Assunto"** — concisa, sem revelar estratégia. `Assunto: Notificação extrajudicial — uso indevido da marca [X] (Reg. INPI nº [•])`.
4. **Abertura** — identifica emitente, direito, registro (se houver), propósito da peça.
5. **O direito** — marca: nº reg. INPI, classe NCL, data de depósito/concessão, situação; patente: nº carta-patente, data de concessão, anuidades em dia; autoral: registro (se houver), descrição da obra, ano; uso anterior: data, território, prova de distintividade adquirida; software: nº registro INPI ou declaração de autoria + vínculo (Lei 9.609).
6. **A conduta infratora** — específica: quem, o quê, onde, quando, evidência.
7. **A base legal** — `[CITAR: LPI art. 189 (crimes contra marca registrada) / LPI art. 195 (concorrência desleal) / Lei 9.610 art. 102 ss. / Lei 9.609 art. 12 / CC art. 187 (abuso de direito) / Marco Civil art. 19 se for on-line]` conforme aplicável.
8. **A demanda** — numerada, específica, proporcional.
9. **O prazo** — data calendário, meio de confirmação (carta com AR / e-mail com leitura confirmada / protocolo em cartório).
10. **Consequências do descumprimento** — calibradas à postura: ação de abstenção (LPI art. 209) com tutela de urgência (CPC art. 300), multa cominatória (CPC art. 537), busca e apreensão (LPI art. 200), perdas e danos (LPI art. 210; Lei 9.610 art. 102), apuração criminal quando cabível (LPI arts. 183–195; Lei 9.610 art. 184 CP).
11. **Pedido de preservação** — documentos, comunicações, metadados, registros contábeis relacionados ao uso acusado (Marco Civil art. 15 se on-line; CC art. 226 escrituração contábil).
12. **Reserva de direitos** — "sem prejuízo de quaisquer direitos, ações ou medidas que se mostrem necessárias".
13. **Assinatura** — aprovador(a) por perfil de prática (advogado(a) inscrito(a) na OAB, com nº de inscrição).

**Regras de minuta:**

- **Especificidade vence adjetivo.** Datas, URLs, números INPI, amostras.
- **Sem afirmação overbroad.** Se a marca está registrada em uma classe e o uso acusado é em outra, diga — não finja que o registro cobre tudo. Afirmação overbroad é evidência de má-fé e pode atrair art. 187 CC + 80 CPC.
- **Citações como placeholder até verificação.** `[CITAR: LPI art. 189]` fica como placeholder até confirmação. Tag de fonte em toda citação: `[Planalto]`, `[INPI]`, `[STJ]`, `[fornecido pelo usuário]`, `[conhecimento do modelo — verificar]`. Não retire as tags.
- **Linguagem de consequência casa com postura.** Agressiva → consequências específicas (ação de abstenção com tutela de urgência LPI 209 + CPC 300, multa cominatória, indenização LPI 210). Equilibrada → "reservados todos os direitos". Conservadora → "gostaríamos de conversar antes de qualquer outra medida".
- **Sigilo de tratativas.** Se houver intenção de compor: o sigilo das tratativas no Brasil é limitado — vale para **mediação/conciliação formal** (CPC art. 166 §3º; Lei 13.140/2015 art. 30). Em negociação extrajudicial pura, não há "FRE 408" — o que se diz pode ser usado. Diga isso explicitamente.

### Passo 7: Gate alto antes do envio

```
┌─────────────────────────────────────────────────────────────┐
│  ANTES DESTA MINUTA SAIR                                    │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  Esta é minuta para revisão por advogado(a) — não peça      │
│  pronta para envio. Notificação extrajudicial é afirmação   │
│  de direitos com consequências reais:                       │
│                                                             │
│  • Pode disparar AÇÃO DECLARATÓRIA DE INEXISTÊNCIA DE       │
│    INFRAÇÃO (CPC art. 19 I) em foro escolhido pela ré       │
│    (em regra, sede da ré — CPC art. 46). Contraparte        │
│    bem-recursada pode usar a notificação como convite       │
│    para escolher foro hostil.                               │
│                                                             │
│  • Afirmação overbroad ou de má-fé é abuso de direito       │
│    (CC art. 187), litigância de má-fé (CPC art. 80),        │
│    e em alguns casos crime contra a ordem econômica         │
│    quando há intuito anticompetitivo (Lei 12.529/11).       │
│                                                             │
│  • Inicia conflito que pode não se compor em curto prazo.   │
│                                                             │
│  Confirme antes de a notificação sair:                      │
│                                                             │
│    1. Os direitos invocados são válidos — registrados       │
│       (puxados do INPI, não presumidos) ou de uso anterior  │
│       com evidência sólida de distintividade.               │
│    2. A pretensão é amparável — advogado(a) razoável        │
│       sustentaria nestes fatos.                             │
│    3. A demanda é proporcional — pedimos remédio adequado   │
│       à conduta, não tudo.                                  │
│    4. Quem tem poder para iniciar conflito aprovou.         │
│    5. DD da contraparte (Passo 5.5) apresentada e           │
│       confirmada — pessoa jurídica, porte, portfólio PI,    │
│       histórico, advogado(a), risco de declaratória,        │
│       relacionamento. Não condicional. Obrigatório.         │
│                                                             │
│  Aprovador(a) por perfil: [linha "Notificação extrajudi-    │
│  cial (C&D)" da matriz]                                     │
│                                                             │
│  Escaladas automáticas aplicáveis: [da matriz —             │
│  surfaceadas no Passo 5.5]                                  │
│                                                             │
│  Status do trilho paralelo (marketplace): [acionado /       │
│  na fila / não aplicável — Passo 4]                         │
│                                                             │
│  Protocolo: recomenda-se cartório de TD para data certa     │
│  (Lei 8.935/94) ou e-Notariado (Provimento CNJ 100/2020).   │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

Se o usuário for não-advogado(a) (`## Quem está usando`), acrescente:

> Envio de notificação extrajudicial tem consequência jurídica além da reação da contraparte — é afirmação positiva de direitos que pode voltar contra você. Revisou com advogado(a) inscrito(a) na OAB? Se não, segue brief para a conversa: [síntese de 1 página: partes, direitos invocados, conduta, demanda, postura, riscos sinalizados acima, o que pode dar errado, perguntas para o(a) advogado(a)].
>
> Caso precise localizar advogado(a): OAB-seccional do seu estado tem **Serviço de Orientação Jurídica**; OAB seccionais e associações têm comissões de PI (CESA, ABPI — Associação Brasileira da Propriedade Intelectual); para hipossuficientes, **Defensoria Pública** (não atua em PI corporativa, mas em imagem/autoral de pessoa física, sim) e **NPJs** (núcleos de prática jurídica das faculdades).

Não escreva o .docx ou marque a minuta como pronta sem engajamento explícito com o gate.

### Passo 8: Saída

**Primário:** `<matter-folder>/cease-desist/<slug>/minuta-v<N>.docx`. Use a skill `docx`. Formato de carta conforme estrutura acima. Retire o cabeçalho de work-product do documento que sai.

**No chat:** mostre a minuta em texto para revisão antes de gerar .docx. Itere antes de fixar.

**Nota de fechamento para revisor(a)** (apenas preview, não vai no .docx):

> Esta é minuta para revisão por advogado(a) inscrito(a) na OAB. Envio é afirmação de direitos com consequências descritas no gate. A responsabilidade técnica e ética pelo envio é do(a) advogado(a) revisor(a). Não envie sem revisão.

**Verificação de citações.** Todo `[CITAR:___]` e toda citação trazida de template é **não-verificada** até confirmação em fonte primária (Planalto, INPI, STJ). Antes do envio, confirme cada citação. Citação fabricada ou mal-quotada em peça enviada é responsabilidade ética (EOAB; CED-OAB). Preserve as tags de fonte — `[Planalto]`, `[INPI]`, `[STJ]`, `[JusBrasil]`, `[fornecido pelo usuário]`, `[conhecimento do modelo — verificar]`. Tags `verificar` checadas primeiro.

**Sem suplementação silenciosa.** Se a ferramenta de pesquisa configurada retornar pouco ou nada para autoridade necessária, reporte e pare. NÃO complete por web ou conhecimento do modelo sem perguntar.

**Checklist pós-envio.** Após aprovação, escreva `<matter-folder>/cease-desist/<slug>/checklist.md`: leitura final pelo(a) aprovador(a), todo `[verificar]` resolvido, todo `[CITAR]` preenchido e verificado, marcações de sigilo removidas do documento externo, aprovador(a) assinou, meio de entrega executado (AR / cartório de TD / e-mail com leitura), comprovante retido, prazo de cumprimento calendarizado, plano de escalada se sem resposta, matéria criada se for o caso.

## Modo receber — triar a notificação recebida

### Passo 1: Ler a notificação

Extraia:

- **Notificante** — pessoa, signatário(a), escritório, OAB.
- **Destinatário(a)** — qual de nossas pessoas/empresas.
- **Meio e data de entrega**.
- **Direito invocado** — marca (nº INPI? classe? jurisdição?), patente (carta-patente), autoral (registrado? título?), software (Lei 9.609), conjunto-imagem (LPI 195 III), segredo (LPI 195 XI/XII), outro.
- **Conduta alegada** — versão deles.
- **Base legal** — leis, contratos, teorias citadas.
- **Demanda** — o que querem; há prazo?
- **Ameaças** — o que dizem que farão.
- **Tom** — firme / amena / escorched-earth; assinatura por advogado(a) com OAB usualmente sinaliza seriedade.
- **Protocolo** — veio por cartório de TD (data certa)? AR? e-mail?

### Passo 2: Avaliar a afirmação

Não é parecer — leitura estruturada:

- **Validade dos direitos.** As certidões INPI invocadas são reais e ativas? (Confirme em busca.inpi.gov.br; veja anuidades, oposição pendente, processo administrativo de nulidade.) Marca caducada por desuso (LPI art. 143)? Para uso anterior, que evidência efetivamente citam?
- **Plausibilidade.** Nos fatos como alegados, é pretensão amparável ou está esticando? Marca: critérios STJ de colidência — semelhança gráfica/fonética/ideológica + afinidade de produtos/serviços. Autoral: acesso + similaridade substancial. Patente: literal/equivalentes (LPI art. 41 — reivindicações). Sinalize onde a afirmação é mais frágil.
- **Overbreadth.** Demandam mais do que a conduta autoriza? (Querem cessão da marca quando registro só permitiria coexistência? Querem todas as vendas quando um canal sequer tocou o direito?) Demanda overbroad enfraquece e fortalece contra-pretensão por abuso (CC 187) e litigância de má-fé (CPC 80).
- **Prazos.** Prescrição (LPI art. 225 — 5 anos para reparação por infração; CC art. 206 §3º V — 3 anos para reparação civil em outras hipóteses; Lei 9.610 — 3 anos CC §3º V), decadência, eventual usucapião de marca não usada. Sinalize.
- **Foro.** Onde provavelmente ajuizariam? Foro contratual fixado (raro em PI estranha)? Cabe ação declaratória nossa em foro mais favorável (CPC art. 19 I)?

### Passo 3: Avaliar nossa exposição

- **Estamos efetivamente infringindo?** Olhar honesto. O que o registro mostra?
- **Conseguimos parar facilmente?** Custo de cumprir × custo de brigar.
- **Notificante é troll ou reivindicante real?** Reincidente? Conhecido por ajuizar? Campanha recente no setor? Consulta processual rápida em TJ/TRF se houver tempo.
- **O que está em jogo além desta disputa?** Brand equity, relações com cliente, precedente para futuras notificações.

### Passo 4: Opções

Apresente 4–5 com tradeoffs:

**A — Cumprir rapidamente**
- Quando: pretensão amparável, cumprir é barato, brigar não compensa.
- Tradeoff: cria registro de concessão que pode ser usado depois; pode encorajar futuras afirmações.
- Próximo passo: confirmar cumprimento por escrito (restrito), sem conceder a teoria mais ampla.

**B — Negociar**
- Quando: há meio-termo de negócio (licença, coexistência, cronograma de rebranding) que resolve.
- Tradeoff: tempo; cuidado com sigilo das tratativas (só protegido em mediação/conciliação formal — CPC 166 §3º; Lei 13.140; **não há FRE 408 no Brasil**, diga isso).
- Próximo passo: carta provisória + abertura de canal.

**C — Responder firmemente (rejeitar)**
- Quando: pretensão fraca, overbroad ou faticamente errada; queremos encerrar sem judicializar.
- Tradeoff: fixa posição; se a pretensão for amparável, nossa resposta vira documento contra nós.
- Próximo passo: minutar resposta — eventualmente via `/propriedade-intelectual:cease-desist --send` reframeada como resposta.

**D — Ignorar (e preservar)**
- Quando: pretensão frívola, notificante sem capacidade aparente de ajuizar, prazo sem consequência legal direta.
- Tradeoff: silêncio pode ser invocado como elemento (não conclusivo) de má-fé; preservação documental obrigatória; risco de ajuizamento.
- Próximo passo: dever de preservação documental no nível da matéria; registrar a demanda; seguir.

**E — Preemptar com ação declaratória / pedido de nulidade**
- Quando: enfrentamos incerteza relevante de negócio, a pretensão deles é fraca, e ganhamos com nosso foro.
- Tradeoff: vamos ao ataque; orçamento e sponsor sênior aprovam; agora há processo.
- Próximo passo: **escalada para escritório externo (linha "Contencioso de PI")**. Não minute. Possíveis vias: (a) **Ação declaratória de inexistência de infração** (CPC art. 19 I) — competência cível comum (Justiça Estadual se entre privados; Justiça Federal se INPI integrar polo); (b) **Processo administrativo de nulidade** perante o INPI (LPI arts. 168–172 marcas; 50–55 patentes); (c) **Ação de nulidade** judicial (LPI art. 56 patentes; art. 175 marcas — Justiça Federal, com INPI no polo).

**F — Atacar o registro do notificante (caducidade de marca / nulidade no INPI)**
- Quando: os direitos deles são vulneráveis e queremos tirar o instrumento da mesa.
- Tradeoff: lento, custoso, público; separado da disputa em si.
- Próximo passo: escalada para escritório externo. Vias: **caducidade de marca por falta de uso** (LPI art. 143 — INPI; legitimação ampla); **nulidade de marca** (LPI art. 168 — administrativa no INPI; ou judicial na Justiça Federal — LPI art. 175); **nulidade de patente** (LPI art. 50 — administrativa; LPI art. 56 — judicial JF).

Recomende uma com duas frases de razão. Específico.

### Passo 5: Triagem de prazos

- Prazo declarado pelo notificante — anote, mas não nos vincula legalmente (salvo se lei específica der teeth).
- Prazo interno de decisão — em regra, prazo declarado menos o suficiente para minutar/revisar/aprovar.
- Prazos legais — prescrição (LPI 225 / CC 206 §3º V), decadência, prazos contratuais de cura, prazos procedimentais (LPI 158 manifestação de oposição em marca — 60 dias; LPI 70 oposição de patente — não há, há subsídios; Lei 9.610 art. 105 — perícia).

Ignorar prazo declarado é escolha, não default. Ajuizamento usualmente segue silêncio — não necessariamente o prazo declarado.

### Passo 6: Memorando de triagem

Saída: `<matter-folder>/cease-desist/inbound/<slug>/triagem.md` (ou em nível de prática).

```markdown
[CABEÇALHO DE WORK-PRODUCT — conforme config ## Outputs — varia por papel; vide `## Quem está usando`]

[BLOCO DE HERANÇA DE SIGILO — escolha por papel e tipo de matéria; vide guia abaixo]

# Notificação Extrajudicial Recebida — Triagem

> **LEITURA PARA TRIAGEM, NÃO PARECER.** Levantamento estruturado e análise de opções — não é parecer de mérito. Toda lei/regra/jurisprudência sinalizada para verificação por advogado(a); toda decisão de mérito é do(a) advogado(a).

**Slug:** [slug]
**Recebida:** [YYYY-MM-DD]
**Recebida por:** [pessoa/empresa]
**Arquivo de entrada:** [caminho]

## A afirmação

**Notificante:** [pessoa, signatário(a), OAB, escritório]
**Direito invocado:** [marca / patente / autoral / software / outro — com números INPI, jurisdições]
**Conduta alegada:** [versão deles, um parágrafo]
**Demanda:** [lista — pedidos específicos]
**Prazo declarado:** [data]
**Tom:** [firme / amena / escorched-earth]
**Protocolo:** [cartório de TD / AR / e-mail / outro]

## Validade dos direitos

[Registros como afirmados — `[verificar]` no INPI; uso anterior avaliado contra evidência citada]

## Base legal citada

[Cada citação tagueada inline com `[verificar: aplicabilidade / vigência / jurisdição]` e fonte `[Planalto/INPI/STJ/JusBrasil/fornecido pelo usuário/conhecimento do modelo — verificar]`. Não confie sem checagem independente.]

## Avaliação de plausibilidade

- **Confusão/similaridade/infração nos fatos:** [análise]
- **Overbreadth:** [análise]
- **Prazos (prescrição, decadência, caducidade):** [análise]
- **Foro:** [foro provável deles; oportunidade de declaratória nossa — CPC 19 I]

## Nossa exposição

- **Estamos infringindo?** [olhar honesto]
- **Custo de cumprir × custo de brigar:** [análise]
- **Credibilidade do(a) notificante:** [troll / reivindicante real / reincidente — com evidência processual]
- **Stakes colaterais:** [marca, clientes, precedente]

**Classificação:** [substancial / debatável / fraca / frívola] — *leitura estruturada, não parecer; `[review]`*

## Opções

### A. Cumprir rapidamente
### B. Negociar
### C. Responder firmemente
### D. Ignorar + preservar
### E. Preemptar com ação declaratória / nulidade
### F. Atacar o registro (caducidade/nulidade INPI)

**Recomendação:** [A/B/C/D/E/F] — [duas frases — `[review]`]

## Prazos

- **Prazo declarado:** [data]
- **Prazo interno de decisão:** [data]
- **Prazos legais:** [prescrição LPI 225, CC 206; decadência; contratuais — com datas]

## Ações imediatas

- [ ] Dever de preservação documental — [sim/não]
- [ ] Matéria registrada — [sim/não/a fazer]
- [ ] Advogado(a) atribuído(a) — [quem]
- [ ] Comunicar seguradora (se houver D&O ou similar) — [sim/não/N/A]
- [ ] Escalada interna — [quem/quando]
```

**Bloco de herança de sigilo — escolha por papel e tipo de matéria.** Leia `## Quem está usando` (Papel) e o tipo de matéria (marca / patente / autoral / software / segredo / OSS / outro). Esta triagem registra leitura de mérito sobre afirmação adversa; o sigilo depende de quem preparou e do quê trata. Errar em qualquer direção causa dano — marcação falsa de "sigiloso" cria admissão; sub-marcação de memorando genuinamente sigiloso pode comprometer a proteção. Insira exatamente um:

- **Papel = Advogado(a) inscrito(a) na OAB:**
  > **Sigilo profissional.** Esta triagem registra primeira leitura de mérito e postura de resposta sobre afirmação adversa. É material protegido pelo sigilo profissional do(a) advogado(a) (EOAB Lei 8.906/94 art. 7º XIX; CED-OAB; CPP art. 207; CPC art. 388 III). Não encaminhar fora do círculo profissional, anexar a comunicação a seguradora sem expurgo, ou compartilhar com a contraparte. Guardar com material sigiloso conforme padrão do escritório.

- **Papel = Mandatário/agente de PI no INPI, matéria de patente/marca perante o INPI:**
  > **Sigilo por extensão do mandato (INPI).** Esta triagem decorre de mandato específico perante o INPI (LPI art. 217). A confidencialidade é contratual e funcional ao escopo do mandato. **Não há "privilege" autônomo do agente de PI no Brasil** — não confunda com o federal patent agent-client privilege americano (*Queen's University*, USA, Fed. Cir. 2016). Bring para advogado(a) inscrito(a) na OAB para revisão antes de qualquer divulgação além do escopo do mandato.

- **Papel = Mandatário de PI, matéria FORA do INPI** (autoral, OSS, segredo, contrato, ou outro fora do escopo INPI):
  > **CONFIDENCIAL — NÃO PROTEGIDO POR SIGILO PROFISSIONAL DE ADVOGADO(A).** O mandato de agente de PI no INPI não estende sigilo profissional do(a) advogado(a) a matérias fora do INPI. Trate como confidencial, leve a advogado(a) inscrito(a) na OAB, deixe que ele(a) marque. Não encaminhe como "sigiloso" sem essa revisão.

- **Papel = Não-advogado(a) e não-mandatário(a):**
  > **CONFIDENCIAL — NÃO PROTEGIDO POR SIGILO PROFISSIONAL.** Este documento não é coberto por sigilo profissional do(a) advogado(a) até revisão por advogado(a) inscrito(a) na OAB. Trate como confidencial; não encaminhe fora da cadeia de revisão jurídica; leve a advogado(a) e deixe que ele(a) marque. Encaminhar como "sigiloso" antes da revisão profissional não cria a proteção e pode prejudicar você se a matéria for contenciosa.

Encerre o preview com este parágrafo verbatim:

> Esta é triagem, não parecer. A avaliação de força é primeira leitura a partir da notificação — não cobre fatos que você não me contou, registros que não pude verificar, e questões de foro. Advogado(a) inscrito(a) na OAB avalia antes de responder, ignorar ou comprometer um caminho.

Se usuário for não-advogado(a), acrescente o parágrafo de "encontrar advogado(a)" do modo enviar.

### Passo 7: Handoff

Conforme recomendação e confirmação do usuário:

- Responder firmemente → handoff para `/propriedade-intelectual:cease-desist --send` pré-populado como resposta (gate de enviar roda do zero).
- Negociar → carta provisória + canal de tratativa na matéria.
- Preemptar / atacar registro → escalada para escritório externo (linha "Contencioso de PI"); **não minute**.
- Criar matéria → ofereça `/propriedade-intelectual:matter-workspace new <slug>` pré-populado.
- Cumprir / ignorar → logue a decisão; emita ou confirme dever de preservação; encerre a triagem.

## Postura de decisão

Conforme `## Postura de decisão em juízos jurídicos subjetivos`: incerteza sobre infração, sobre colidência marcária, sobre similaridade substancial de obra, sobre se a pretensão é amparável — **não decida silenciosamente**. Sinalize para advogado(a), levante fatores de cada lado. Envio sobre presunção é porta de uma via; sinalização é porta de duas.

## O que esta skill NÃO faz

- **Enviar a notificação.** Apenas minuta. Envio pelo(a) advogado(a)/cliente após aprovação.
- **Verificar autoridades por conta própria.** Placeholders ficam como placeholders até o usuário trazer fontes ou ferramenta de pesquisa retornar. Inventar citações é responsabilidade ética.
- **Atalhar o gate.** Gate roda toda vez no modo enviar.
- **Decidir mérito definitivamente no modo receber.** Classificação é leitura estruturada para roteamento; parecer formal mora com advogado(a).
- **Validar a lei citada pelo notificante.** Sinaliza; não conclui validade.
- **Decidir criar matéria.** Surface a recomendação; usuário decide.
