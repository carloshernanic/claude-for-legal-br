# Corporate Counsel Plugin

> Adaptação BR não oficial. Calibrado para Lei 6.404/1976, Código Civil, CVM, CADE, LGPD, Lei Anticorrupção. Veja `references/terminology-us-to-br.md`.

Fluxos de jurídico societário in-house em quatro áreas de prática: operações de M&A, conselho/secretaria societária, governança de companhia aberta e gestão de entidades (subsidiárias e filiais). Ative apenas os módulos aplicáveis ao seu papel. A entrevista de cold-start é modular — pergunta de forma direcionada por área ativa e grava no perfil de prática apenas as seções relevantes.

**Toda saída é rascunho para revisão por advogado(a) inscrito(a) na OAB — citado, sinalizado e com guard-rails — não é parecer jurídico.** O plugin faz o trabalho: lê os documentos, aplica o seu playbook, identifica os pontos relevantes, redige o memorando. O(a) advogado(a) revisa, valida e decide. Citações vêm marcadas por fonte para você saber quais vieram de ferramenta de pesquisa e quais precisam ser conferidas. Marcações de sigilo profissional (Art. 7º, XIX, Lei 8.906/94 — EOAB) são aplicadas de forma conservadora para que nada vaze por descuido. Ações consequentes — protocolar, enviar, assinar, levar a registro na Junta Comercial — só ocorrem após confirmação explícita.

## Para quem é

| Papel | Módulos ativos |
|---|---|
| **Advogado(a) interno(a) de M&A** | M&A |
| **Secretário(a) societário(a) / corporate secretary** | Conselho & Secretaria |
| **Diretor(a) Jurídico(a) de companhia aberta** | M&A + Companhia Aberta + Conselho & Secretaria |
| **Diretor(a) Jurídico(a) de companhia fechada** | M&A + Conselho & Secretaria + Gestão de Entidades |
| **Legal ops / GC solo** | O que se aplicar — combine livremente |

## Primeira execução

```
/societario:cold-start-interview
```

Faz a seleção de módulos e uma entrevista curta direcionada por área ativa. Grava um `~/.claude/plugins/config/claude-for-legal/societario/CLAUDE.md` modular contendo apenas as seções relevantes. Sua configuração fica armazenada nesse caminho e sobrevive a atualizações do plugin.

Configuração por operação (apenas módulo M&A):

```
/societario:cold-start-interview --new-deal
```

## Comandos

| Comando | O que faz |
|---|---|
| `/societario:cold-start-interview` | Cold-start modular, ou `--new-deal` / `--module [m&a \| board \| public \| entities]` |
| `/societario:diligence-issue-extraction [pasta]` | Lê documentos do data room (VDR), extrai pontos de atenção no formato da casa |
| `/societario:tabular-review` | Revisão tabular — uma linha por documento, uma coluna por campo, toda célula citada para a fonte, saída em Excel |
| `/societario:material-contract-schedule` | Anexo de contratos relevantes a partir dos achados de due diligence |
| `/societario:closing-checklist` | Checklist de fechamento — o que está travando, caminho crítico |
| `/societario:written-consent` | Deliberação por escrito (CC art. 1.072 §3º para Ltda.; Lei 6.404 art. 121 par. único para S.A. fechada) — minuta com base em precedente + tracker de signatários |
| `/societario:entity-compliance` | Tracker de compliance societário — inicialização, relatório, atualização, auditoria, exportação |
| `/societario:integration-management` | Plano de integração pós-fechamento, tracker de consentimentos (incluindo CADE), cessão de contratos, relatórios de status |
| `/societario:matter-workspace` | Gerenciamento de workspaces por matéria (apenas advocacia privada multicliente) — new, list, switch, close, none |

## Pré-requisitos

Vários recursos referenciam integrações com Slack, Google Drive, SharePoint, Box, Intralinks ou Datasite. Essas integrações exigem servidores MCP configurados no seu ambiente — **não vêm incluídas no plugin**. Sem elas, o plugin opera em modo offline (rascunhos gravados localmente em vez de postados em canal; trackers gravados em disco em vez de lidos de repositório conectado).

Configure servidores MCP em `.mcp.json` no nível do repositório ou do usuário. Skills e agents detectam o que está disponível em tempo de execução e ajustam o comportamento.

## Skills

