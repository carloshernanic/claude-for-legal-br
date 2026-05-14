# IP Counsel Plugin (Adaptação BR)

> **Adaptação BR não oficial.** Este plugin é uma adaptação experimental do plugin original `propriedade-intelectual` (jurisdição EUA) para o contexto brasileiro de propriedade intelectual: **INPI**, **LPI (Lei 9.279/1996)**, **Lei de Direitos Autorais (Lei 9.610/1998)**, **Lei do Software (Lei 9.609/1998)** e **Marco Civil da Internet (Lei 12.965/2014)**. Não é trabalho oficial da Anthropic nem produto homologado por banca/escritório. Toda saída é **rascunho para revisão por advogado(a) inscrito(a) na OAB** — não é parecer jurídico, não substitui análise profissional. Onde a equivalência EUA→BR é imperfeita, sinalize na própria peça.

Prática de propriedade intelectual: marca, direito autoral, patente, segredo de empresa e código aberto. Redige e triagia notificações extrajudiciais (cease & desist) e pedidos de remoção de conteúdo on-line (Marco Civil — em regra mediante ordem judicial), faz busca de anterioridade de marca e triagem de liberdade de operação (FTO), revisa cláusulas de PI em contratos, acompanha registros e prazos de renovação no INPI, e verifica compliance de licenças open source. Construído em torno de um perfil de prática preenchido por entrevista inicial — o plugin aprende *a sua* postura de enforcement, portfólio e matriz de aprovações, não uma genérica.

**Cada saída é um rascunho para revisão por advogado(a) — citado, sinalizado e travado em pontos críticos — não é conclusão jurídica.** O plugin faz o trabalho: lê os documentos, aplica o seu playbook, identifica os problemas, redige a peça. Um(a) advogado(a) revisa, verifica e decide. Citações são marcadas por fonte para você saber quais vieram de ferramenta de pesquisa e quais precisam ser conferidas. Marcações de sigilo profissional são aplicadas de forma conservadora para que nada se torne público por acidente. Ações consequentes — protocolar, enviar, executar — ficam atrás de confirmação explícita.

## Para quem é

| Papel | Fluxos principais |
|---|---|
| **Advogado(a) de PI in-house** | Decisões de enforcement, revisão de cláusulas, supervisão de portfólio, triagem de FTO |
| **Paralegal / especialista de PI** | Acompanhamento de portfólio e renovações, busca de anterioridade, intake de matérias |
| **Brand protection / proteção de marca** | Notificações extrajudiciais, pedidos de remoção on-line, follow-up de watch service |
| **Advogado(a) de marca / direito autoral** | Busca de anterioridade, revisão de cláusulas, manutenção de portfólio — *não redação de reivindicações de patente* |
| **Associado(a) de PI em escritório** | Workspaces de matéria por cliente, triagem de FTO, revisão de cláusulas |
| **Legal ops gerenciando portfólio de PI** | Registro de portfólio, prazos de renovação, compliance de OSS |

Este plugin **não** redige reivindicações de patente. Redação e estratégia de patente são ofício especializado (agente de PI ou advogado(a) especialista) e não devem ser delegadas a uma ferramenta generalista. O trabalho de patente aqui se limita a triagem de FTO (este produto está bloqueado por patente de terceiro?), revisão de cláusulas de PI, acompanhamento de renovações/anuidades, e triagem de infração.

> **Software no Brasil ≠ patente.** A proteção de programa de computador no Brasil é por **direito autoral**, regida pela **Lei 9.609/1998** (com registro facultativo no INPI). Software, em regra, **não é patenteável** (LPI art. 10, V), salvo invenções implementadas por computador com efeito técnico (Diretrizes INPI 2020). Quando o usuário falar em "patentear software", confirme se ele quer registro de software (9.609) ou patente de invenção implementada por computador.

## Primeira execução: entrevista inicial

