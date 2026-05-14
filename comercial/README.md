> **Adaptação BR não oficial.** Este plugin é uma adaptação independente do `comercial` (jurisdição EUA) para o contexto contratual brasileiro: Código Civil (Lei 10.406/2002), CDC (Lei 8.078/1990) quando aplicável, Lei de Arbitragem (Lei 9.307/1996), LINDB (Decreto-Lei 4.657/1942) e MP 2.200-2/2001 (ICP-Brasil). Não é produto oficial da Anthropic nem dos autores originais. Todo conteúdo gerado é **rascunho para revisão por advogado(a) inscrito(a) na OAB** — não é parecer, não é conclusão jurídica, não substitui profissional habilitado.

# Plugin Commercial Counsel (BR)

Fluxos de trabalho de contratos comerciais para departamento jurídico interno: revisão de contrato com fornecedor, triagem de NDA, revisão de SaaS / contrato-quadro, controle de renovações, roteamento de escalonamento e resumos para áreas de negócio. Estruturado em torno de um perfil de prática do time, escrito por uma entrevista de cold-start — o plugin aprende *o seu* playbook, não um genérico.

**Toda saída é rascunho para revisão por advogado(a) — citada, sinalizada e gateada — não é conclusão jurídica.** O plugin faz o trabalho: lê os documentos, aplica o playbook, encontra os pontos sensíveis, redige o memorando. Um(a) advogado(a) revisa, valida e decide. As citações são tagueadas pela fonte para você saber quais vieram de ferramenta de pesquisa e quais precisam de checagem. Marcadores de sigilo profissional (art. 7º XIX EOAB) são aplicados de modo conservador para nada vazar sem querer. Ações consequentes — protocolar, enviar, executar — ficam atrás de confirmação explícita.

## Para quem é

| Papel | Fluxos principais |
|---|---|
| **Advogado(a) de contratos** | Revisão de contratos com fornecedor, escalonamento, resumos para negócio |
| **Coordenador(a) de contratos / paralegal** | Triagem de NDA, controle de renovação, revisão de primeira passada |
| **Suprimentos / Compras** | Acompanhamento de renovações; consome resumos para negócio |
| **Comercial / BD** | Triagem de NDA self-service antes de pingar o jurídico |

## Primeira execução: entrevista de cold-start

Na primeira utilização, o plugin entrevista você — dez minutos, conversacional — para entender como o time realmente opera. Ele pergunta sobre as posições do seu playbook, regras de escalonamento e o que faz o time bufar quando cai na mesa. Depois pede 5–10 contratos assinados recentes (mais é melhor; 20 dá padrão mais claro) para enxergar suas posições aplicadas.

Ele grava o que aprende em `~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md` — um documento em português claro sobre o seu time que toda outra skill lê antes de qualquer coisa. Você edita o documento, não um arquivo de configuração.

```
/comercial:cold-start-interview
```

**Lado do playbook.** Cedo no setup, será perguntado se deve construir um playbook **vendedor** (você vende seu produto/serviço; você é o fornecedor; geralmente o seu papel), um playbook **comprador** (você compra de terceiros; você é o cliente; geralmente papel do outro lado) ou ambos. A resposta inverte quase toda posição do playbook — limite de responsabilidade, direção da indenização, direitos de rescisão, titularidade de PI — então importa logo no início. Se escolher ambos, o setup constrói o vendedor primeiro; rode `/comercial:cold-start-interview --side comprador` depois para construir o outro. A configuração mantém os dois em paralelo, e as skills de revisão checam qual lado se aplica antes de ler o playbook.

**Distinção crítica B2B vs consumo.** O playbook diferencia contratos B2B (regidos pelo Código Civil) de contratos de consumo (regidos pelo CDC). Em consumo, cláusulas leoninas listadas no art. 51 do CDC são **nulas de pleno direito** — limitação de responsabilidade do fornecedor (inciso I), renúncia a direitos (inciso I), eleição de foro abusiva (inciso IV, lido com CPC art. 63 §3º), arbitragem compulsória (inciso VII). Toda revisão pergunta: "B2C? aplica CDC, cláusulas leoninas nulas (art. 51)" antes de mirar no playbook B2B.