| Skill | Módulo | Finalidade |
|---|---|---|
| **cold-start-interview** | Todos | Entrevista modular — ativa apenas as seções relevantes |
| **diligence-issue-extraction** | M&A | Documentos do VDR → pontos de atenção no formato da casa, por categoria (societário, tributário, trabalhista, regulatório, LGPD, anticorrupção, ambiental) |
| **tabular-review** | M&A | Revisa um conjunto de documentos contra schema tipado de colunas; células citadas; saída `.xlsx` / `.csv` / markdown; alimenta material-contract-schedule |
| **deal-team-summary** | M&A | Briefings em camadas: executivo / responsável pela operação / time de trabalho |
| **material-contract-schedule** | M&A | Anexo de declarações conforme definição no contrato de compra e venda de participação (SPA) |
| **closing-checklist** | M&A | Auto-atualizável: ingere dados da due diligence e da construção do anexo |
| **ai-tool-handoff** | M&A | Integração com Luminance/Kira — extração em massa + camada de QA |
| **board-minutes** | Conselho & Secretaria | Reuniões detectadas no calendário → minuta de ata no formato da casa |
| **written-consent** | Conselho & Secretaria | Deliberações por escrito com busca de precedentes no repositório; alerta de escopo para atos relevantes pontuais |
| **entity-compliance** | Gestão de Entidades | Tracker de calendário de compliance (YAML); prazos de arquivamento por entidade e Junta Comercial; auditoria de saúde; ingestão de relatórios de agente de registro; exportação CSV |
| **integration-management** | M&A | Tracker de integração pós-fechamento; plano em fases (Dia 1/30/90/180); tracker de Consentimentos Necessários com prazos do SPA; cessão de contratos em escala (repositório ou lista manual); relatórios semanais |
| **matter-workspace** | Cria, lista, alterna e encerra workspaces de matéria para prática multicliente; isola cada cliente/matéria para que o contexto não vaze entre eles |

*Skills de Companhia Aberta serão incluídas na próxima versão.*

## Comandos interativos vs. agents agendados

Os comandos acima rodam quando você os invoca — para quando você está trabalhando uma matéria. Os agents abaixo rodam em agenda — para o que se move enquanto você não está olhando:

| Agent | Módulo | O que observa | Cadência padrão |
|---|---|---|---|
| **dataroom-watcher** | M&A | VDR para novos uploads de documento; sinaliza uploads que combinam com categorias prioritárias; roda status do checklist de fechamento | Semanal |

## Integrações

**Conecte primeiro uma ferramenta de pesquisa — os guard-rails de citação dependem disso.** Sem ela, toda citação é marcada `[verify]` e a nota ao revisor acima de cada entregável registra que as fontes não foram verificadas. As skills funcionam de qualquer forma; uma ferramenta de pesquisa apenas tira o trabalho de verificação das suas costas.

Já vem com:

- **Slack** — busca de mensagens, leitura de canais, localização de discussões (bucket geral)
- **Google Drive** — busca, leitura e download de documentos (bucket geral)
- **Box** — data room e gestão de documentos

Intralinks, Datasite e outros conectores de VDR podem ser adicionados em `.mcp.json` quando as URLs do parceiro estiverem disponíveis.

## Como aprende

Seu perfil de prática em `~/.claude/plugins/config/claude-for-legal/societario/CLAUDE.md` não é estático — ele evolui conforme você usa o plugin. As skills te avisam quando uma saída usou um padrão que vale ajustar. Você pode rodar o setup de novo, editar o arquivo direto, ou instruir a skill a registrar uma nova posição.

## Notas de M&A

- A extração de pontos de atenção aplica thresholds de materialidade — não lê todo documento se o threshold define top N por valor.
- Buy-side e sell-side são ambos suportados. O Perfil de Prática captura qual lado se aplica nesta operação; as skills ajustam a postura conforme.
- O handoff com ferramenta de IA (Luminance/Kira) é opcional. Se o `~/.claude/plugins/config/claude-for-legal/societario/CLAUDE.md` indicar que não há ferramenta, toda extração roda pela skill direta.
- O checklist de fechamento é inicializado a partir do contrato de compra e venda (SPA), e se auto-atualiza conforme a due diligence revela consentimentos necessários (incluindo notificação ao CADE quando aplicável — Lei 12.529/2011, thresholds de R$ 750 mi e R$ 75 mi).
- Due diligence brasileira inclui obrigatoriamente: societária (Lei 6.404 + CC), trabalhista (CLT + previdenciário), tributária (RFB + ICMS estadual + ISS municipal), ambiental (Lei 6.938/81 + licenças), regulatória setorial, LGPD (Lei 13.709/2018), anticorrupção (Lei 12.846/2013) e arquivamento na Junta Comercial competente.
