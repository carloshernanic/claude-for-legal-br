---
name: dsar-response
description: >
  Conduz uma Requisição de Titular (LGPD art. 18) — acesso, eliminação,
  portabilidade, correção — e minuta a resposta: verifica identidade, localiza
  dados sistema por sistema, avalia exceções, minuta cartas de acuso de
  recebimento e resposta substantiva. Use quando chega uma requisição, o(a)
  usuário(a) cola um pedido de acesso/eliminação/portabilidade/correção, ou
  diz "chegou requisição de titular", "pedido de acesso", "direito ao
  esquecimento", "alguém quer os dados dele".
argument-hint: "[cole o pedido, ou descreva]"
---

# /dsar-response

> **Adaptação BR.** Esta skill foi adaptada para o regime da **LGPD (Lei 13.709/2018) art. 18** (direitos do titular). "DSAR" → **requisição de titular** ou **pedido de exercício de direitos**. Supervisory authority → **ANPD**. "Attorney work product" → sigilo profissional do(a) advogado(a) (**EOAB art. 7º XIX**). Prazos da LGPD: art. 19 §3º — confirmação simplificada imediata; art. 19, II — 15 dias para informação completa. Sempre cite primária BR.

1. Carregue `~/.claude/plugins/config/claude-for-legal/privacidade/CLAUDE.md` → processo de requisição de titular (lista de sistemas, método de verificação, SLA).
2. Rode o workflow abaixo.
3. Classifique o tipo de pedido. Cheque gatilhos de escalonamento — se algum disparar, encaminhe antes de prosseguir.
4. Caminhe: verificar identidade → percorrer lista de sistemas → análise de exceções → minutar.
5. Saída: minuta de resposta. NÃO envie — humano revisa e envia.
6. Registre a requisição conforme processo da casa.

**Antes de colar o pedido:** vai conter dados pessoais do(a) titular. Confirme que sua sessão e o armazenamento da saída atendem aos requisitos de tratamento. Redija o que não precisa (anexos de documentos de identidade, fios de e-mail não relacionados). Não armazene o nome do(a) titular em nomes de arquivo.

```
/privacidade:dsar-response
[cole o e-mail com o pedido]
```

---

# Minuta de Resposta à Requisição de Titular (LGPD)

## Contexto de caso

**Contexto de caso.** Cheque `## Workspaces de caso` no CLAUDE.md de prática. Se `Enabled` é `✗` (padrão para in-house), pule o resto deste parágrafo — skills usam contexto de prática e a máquina de casos fica invisível. Se habilitado e sem caso ativo, pergunte: "Para qual caso? Rode `/privacidade:matter-workspace switch <slug>` ou diga `prática`." Carregue o `matter.md` do caso ativo para contexto e overrides. Escreva saídas em `~/.claude/plugins/config/claude-for-legal/privacidade/matters/<matter-slug>/`. Nunca leia arquivos de outro caso salvo se `Cross-matter context` for `on`.

---

## Propósito

Uma requisição de titular tem prazo (definido pela LGPD ou regime aplicável), processo (verificar, localizar, avaliar exceções, responder), e vários pontos onde pode dar errado. Esta skill caminha por cada passo e minuta a resposta.

## Hipótese jurisdicional

Esta análise assume o escopo jurisdicional especificado na sua configuração — **prioritariamente LGPD (Lei 13.709/2018) para tratamento no Brasil ou de titulares no Brasil (art. 3º)**. Regras, prazos e bases legais variam materialmente entre regimes (LGPD vs. GDPR vs. CCPA vs. setoriais). Se o(a) titular, a atividade ou o(a) controlador(a) estiver em jurisdição diferente da configurada, esta análise pode não se aplicar literalmente.

## Carregue o processo

Leia `~/.claude/plugins/config/claude-for-legal/privacidade/CLAUDE.md` → `## Processo de requisição de titular`. Tem:
- A lista de sistemas (todos os lugares onde há dados pessoais)
- Método de verificação de identidade
- SLA de resposta
- Quem trata rotineiro vs. quem recebe escalonamento

Se a lista de sistemas estiver vazia ou desatualizada, sinalize — não dá para completar requisição sem saber onde olhar.

## Workflow

### Passo 1: Classifique o pedido