## Comandos

| Comando | Faz |
|---|---|
| `/comercial:cold-start-interview` | Roda (ou re-roda) a entrevista de cold-start |
| `/comercial:review [arquivo]` | Revisa contrato com fornecedor, NDA ou SaaS contra o playbook |
| `/comercial:renewal-tracker` | O que renova nos próximos 90 dias e quando vencem as janelas de denúncia |
| `/comercial:escalation-flagger` | Roteia uma questão para o aprovador certo e redige o pedido |
| `/comercial:amendment-history [arquivo(s)]` | Traça como o contrato mudou entre o contrato-base e seus aditivos |
| `/comercial:review-proposals` | Percorre propostas pendentes de atualização do playbook do agente monitor |
| `/comercial:matter-workspace` | Gerencia workspaces de matéria (somente advocacia multi-cliente) — novo, listar, alternar, fechar, nenhum |

## Skills

| Skill | Propósito |
|---|---|
| **cold-start-interview** | Entrevista de primeira execução que grava `~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md` |
| **vendor-agreement-review** | Análise de desvios completa playbook-vs-contrato com sugestões de redação |
| **nda-review** | Triagem rápida VERDE/AMARELO/VERMELHO para o jurídico só ler os NDAs que precisam |
| **saas-msa-review** | Overlay específico de assinatura: renovação automática, reajuste de preço, portabilidade de dados, SLAs |
| **renewal-tracker** | Registro de janelas de denúncia, mostra o que está chegando |
| **escalation-flagger** | Casa questões com a matriz de escalonamento, redige o pedido ao aprovador |
| **stakeholder-summary** | Tradução em dois parágrafos da revisão jurídica para a área de negócio |
| **amendment-history** | Resume mudanças entre contrato-base e aditivos, ou traça uma cláusula específica até a redação vigente |
| **matter-workspace** | Cria, lista, alterna e fecha workspaces de matéria para advocacia multi-cliente; isola cada cliente/matéria para que contexto não vaze |

## Comandos interativos vs. agentes agendados

Os comandos acima rodam quando você invoca — para quando você está trabalhando uma matéria. Os agentes abaixo rodam por agenda — para o que se move enquanto você não está olhando:

| Agente | O que monitora | Cadência padrão |
|---|---|---|
| **renewal-watcher** | Registro de renovação — posta o que vem nos próximos 90 dias, com escalonamento bandeira-vermelha para janelas de denúncia em 0–13 dias | Semanal (segunda) |
| **deal-debrief** | Contratos assinados recentemente quanto a desvios de playbook; provoca o(a) advogado(a) a registrar contexto enquanto a memória está fresca | Semanal (segunda) |
| **playbook-monitor** | Log de desvios — propõe atualizações ao playbook quando uma cláusula foi sobreposta 5+ vezes numa janela móvel de 12 meses | Disparado por dados (após cada deal-debrief) |

## Integrações

**Conecte uma ferramenta de pesquisa primeiro — as guardrails de citação dependem dela.** Sem ela, toda citação é taggeada `[verificar]` e a nota de revisor acima de cada entregável registra que fontes não foram verificadas. As skills funcionam dos dois jeitos; uma ferramenta de pesquisa apenas tira o trabalho de verificação de cima de você.

Vem com conectores configurados em `.mcp.json`:

- **CLM** — gestão de ciclo de vida de contrato (Ironclad, Docusign CLM, Linksquares, Conga, ou alternativas BR como Lawnix, Linte, Signer)
- **Assinatura eletrônica** — DocuSign (integra ICP-Brasil), Adobe Sign; alternativas brasileiras: Clicksign, D4Sign, ZapSign, Autentique
- **Slack** — busca mensagens, lê canais, encontra discussões (bucket geral)
- **Google Drive** — busca, lê e baixa documentos (bucket geral)

Com um [CLM] conectado: as revisões checam por contratos anteriores com a mesma contraparte, carregam em massa o registro de renovação, criam registros com o memo de revisão anexado.

