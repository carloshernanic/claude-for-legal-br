> **Adaptação BR não oficial.** Este plugin é uma adaptação do `contencioso` (jurisdição EUA — FRCP/FRE/discovery amplo) para o contexto processual brasileiro (CPC — Lei 13.105/2015; CPP — DL 3.689/1941; jurisprudência STJ/STF/TST). Não é produto oficial de qualquer banca, tribunal ou da OAB. Equivalências entre institutos americanos e brasileiros são funcionais, não literais — em muitos casos não existe análogo direto (notadamente: discovery amplo, sigilo de tratativas estilo FRE 408, class action, júri cível). Todo conteúdo gerado é **rascunho para revisão por advogado(a) habilitado(a) na OAB**; não é parecer, não substitui profissional. Veja `references/terminology-us-to-br.md` para o mapeamento canônico.

# Plugin Contencioso (Litigation Counsel) — Adaptação BR

Suporte ao(à) advogado(a) interno(a) de contencioso na gestão de um portfólio de processos e contenciosos pré-judiciais. O cold-start captura a calibração de risco, o panorama de disputas e o estilo da casa — a moldura contra a qual cada caso é triado. A captação uniforme transforma novos casos em entradas estruturadas no log e em arquivos de histórico por matéria. Rollups de status e briefings de aprofundamento leem do log.

Construído para quem cuida de muitos casos ao mesmo tempo, a maioria conduzida por escritórios externos. O plugin é parceiro de raciocínio, não sistema de gestão de processos. Se você usa Legal One, Astrea, ADVBox, Projuris, SAJ Adv, ele não substitui — fica ao lado, como camada estruturada de raciocínio.

**Toda saída é rascunho para revisão do(a) advogado(a) — com citações, sinalizações e gates — não conclusão jurídica.** O plugin faz o trabalho: lê documentos, aplica o playbook, encontra issues, redige a peça. O(a) advogado(a) revisa, verifica e decide. Citações são marcadas por fonte para você saber quais vieram de pesquisa qualificada e quais precisam de conferência. Marcadores de sigilo profissional (EOAB art. 7º XIX; CPC art. 388) são aplicados conservadoramente para nada ser exposto por acidente. Ações consequentes — protocolar, enviar, executar — ficam atrás de confirmação explícita.

## Pré-requisitos

Algumas funcionalidades referenciam integrações de Gmail e tarefas agendadas. Elas exigem servidores MCP configurados no seu ambiente — não vêm empacotadas. Sem elas, as saídas são gravadas em arquivos para envio manual:

- **Gmail MCP** — `/oc-status` cria rascunhos no Gmail se autenticado; caso contrário grava drafts markdown em `oc-status/[YYYY-MM-DD]/[slug].md`.
- **MCP de tarefas agendadas** — não há agendamento automático embutido. Crie um lembrete recorrente de calendário para invocar os comandos semanais.

O plugin roda fim-a-fim sem nenhum dos dois; integrações são aditivas.

## Para quem é

| Papel | Uso primário |
|---|---|
| **Advogado(a) interno(a) de contencioso** | Tudo — intake, triagem, status, histórico, briefings |
| **Diretor(a) Jurídico(a) Adjunto(a) / Deputy GC** | Visão de portfólio, rollups para diretoria/Conselho |
| **Diretor(a) Jurídico(a) / GC** | Status rápido do portfólio, deep dive em qualquer caso |

## Primeira execução: cold-start

A entrevista de cold-start escreve o perfil *da casa* — persistente por matéria. Três pilares:

- **Calibração de risco** — apetite, faixas de materialidade, gatilhos de provisão/disclosure, alçada de acordo, perfil de seguros, matriz severidade-probabilidade
- **Panorama** — empresa, geografias, status regulado, padrões de disputa, adversários recorrentes, banco de escritórios externos, stakeholders internos
- **Estilo da casa** — formato de memo para Diretoria/Comitê de Auditoria, formato de memo de provisão, estilo de instrução a escritório externo, convenções de sigilo profissional, normas de escalonamento

Oferece padrões sensatos a cada passo (ex. matriz 3×3) e mantém tudo editável livremente. Se você ainda não tem um framework escrito, essa é a coisa que força a articulação.

```
/contencioso:cold-start-interview
```

Sua configuração fica em `~/.claude/plugins/config/claude-for-legal/contencioso/CLAUDE.md` e sobrevive a atualizações do plugin.

## Comandos