Na primeira utilização, o plugin entrevista você — dez a quinze minutos, conversacional — para aprender como sua prática funciona de fato. Pergunta sobre mix de áreas de PI, jurisdições onde você atua, postura de enforcement, matriz de aprovações e gatilhos de escalada. Depois pede sua lista de portfólio, manual de marca, modelos de notificação extrajudicial, playbook de enforcement e política de open source — o que você tiver — para extrair em vez de pedir que você redigite tudo.

O resultado fica em `~/.claude/plugins/config/claude-for-legal/propriedade-intelectual/CLAUDE.md` — um documento em português claro sobre sua prática que toda outra skill lê antes de fazer qualquer coisa. Você edita o documento, não um arquivo de configuração.

```
/propriedade-intelectual:cold-start-interview
```

**Mix de áreas de prática.** Logo no início, será perguntado em quais áreas de PI você efetivamente atua — marca, patente, direito autoral, segredo de empresa, open source ou todas. O plugin pula perguntas em áreas em que você não atua. Sua configuração pode manter múltiplas áreas em paralelo, e cada skill pergunta qual área aplica quando não estiver óbvio pelo que você colou.

**Postura de enforcement.** Será perguntado onde você se posiciona no espectro agressivo / equilibrado / conservador para enviar notificações de assertividade, e quem aprova o envio de cada tipo de carta. A postura inverte os defaults das skills `cease-desist`, `takedown` e `infringement-triage`.

## Comandos

| Comando | Faz |
|---|---|
| `/propriedade-intelectual:cold-start-interview` | Roda (ou re-roda) a entrevista inicial |
| `/propriedade-intelectual:cease-desist [contexto]` | Notificação extrajudicial — enviar, ou triagiar uma recebida, com o roteiro de aprovação que seu CLAUDE.md exigir |
| `/propriedade-intelectual:takedown [contexto]` | Pedido de remoção on-line — em regra, ordem judicial (Marco Civil art. 19); exceção art. 21 (nudez não consentida); ou resposta a notificação recebida |
| `/propriedade-intelectual:clearance [marca]` | Busca de anterioridade de marca em base INPI — confronto de classes NCL/Nice + análise de confundibilidade, advogado(a) assina |
| `/propriedade-intelectual:fto-triage [produto / escopo da reivindicação]` | Triagem de liberdade de operação — surfaceia anterioridades bloqueantes para revisão |
| `/propriedade-intelectual:invention-intake [disclosure]` | Triagem inicial de invenção — novidade, atividade inventiva, matéria patenteável (LPI arts. 8º e 10), aplicação industrial, prazo de graça (LPI art. 12), detectabilidade, valor estratégico |
| `/propriedade-intelectual:infringement-triage [contexto]` | Triagem de infração — vale a pena agir, e como |
| `/propriedade-intelectual:ip-clause-review [arquivo]` | Revisa cláusulas de PI em contrato — cessão, licença, indenidade por PI, declarações de OSS |
| `/propriedade-intelectual:oss-review [repo / lista de arquivos]` | Verificação de licenças open source — obrigações de copyleft, atribuição, compatibilidade |
| `/propriedade-intelectual:portfolio` | Registro e acompanhamento de renovações — o que vence, o que foi protocolado, o que precisa de ação |
| `/propriedade-intelectual:matter-workspace` | Gerencia workspaces de matéria (escritório de múltiplos clientes apenas) — novo, listar, alternar, fechar, nenhum |

## Skills