Identifique qual direito o(a) titular está invocando. Conforme **LGPD art. 18**, são (categorias canônicas):

- **Confirmação da existência de tratamento** (art. 18, I)
- **Acesso aos dados** (art. 18, II)
- **Correção** de dados incompletos, inexatos ou desatualizados (art. 18, III)
- **Anonimização, bloqueio ou eliminação** de dados desnecessários, excessivos ou tratados em desconformidade (art. 18, IV)
- **Portabilidade** a outro fornecedor de serviço ou produto, observado o segredo comercial e industrial (art. 18, V — regulamentação parcial via Res. CD/ANPD pendente em alguns pontos)
- **Eliminação** dos dados tratados com consentimento (art. 18, VI)
- **Informação** sobre entidades públicas e privadas com as quais houve compartilhamento (art. 18, VII)
- **Informação** sobre a possibilidade de não fornecer consentimento e as consequências (art. 18, VIII)
- **Revogação do consentimento** (art. 18, IX)
- **Revisão de decisões automatizadas** (art. 20)
- **Oposição a tratamento** com fundamento em hipótese de dispensa de consentimento, em caso de descumprimento (art. 18 §2º)

**Pesquise a regra aplicável antes de prosseguir.** Para cada direito invocado, identifique o(s) regime(s) (LGPD; e setoriais quando aplicável: BCB/Open Finance, ANS, CFM, ECA art. 100 para crianças, Marco Civil da Internet, CDC quando há relação de consumo, GDPR se titular UE, CCPA se titular CA, etc.). Cite o dispositivo controlador com pinpoint — artigo, inciso, parágrafo —, o escopo do direito, e quaisquer ressalvas. Sinalize incerteza e escale para verificação por advogado(a) em vez de afirmar regra não confirmada.

> **Sem complementação silenciosa.** Se a consulta ao conector de pesquisa configurado retornar poucos ou nenhum resultado para os direitos/exceções/prazos do regime, reporte o que achou e pare. NÃO preencha a lacuna com busca web ou conhecimento do modelo sem perguntar. Diga: "A busca retornou [N] resultados de [ferramenta]. A cobertura parece rasa para [regime / direito]. Opções: (1) ampliar a query, (2) tentar outra ferramenta, (3) busca web — resultados serão tagueados `[busca web — verificar]` e devem ser conferidos contra fonte primária antes de confiar, ou (4) sinalizar como não verificado e parar. O que prefere?" Quem decide aceitar fontes de menor confiança é o(a) advogado(a).
>
> **Atribuição de fonte em camadas.** Etiquete toda citação com a fonte. Para citações de conhecimento do modelo, use uma das três camadas em vez de uma tag genérica:
>
> - `[estável]` — referências estatutárias/regulatórias estáveis e bem conhecidas com baixa chance de ter mudado (ex.: LGPD art. 18, art. 41; conceito do prazo de 15 dias do art. 19 II). Verificar antes de protocolar, mas prioridade menor.
> - `[verificar]` — citações de conhecimento do modelo plausíveis mas que devem ser conferidas: resoluções específicas da ANPD, guias orientativos, decisões do STJ/STF, thresholds, datas de vigência, alterações pós-treino.
> - `[verificar-pinpoint]` — citações com pinpoint (inciso, parágrafo, número de processo, página) carregam o maior risco de fabricação e DEVEM SEMPRE ser verificadas contra fonte primária.
>
> Citações recuperadas por ferramenta mantêm a tag da fonte (`[JusBrasil]`, `[ANPD]`, `[Diário Oficial]`, ou o nome da MCP); web são `[busca web — verificar]`; informações do(a) usuário(a) são `[fornecido pelo usuário]`. A camada explicita o trabalho real de verificação — quem verifica tudo verifica nada. Nunca remova ou colapse as tags.

Alguns pedidos são combinações — "exclui minha conta e me manda meus dados antes" é eliminação + portabilidade/acesso. Trate como duas requisições ligadas.

### Passo 2: Verifique identidade

Por o método em `~/.claude/plugins/config/claude-for-legal/privacidade/CLAUDE.md`. Abordagens comuns:

- **Verificação logada:** Pedido veio de sessão autenticada → identidade confirmada
- **Match de e-mail:** Pedido veio de e-mail cadastrado → costuma bastar para pedidos de baixo risco
- **Verificação adicional:** Para contas de alto valor ou pedidos de eliminação → pergunta-desafio, telefone, documento de identidade (lembre LGPD art. 6º III — necessidade: não colete mais do que o suficiente)

**Calibre ao risco.** Sobreverificação vira barreira (visual ruim com a ANPD). Subverificação corre risco de entregar dados a fraudador.

Se a identidade não pode ser verificada:

```markdown
Não conseguimos confirmar que este pedido veio da pessoa cujos dados estão em
discussão. Para prosseguir, por favor [passo de verificação]. Não podemos
fornecer dados pessoais em resposta a um pedido que não conseguimos verificar.
```

Isto pode (argumentavelmente) suspender o relógio, mas não fique sentado em cima — responda dizendo que precisa de verificação em poucos dias, não no dia 14.

### Passo 3: Localize os dados

Percorra a lista de sistemas do `~/.claude/plugins/config/claude-for-legal/privacidade/CLAUDE.md`. Para cada sistema:

| Sistema | Consultado? | Achou dado? | O quê |
|---|---|---|---|
| Banco de produção | | | |
| Analytics (ex.: Mixpanel, Amplitude, Google Analytics) | | | |
| Tickets de suporte (ex.: Zendesk, Movidesk) | | | |
| CRM (ex.: Salesforce, HubSpot, RD Station) | | | |
| E-mail marketing (ex.: Mailchimp, RD Station) | | | |
| Logs | | | |
| Backups | | | (nota: usualmente fora do escopo da eliminação imediata — ver abaixo) |
| Operadores terceiros (e suboperadores LGPD art. 39 III) | | | (podem precisar ser notificados para eliminar) |

Para operador(a) B2B: o "titular" costuma ser *o(a) usuário(a) final do(a) seu(sua) cliente*. Cheque se isto é, na verdade, requisição do(a) seu(sua) cliente para tratar — muitos contratos de tratamento dizem "encaminhe requisições ao(à) controlador(a)". A LGPD reforça: art. 18 §1º diz que o(a) titular pode peticionar ao(à) controlador(a); o(a) operador(a) é coadjuvante.

### Passo 4: Análise de exceções

Nem tudo é produzido ou eliminado. **Pesquise a regra aplicável antes de prosseguir.** Para cada item, identifique cada exceção que plausivelmente se aplica sob o regime em escopo. As principais hipóteses da LGPD:

- **LGPD art. 16:** retenção possível para cumprimento de obrigação legal ou regulatória, estudo por órgão de pesquisa (anonimizado quando possível), transferência a terceiro (com requisitos), uso exclusivo do(a) controlador(a) (vedado o acesso de terceiro, anonimizado quando possível).
- **LGPD art. 11 §4º:** vedações específicas em dados sensíveis.
- **Segredo de empresa / segredo comercial e industrial** (LGPD art. 19 §1º para portabilidade).
- **Direitos de terceiros** (dados de outras pessoas que não o(a) titular).
- **Exercício regular de direitos em processo judicial, administrativo ou arbitral** (LGPD art. 7º VI).
- **Sigilo profissional** (EOAB art. 7º XIX).
- **Obrigações de retenção fiscal e contábil** (CC art. 1.179 e ss.; Decreto 9.580/2018; legislação tributária).
- **Litígio em curso** (preservação probatória).
- **Backup rotation** — acomodações técnicas devem ser documentadas, não usadas como desculpa geral.

Cite o dispositivo controlador (lei, resolução, decisão STJ/STF) com pinpoint. Escopo das exceções varia por regime — verifique vigência e sinalize incerteza.

**Não estreite a lista por julgamento subjetivo.** A skill propõe exceções onde houver base de boa-fé e sinaliza as incertas; o(a) advogado(a) estreita antes do envio. Deixar cair uma exceção que depois se aplica é caro — uma vez divulgado o material, a exceção foi praticamente perdida. Sobreafirmar exceção plausível é corrigível em revisão. Prefira o erro recuperável.

Cada exceção proposta vai com nota explícita: **"proposto — exige revisão por advogado(a) habilitado(a) na OAB antes de afirmar. A ANPD escrutina afirmações genéricas de exceção, então o(a) advogado(a) estreita; a skill não."**