| Comando | Faz |
|---|---|
| `/contencioso:cold-start-interview` | Cold-start → grava o `~/.claude/plugins/config/claude-for-legal/contencioso/CLAUDE.md` da casa |
| `/contencioso:matter-intake` | Intake uniforme → grava `matters/[slug]/` + acrescenta ao `_log.yaml` |
| `/contencioso:portfolio-status` | Rollup de portfólio — distribuição de risco, prazos próximos, casos parados |
| `/contencioso:matter-briefing [slug]` | Briefing profundo sobre um caso — leitura pronta antes de reunião com GC ou escritório externo |
| `/contencioso:matter-update [slug]` | Acrescenta um evento datado ao histórico do caso; atualiza `last_updated` no log |
| `/contencioso:matter-close [slug]` | Arquiva caso para fora do portfólio ativo (retido, não deletado) |
| `/contencioso:demand-intake [title]` | Levantamento de contexto antes de redigir notificação extrajudicial (pagamento / inadimplemento / cessar-e-desistir / rescisão trabalhista / preservação) |
| `/contencioso:demand-draft [slug]` | Redige a notificação a partir do intake — passa pelo gate de sigilo profissional / tratativas, gera `.docx`, grava checklist pós-envio |
| `/contencioso:demand-received [path]` | Tria notificação extrajudicial recebida — análise de opções, cross-check no portfólio, handoff a matter/demand-intake |
| `/contencioso:subpoena-triage [path]` | Tria intimação/ofício/requisição judicial — classifica, escopo/ônus/sigilo, framework de impugnação, plano de cumprimento |
| `/contencioso:legal-hold [slug] [--issue/--refresh/--release/--status]` | Emite, renova, libera ou reporta deveres de preservação documental — gera `.docx` + atualiza log |
| `/contencioso:chronology [slug]` | Constrói ou atualiza cronologia a partir das fontes documentais declaradas + uploads — eventos etiquetados por relevância dentro da tese da matéria |
| `/contencioso:oc-status` | Redige e-mails semanais de pedido de status ao escritório externo por todo o portfólio; rascunhos Gmail se MCP disponível |
| `/contencioso:claim-chart` | Constrói ou revisa quadro de elementos — quadro de violação de patente (LPI Lei 9.279/1996) ou quadro de elementos cíveis (qualquer causa de pedir ou defesa) com detecção de lacunas |

## Skills

| Skill | Função |
|---|---|
| **cold-start-interview** | Perfil prático da casa — calibração de risco, panorama, estilo |
| **matter-intake** | Perguntas uniformes de intake; grava arquivo da matéria + linha do log |
| **portfolio-status** | Rollup a partir do log — risco, prazos, casos parados |
| **matter-briefing** | Leitura profunda de uma matéria a partir do arquivo + histórico |
| **matter-update** | Append estruturado de evento; atualiza `last_updated` no log |
| **matter-close** | Semântica de arquivamento; captura desfecho |
| **demand-intake** | Levantamento adaptativo de contexto para notificação extrajudicial — partes, fatos, leverage, filtros de sigilo |
| **demand-draft** | Gate de sigilo profissional / tratativas conciliatórias (CPC art. 166 §3º), depois redige `.docx` com placeholders `[CITE:___]`; grava checklist pós-envio; oferece criar matéria |
| **demand-received** | Tria notificação recebida — mérito, opções, cross-check no portfólio |
| **subpoena-triage** | Classifica intimação/ofício, analisa escopo/ônus/sigilo, produz framework de impugnação + plano de cumprimento |
| **legal-hold** | Emite / renova / libera / status de dever de preservação documental; grava notificação `.docx`; atualiza campos `legal_hold` do log |
| **chronology** | Extrai eventos datados das fontes documentais declaradas + uploads; deduplica; etiqueta significância conforme a tese |
| **oc-status** | Redator semanal de pedidos de status ao escritório externo em todo o portfólio; markdown + rascunhos Gmail |
| **claim-chart** | Quadro de violação de patente (LPI) ou quadro de elementos cíveis (qualquer causa de pedir ou defesa). Mapeamento elemento-por-elemento, cada célula com cite pinpoint, detecção de lacunas. Acompanha biblioteca de templates por causa de pedir. |

## Comandos interativos vs. agentes agendados

Os comandos acima rodam quando você invoca — para quando você está mexendo num caso. Os agentes abaixo rodam em agenda — para o que se move enquanto você não está olhando:

| Agente | O que monitora | Cadência padrão |
|---|---|---|
| **docket-watcher** | Andamento processual (PJe, e-SAJ, e-Proc, Projudi) das matérias do portfólio ativo — puxa novos atos, calcula prazos candidatos, cross-referencia com histórico e entregáveis | Semanal |

## Como os dados se organizam

```
contencioso/
├── CLAUDE.md                          # Perfil prático DA CASA — risco, panorama, estilo
├── matters/
│   ├── _log.yaml                      # Ledger do portfólio (uma entrada por matéria)
│   └── [matter-slug]/
│       ├── matter.md                  # intake + tese + posição da matéria
│       ├── history.md                 # log de eventos append-only
│       ├── chronology.md              # linha do tempo argumentativa (sob demanda)
│       └── legal-hold-v[N].docx       # notificações de preservação (emissão, renovação, liberação)
├── demand-letters/                    # notificações extrajudiciais enviadas
│   └── [slug]/
│       ├── intake.md
│       ├── draft-v1.docx
│       └── checklist.md
├── inbound/                           # notificações recebidas, intimações, ofícios, cartas de regulador
│   └── [slug]/
│       ├── incoming.[ext]
│       ├── triage.md
│       └── response-v1.docx           # se respondermos
└── oc-status/                         # rascunhos semanais de pedido de status ao externo
    └── [YYYY-MM-DD]/
        ├── _summary.md
        └── [slug].md                  # um e-mail por matéria
```