| Skill | Função |
|---|---|
| **cold-start-interview** | Entrevista inicial que escreve `~/.claude/plugins/config/claude-for-legal/propriedade-intelectual/CLAUDE.md` |
| **cease-desist** | Redige ou triagia notificação extrajudicial; passa pela matriz de aprovação antes do envio |
| **takedown** | Pedido de remoção de conteúdo on-line — Marco Civil exige ordem judicial em regra; arquitetura completamente diferente do DMCA §512 |
| **clearance** | Busca de anterioridade na base INPI + análise de risco de confusão para marca proposta |
| **fto-triage** | Triagem de FTO — sinaliza anterioridades que advogado(a) deve ler antes do lançamento |
| **invention-intake** | Triagem inicial de patenteabilidade — novidade, atividade inventiva, matéria (LPI art. 10), prazo de graça |
| **infringement-triage** | Diante de aparente infração, decidir: ignorar / carta amena / notificação extrajudicial / ajuizar |
| **ip-clause-review** | Revisa cláusulas de PI em contratos-quadro, ordens de serviço, licenças, contratos de prestador |
| **oss-review** | Verifica licenças open source contra a política de OSS |
| **portfolio** | Registro de portfólio, prazos de renovação/anuidade, dashboard de status |
| **matter-workspace** | Cria, lista, alterna e fecha workspaces de matéria para escritórios; isola cada cliente/matéria para que contexto não vaze |

## Comandos interativos vs. agentes em schedule

Os comandos acima rodam quando você invoca — para quando está trabalhando uma matéria. Os agentes abaixo rodam em schedule — para o que se move enquanto você não está olhando:

| Agente | O que monitora | Cadência padrão |
|---|---|---|
| **ip-renewal-watcher** | Registro de portfólio — calcula o que vence (renovação decenal de marca LPI art. 133, anuidades de patente, prova de uso para evitar caducidade LPI art. 143) nos próximos 90 dias | Semanal |

## Conectores e verificação de citação

**Conecte uma ferramenta de pesquisa primeiro — os guardrails de citação dependem disso.** Sem conector, toda citação é marcada `[verificar]` e a nota do revisor acima de cada entregável registra que as fontes não foram verificadas. O plugin funciona dos dois jeitos; ele só faz mais da verificação por você quando há ferramenta conectada.

Os conectores de pesquisa jurídica não são apenas fontes de dados — são a diferença entre uma citação verificada e uma que você precisa conferir. Uma citação obtida via ferramenta conectada é marcada com a fonte e pode ser rastreada. Citação vinda do conhecimento do modelo ou de web search é marcada `[verificar]` ou `[verificar-pinpoint]` e deve ser conferida contra fonte primária antes que alguém se baseie nela.

> **Conectores EUA × BR.** Os conectores ofertados na versão original (CourtListener, Descrybe, Solve Intelligence) cobrem majoritariamente decisões e literatura EUA — úteis quando o caso envolve elementos de direito comparado ou anterioridade técnica internacional (PCT, USPTO, EPO), **insuficientes para confronto puramente brasileiro**. Para pesquisa BR, use: **busca.inpi.gov.br** (marcas, patentes, software, indicações geográficas, desenhos industriais), **JusBrasil / Jurídico** ou conectores STJ/STF/TJs (jurisprudência), Diário Oficial da União/INPI (RPI — Revista da Propriedade Industrial). Sinalize na nota do revisor qual base foi de fato consultada.

## Integrações

Acompanha conectores configurados em `.mcp.json`:

- **Solve Intelligence** — busca de literatura patentária e não-patentária, padrões técnicos SEP, anterioridades, análise de reivindicações (foco internacional / PCT / USPTO; complementar para BR)
- **CourtListener** — opiniões de cortes federais dos EUA, dockets PACER (somente direito comparado / litígio internacional)
- **Descrybe** — pesquisa de direito primário por conceito ou fraseologia (cobertura EUA)
- **Slack** — busca de mensagens, leitura de canais, localização de discussões
- **Google Drive** — busca, leitura e fetch de documentos

Com pesquisa de patente conectada: skills de FTO e prior-art puxam anterioridades automaticamente em vez de depender de listas fornecidas pelo usuário.

Com ferramenta de jurisprudência conectada: skills de clearance e infringement-triage verificam precedentes e checam se o caso citado ainda é bom direito.

Com Drive ou Slack conectado: exportações de portfólio, modelos de notificação e atualizações do log de enforcement passam pelo canal que você indicar.

## Quick start

### 1. Seja entrevistado

```
/propriedade-intelectual:cold-start-interview
```

Dez a quinze minutos. Tenha à mão sua lista de portfólio (INPI), manual de marca (se houver), modelo de notificação extrajudicial (se houver) e sua política de OSS (se houver).