Questões recorrentes para trabalhar:

- O registro contém dados *sobre outras pessoas* que precisam ser redigidos antes da entrega?
- Há obrigação legal específica de retenção que bloqueia a eliminação? Cite.
- Há hold de litígio cobrindo os dados deste(a) titular?
- O(a) requerente está pedindo eliminação de dados tratados com base legal diversa de consentimento (em geral só o art. 18, IV se aplica — anonimização/bloqueio/eliminação por desnecessidade)?
- Acomodações de backup rotation precisam ser documentadas (não usadas como desculpa geral)?

**Documente toda exceção afirmada.** Se a ANPD perguntar por que não eliminou algo, "tínhamos obrigação legal" precisa de citação.

### Passo 5: Minute a resposta — DUAS CARTAS

> **Pré-voo de conector de pesquisa.** Antes de emitir qualquer carta ou a análise interna de exceções, cheque se um conector de pesquisa jurídica é alcançável (JusBrasil, sites da ANPD/STJ/STF, Diário Oficial da União, MCP de lei/regulador). Registre na linha **Fontes:** da nota de revisão conforme CLAUDE.md `## Saídas` — a nota fica na análise INTERNA, NÃO nas cartas externas ao(à) titular. Se nenhum conector retornar resultados no Passo 1 (classificação), Passo 4 (exceções) ou na pesquisa de gerenciamento de prazo, registre na linha **Fontes:** — ex.: `não conectado — citações vêm de conhecimento de treinamento; exceções afirmadas, prazos de resposta e mecanismos de prorrogação são especialmente propensos a fabricação, verificar antes de afirmar qualquer exceção a titular ou à ANPD`. Tags por citação `[conhecimento do modelo — verificar]` permanecem inline. Não emita banner separado acima da saída.

A LGPD e a boa prática esperam (e em parte exigem) um aviso de recebimento prévio e separado da resposta substantiva. Produza os dois; não colapse num único e-mail que sai no dia 15.

- **Passo 5a — Carta de acuso de recebimento.** Enviada em poucos dias do recebimento (alvo: mesmo dia a 3-5 dias, sempre bem dentro da janela legal). Confirma recebimento, declara como o(a) controlador(a) entende o pedido, declara o prazo de resposta e a data-alvo, pede material de verificação ainda pendente. NÃO contém a entrega substantiva. Um acuso pronto é o primeiro sinal visível à ANPD de que o processo está funcionando; também reduz risco de pedido duplicado ou reclamação precoce.
- **Passo 5b — Carta de resposta substantiva.** A entrega real, confirmação de eliminação ou export de portabilidade. Sai pelo prazo legal (ou SLA interno se mais apertado). Apenas após verificação de identidade e análise de localização + exceções.

**Antes de enviar qualquer carta:** Leia `## Quem está usando` em `~/.claude/plugins/config/claude-for-legal/privacidade/CLAUDE.md`. Se Papel for Não advogado(a):

> Enviar resposta a requisição de titular tem consequências jurídicas — conteúdo, exceções afirmadas e omissões são revisáveis por ANPD ou em juízo, e erros viram exposição sancionatória. Você revisou com advogado(a)? Se sim, prossiga. Se não, eis um brief para levar a ele(a):
>
> [Gerar resumo de 1 página: titular, direito invocado, regime(s) aplicável(is), o que foi localizado nos sistemas, o que está sendo retido e sob qual exceção, postura de verificação de identidade, prazo de resposta e as três perguntas para o(a) advogado(a) antes da carta sair.]
>
> Se precisa encontrar advogado(a) habilitado(a) na OAB: a Seccional da OAB do seu estado tem serviço de orientação e referência. Muitos(as) oferecem consulta inicial gratuita ou de baixo custo.

Não passe deste portão sem "sim" explícito.

> **Nota:** As duas cartas são entregáveis externos ao(à) titular. **Não** inclua o cabeçalho de material de trabalho do `~/.claude/plugins/config/claude-for-legal/privacidade/CLAUDE.md` `## Saídas` em nenhuma carta. Notas internas, logs e análise de exceções que acompanham as cartas são protegidos por sigilo profissional — mantenha separados e use o cabeçalho conforme `## Saídas` (que varia por papel — ver `## Quem está usando`).