Pastas separadas porque cada uma tem fluxo distinto. Matérias entram no portfólio; notificações extrajudiciais e itens recebidos podem ou não virar matéria; rascunhos de status são artefatos periódicos. Quando se relacionam, o campo `related_matters` e cross-links em `matter.md` amarram.

O log é YAML porque é parseable pelas skills de rollup. Arquivos por matéria são markdown porque é onde você lê e edita. Ambos vivem em texto puro na pasta — nada proprietário.

## Conectores e verificação de citações

**Conecte uma ferramenta de pesquisa primeiro — as guardrails de citação dependem disso.** Sem conector, toda citação é etiquetada `[verify]` e a nota ao revisor acima do entregável registra que as fontes não foram verificadas. O plugin funciona dos dois jeitos; só faz mais verificação por você quando há ferramenta conectada.

Os conectores de pesquisa jurídica neste plugin não são meras fontes de dados — são a diferença entre uma citação verificada e uma citação que você precisa conferir. Uma citação obtida via **PJe / e-SAJ / e-Proc / Projudi** (sistemas oficiais do Judiciário, andamento e cópia integral), **JusBrasil** (busca de jurisprudência e DJE), **Lexml** (base oficial de legislação federal), **STF / STJ / TST** (sítios oficiais para acórdãos, súmulas, Temas de Repercussão Geral, Temas Repetitivos), ou **DJE/Push** (publicações em diários oficiais) é etiquetada com sua fonte e pode ser rastreada. Uma citação que vem do conhecimento do modelo ou de busca web é etiquetada `[verify]` ou `[verify-pinpoint]` e deve ser conferida contra fonte primária antes de qualquer uso. O plugin escalona as citações para que seu tempo de verificação vá onde importa.

> **Sobre precedentes qualificados.** O sistema brasileiro mistura civil law com efeito vinculante diferenciado para Súmulas Vinculantes (STF), acórdãos em controle concentrado, Temas de Repercussão Geral (STF), Temas Repetitivos (STJ), IRDR e IAC (CPC arts. 926–928 e 988). Cite-os com destaque quando aplicáveis — não trate jurisprudência ordinária de TJ/TRF/TRT como vinculante.

## Integrações

Acompanha o bucket geral de conectores em `.mcp.json`:

- **Slack** — busca mensagens, lê canais, encontra discussões
- **Google Drive** — busca, lê e baixa documentos

Projetado para ser útil mesmo sem nada conectado. Se/quando quiser puxar de eDiscovery brasileiro, sistemas CLM, ou e-mail, skills de integração podem ser adicionadas sem mudar a arquitetura central.

## Como aprende

Seu perfil prático em `~/.claude/plugins/config/claude-for-legal/contencioso/CLAUDE.md` não é estático — melhora com o uso. Skills te avisam quando uma saída usou um padrão que vale ajustar. Você pode rerodar o setup, editar o arquivo direto, ou pedir para uma skill registrar uma nova posição.

## Notas

- Toda skill lê primeiro `~/.claude/plugins/config/claude-for-legal/contencioso/CLAUDE.md`. Se mudar apetite de risco ou trocar de escritório externo, atualize — não maquie no caso individual.
- `## Perfil da empresa` é a primeira seção do `~/.claude/plugins/config/claude-for-legal/contencioso/CLAUDE.md` por convenção. Se você usa outros plugins `-legal`, dá para copiar entre eles em vez de reentrar o mesmo contexto.
- `_log.yaml` é a fonte da verdade do estado do portfólio. Mantenha limpo.
- Histórico da matéria é append-only. Se algo estava errado, registre a correção como nova entrada — não edite o passado.
- Matérias encerradas ficam em `_log.yaml` (histórico pesquisável). `/portfolio-status` as filtra do rollup ativo por padrão.

## Convenções de marcação inline

Três marcadores aparecem em saídas e rascunhos. Não são disclaimers — são itens de ação:

- `[CITE: citação específica necessária]` — placeholder de autoridade jurídica. Advogado(a) preenche ou confirma antes de enviar.
- `[VERIFY: fato específico]` — afirmação factual não confirmada na fonte. Advogado(a) verifica antes de relying.
- `[SME VERIFY: julgamento específico]` — um julgamento (leitura de mérito, etiqueta de significância, força de impugnação, status de sigilo) que pede revisão de especialista. SME = advogado(a) habilitado(a) na OAB qualificado(a) na área e jurisdição relevantes. Use generosamente — qualquer coisa pesada em julgamento carrega isto.

Um rascunho ou triagem com marcadores não resolvidos não é final, por mais polido que pareça.

## Testing & QA