Sua configuração fica em `~/.claude/plugins/config/claude-for-legal/propriedade-intelectual/CLAUDE.md` e sobrevive a atualizações do plugin.

### 2. Faça busca de anterioridade de uma marca

```
/propriedade-intelectual:clearance "APEXLEAF"
```

Saída: lista de colidências no INPI, análise de fatores de confundibilidade, sinalizações para advogado(a). Não é "vai/não vai" — é triagem.

### 3. Veja o que está vencendo

```
/propriedade-intelectual:portfolio
```

Saída: registros com renovação decenal (marca, LPI art. 133), anuidades de patente, ou outras providências (declaração de uso para evitar caducidade, LPI art. 143) nos próximos 90 dias, agrupados por urgência.

## Estrutura de arquivos

```
propriedade-intelectual/
├── .claude-plugin/plugin.json
├── .mcp.json
├── CLAUDE.md                    # Seu perfil de prática — escrito pela entrevista, editado por você
├── README.md
├── agents/
│   └── ip-renewal-watcher.md
├── skills/
│   ├── cold-start-interview/
│   ├── cease-desist/
│   ├── takedown/
│   ├── clearance/
│   ├── fto-triage/
│   ├── invention-intake/
│   ├── infringement-triage/
│   ├── ip-clause-review/
│   ├── oss-review/
│   ├── portfolio/
│   └── matter-workspace/
└── hooks/hooks.json
```

## Configuração

O plugin lê a configuração específica do usuário em:

```
~/.claude/plugins/config/claude-for-legal/propriedade-intelectual/CLAUDE.md
```

Esse caminho sobrevive a atualizações do plugin. O `CLAUDE.md` que vem com o plugin é um template — é substituído a cada upgrade. A entrevista inicial escreve sua versão preenchida no caminho acima; daí em diante, edite esse arquivo diretamente quando algo mudar.

## Como aprende

Seu perfil de prática em `~/.claude/plugins/config/claude-for-legal/propriedade-intelectual/CLAUDE.md` não é estático — ele melhora conforme você usa o plugin. As skills avisam quando uma saída usou um default que você deveria ajustar. O agente `ip-renewal-watcher` monitora o registro de portfólio e levanta prazos de renovação na cadência que você escolher. Você pode re-rodar a entrevista, editar o arquivo diretamente ou pedir a uma skill que registre uma nova posição.

## Observações

- Toda skill lê o perfil de prática primeiro. Se encontrar placeholders, para e te orienta a rodar `/propriedade-intelectual:cold-start-interview`. Não há fallback genérico — postura de PI genérica é pior do que nenhuma.
- Enviar notificação extrajudicial inicia uma briga. A skill `/propriedade-intelectual:cease-desist` não envia nada sozinha; redige, mostra a entrada na matriz de aprovação e espera o aprovador.
- `/propriedade-intelectual:clearance` e `/propriedade-intelectual:fto-triage` são triagem **inicial**. A saída é um pacote de pesquisa para advogado(a), não parecer de viabilidade. A skill avisa em toda execução.
- `/propriedade-intelectual:oss-review` sinaliza obrigações e incompatibilidades de licença. Não decide uso comercial — engenharia e jurídico decidem juntos.
- Redação de reivindicações de patente está intencionalmente fora do escopo. Este plugin funciona ao lado de um(a) especialista em prossecução patentária; não substitui.
- **Remoção de conteúdo on-line:** o Brasil adota a regra do **art. 19 do Marco Civil** — provedores só respondem após **descumprimento de ordem judicial** específica. Notificação extrajudicial pode ser primeiro passo (boa-fé / construção de prova), mas o caminho típico para remoção é **medida judicial** (tutela de urgência, art. 300 CPC). Exceção: art. 21 (cenas de nudez/ato sexual sem consentimento) admite notificação extrajudicial direta. Direitos autorais, marca e segredo de empresa **seguem a regra do art. 19** — ordem judicial.