> **Antes de enviar qualquer carta:** É minuta para revisão por advogado(a), não resposta para enviar. Enviar compromete o(a) controlador(a) com uma posição, pode renunciar a exceções e iniciar relógios de regulador. Um(a) advogado(a) habilitado(a) na OAB revisa, edita e aprova antes da carta ir ao(à) titular. Não envie sem revisão.

#### Passo 5a — Template da carta de acuso

```markdown
Assunto: Recebemos seu pedido de exercício de direitos — [Empresa] — [data]

Prezado(a) [Nome],

Recebemos seu pedido de [confirmação / acesso / correção / anonimização / eliminação / portabilidade / informação sobre compartilhamento / revogação de consentimento] em [data de recebimento], com base no art. 18 da LGPD (Lei 13.709/2018).

**Seu pedido, como entendemos:** [reformulação em uma frase — ex.: "cópia de todos os dados pessoais associados à sua conta, com a indicação de terceiros com quem houve compartilhamento, e eliminação da conta após a entrega."]

**O que acontece a seguir:**
- Nossa data-alvo para resposta substantiva é [data — não posterior ao prazo do regime; usar SLA interno se mais apertado]. [Se verificação de identidade pendente: "Precisamos de [passo específico] antes de prosseguir — veja abaixo."]
- A LGPD prevê resposta em formato simplificado de imediato e em até 15 dias para informação completa (art. 19, II), salvo regra setorial diversa.
- Se precisarmos de mais tempo por complexidade ou por receber outros pedidos seus em paralelo, vamos avisá-lo(a) antes do prazo inicial e justificar.
- Nenhuma taxa se aplica a este pedido (LGPD art. 18 §5º — resposta gratuita).

[Se verificação de identidade pendente:]
**Para verificar sua identidade,** por favor [passo específico — ex.: "responda a este e-mail do endereço cadastrado com os últimos 4 dígitos do meio de pagamento que temos"]. Isso não suspende nosso prazo; trabalhamos em paralelo.

Em caso de dúvidas, contate [Encarregado(a) — nome e canal de contato — LGPD art. 41 §1º].

[Remetente]
```

**Regra de início de relógio.** O prazo começa no recebimento do pedido, não na conclusão da verificação de identidade — salvo se regime aplicável dispuser de modo diverso. Não suspenda tacitamente o relógio na verificação. Se regime tem gatilho diverso, cite; não presuma.

#### Passo 5b — Templates de carta substantiva

**Resposta de acesso:**

```markdown
Assunto: Sua Requisição de Acesso — [Empresa] — [data]

Recebemos em [data] seu pedido de cópia dos dados pessoais que tratamos sobre você.

**O que encontramos:**

Tratamos as seguintes categorias de dados pessoais associadas a [identificador]:

| Categoria | Origem | Finalidade | Base legal (LGPD art. 7º/11) | Retenção |
|---|---|---|---|---|
| [Cadastro: nome, e-mail] | Você, no cadastro | Gestão da conta | Execução de contrato (art. 7º V) | Até exclusão da conta |
| [Dados de uso] | Nosso serviço | Analytics, melhoria do produto | Legítimo interesse (art. 7º IX) | [período] |
| [Suporte] | Você | Atendimento | Execução de contrato | [período] |

**Seus dados estão anexados** em [formato]. [Nota de entrega segura — pacote com senha, link com expiração, etc.]

**Compartilhamento com terceiros (LGPD art. 18, VII):** Compartilhamos dados com os seguintes operadores: [lista ou link para a página de suboperadores].

**Outros direitos (LGPD art. 18):** Você também pode pedir [confirmação / correção / anonimização / eliminação / portabilidade / informação sobre compartilhamento / revogação de consentimento / revisão de decisão automatizada art. 20]. Para fazê-lo, [método — canal do(a) Encarregado(a)].

**Dados que não incluímos:**
- [Categoria] — [exceção e fundamento — ex.: "logs internos de segurança — disclosure comprometeria medidas de segurança"]
- [Dados sobre terceiros redigidos das correspondências de suporte conforme LGPD art. 18, II + proteção de dados de terceiros]

Em caso de dúvidas, contate [Encarregado(a) — canal].
```