Com plataforma de assinatura conectada: rastreia status de assinatura, encaminha envelopes em ordem de aprovação. Para validade jurídica plena perante terceiros e cartórios, prefira assinatura **qualificada** com certificado ICP-Brasil (MP 2.200-2/2001 art. 10 §1º); avançada e simples têm presunção condicionada (Lei 14.063/2020).

## Início rápido

### 1. Seja entrevistado

```
/comercial:cold-start-interview
```

Dez minutos. Tenha 5–10 contratos assinados recentes em mãos (mais é melhor; 20 dá padrão mais claro).

Sua configuração fica em `~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md` e sobrevive a atualizações do plugin.

### 2. Revise um contrato

```
/comercial:review contrato-fornecedor.pdf
```

Saída: memo desvio-a-desvio contra o seu playbook, com sugestão específica de redação e aprovador nominado.

### 3. Veja o que está renovando

```
/comercial:renewal-tracker
```

Saída: tudo com janela de denúncia nos próximos 90 dias, agrupado por urgência.

## Como aprende

Seu perfil de prática em `~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md` não é estático — melhora à medida que você usa o plugin. As skills te avisam quando uma saída usou um padrão que você devia calibrar. O agente `playbook-monitor` propõe atualizações quando a prática diverge do playbook. Você pode re-rodar o setup, editar o arquivo direto ou dizer a uma skill que registre uma nova posição.

## Estrutura de arquivos

```
comercial/
├── .claude-plugin/plugin.json
├── .mcp.json
├── CLAUDE.md                    # Seu perfil de prática — escrito pelo cold-start, editado por você
├── README.md
├── agents/
│   ├── renewal-watcher.md
│   ├── deal-debrief.md
│   └── playbook-monitor.md
├── skills/
│   ├── cold-start-interview/
│   ├── review/
│   ├── review-proposals/
│   ├── vendor-agreement-review/
│   ├── nda-review/
│   ├── saas-msa-review/
│   ├── renewal-tracker/
│   │   └── references/renewal-register.yaml
│   ├── escalation-flagger/
│   ├── amendment-history/
│   ├── matter-workspace/
│   └── stakeholder-summary/
└── hooks/hooks.json
```

## Notas

- O plugin presume que você é o **cliente** na maioria das revisões. Quando você for o fornecedor, sinalize e a revisão inverte a polaridade do playbook.
- Triagem de NDA é construída para self-service por não-advogados. VERDE significa "encaminhar para assinatura". Não negocia.
- Controle de renovação só sabe de contratos que passaram por este plugin ou foram carregados em massa do [CLM]. Contratos assinados antes da instalação precisam de uma varredura única.
- **Lei aplicável e foro.** Contratos internos (ambas as partes no Brasil) são regidos pelo direito brasileiro — LINDB art. 9º limita derrogação ampla. Contratos internacionais admitem eleição de lei estrangeira com cautelas (jurisprudência STJ permissiva; Convenção do México 1994 não foi ratificada). Eleição de foro segue CPC art. 63; é restrita em contrato de adesão e relação de consumo (CPC art. 63 §3º; CDC art. 51, IV).
- **Arbitragem.** Cláusula compromissória rege-se pela Lei 9.307/1996. Câmaras de uso frequente no Brasil: CAM-CCBC, CAM B3, CBMA, CMA-FGV. Em consumo, arbitragem compulsória é inadmissível (CDC art. 51, VII; STJ REsp 1.169.841).
- **Idioma.** Contrato assinado no Brasil que precise ser registrado em cartório ou produzir efeitos perante terceiros deve estar em português, ou acompanhar tradução juramentada (Decreto 13.609/1943).
- **Sigilo conciliatório.** Propostas conciliatórias só são juridicamente protegidas quando feitas em **mediação ou conciliação** (CPC art. 166 §3º). Fora desse contexto, uma proposta de acordo pode ser usada como prova — diferentemente do FRE 408 dos EUA, não há sigilo automático em negociação direta. Sinalize ao redigir notificação extrajudicial.