**Resposta de eliminação:**

```markdown
Assunto: Sua Requisição de Eliminação — [Empresa] — [data]

Recebemos em [data] seu pedido de eliminação dos dados pessoais que tratamos sobre você.

**O que eliminamos:**

| Categoria | Sistema | Eliminado em |
|---|---|---|
| [Conta e perfil] | Produção | [data] |
| [Eventos de analytics] | [Amplitude/etc.] | [data] |
| [etc.] | | |

**O que mantivemos e por quê (LGPD art. 16):**

| Categoria | Fundamento | Mantido até |
|---|---|---|
| [Registros transacionais] | Obrigação legal (retenção fiscal — Decreto 9.580/2018 e legislação tributária) | [data] |
| [Snapshots de backup] | Eliminação na próxima rotação técnica | [data] |
| [Dados em litígio em curso] | Exercício regular de direitos (LGPD art. 7º VI) | [enquanto durar o processo] |

**Operadores terceiros (LGPD art. 39 III):** Instruímos [lista] a eliminar seus dados em seus sistemas.

Sua conta está encerrada. Em caso de dúvidas, contate [Encarregado(a)].
```

### Passo 6: Registre

Requisições de titular são auditadas. Registre:
- Data de recebimento
- Data de verificação de identidade
- Data de resposta
- O que foi produzido/eliminado
- Exceções afirmadas e fundamento
- Quem tratou

Se o time usa ferramenta de tracking, crie o registro lá. Se não, arquivo de log funciona. Lembre que a ANPD pode requisitar histórico em fiscalização (LGPD art. 55-J).

## Gatilhos de escalonamento

Por `~/.claude/plugins/config/claude-for-legal/privacidade/CLAUDE.md` → tabela de Escalonamento, escale quando:

- Requerente é (ou pode ser) parte adversa, advogado(a) adverso(a), jornalista
- Escopo é incomum ("todos os dados, incluindo comunicações internas sobre mim")
- Há hold de litígio sobre os dados do(a) requerente (pedido de eliminação + hold = conflito, advogado(a) decide)
- Requerente está disputando resposta anterior
- ANPD ou outro regulador está em cópia ou mencionado(a)
- Pedido envolve **dados sensíveis** (LGPD art. 5º II) ou **dados de crianças/adolescentes** (LGPD art. 14)
- Pedido invoca revisão de decisão automatizada (LGPD art. 20)

## Gerenciamento de prazos

**Regra das duas cartas.** Toda requisição produz acuso (pronto — alvo mesmo dia a 3-5 dias) E carta substantiva (pelo prazo legal). LGPD e a prática esperam acuso pronto separado da resposta; carta única no dia 15 é falha de processo mesmo se substantivamente correta.

**Pesquise o prazo de resposta vigente para o direito invocado e o regime aplicável.** Prazos canônicos:
- **LGPD art. 19, II:** **15 dias** a contar do pedido para resposta com informações completas (acesso, etc.).
- **LGPD art. 19 §3º:** confirmação simplificada **imediata**.
- **CDC** (quando há relação de consumo): prazos do art. 43 §3º podem ser invocados em paralelo para retificação cadastral.
- Setoriais: BCB, ANS, CFM podem prever prazos paralelos — verifique.

Cheque se há mecanismo de prorrogação (LGPD não prevê expressamente, mas a prática sugere comunicação prévia com fundamento); cite o dispositivo. Identifique quando o relógio começa (recebimento, em regra). Cite o dispositivo controlador com pinpoint. Verifique vigência — Resoluções CD/ANPD podem complementar.

Se `~/.claude/plugins/config/claude-for-legal/privacidade/CLAUDE.md` → `## Processo de requisição de titular` registra SLA interno mais apertado que o legal, use o interno e note o backstop legal.

Se vai precisar de mais tempo, mande o aviso "precisamos de mais tempo" bem antes do prazo inicial. Prorrogação no dia 15 fica ruim.

## O que esta skill não faz

- Não consulta sistemas diretamente. Caminha pelo checklist; humano (ou ferramenta conectada) faz as consultas reais.
- Não decide exceções em casos limítrofes. Sinaliza para advogado(a).
- Não envia a resposta. Minuta, advogado(a) revisa, humano envia.
