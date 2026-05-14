# Claude for Legal — Brasil

Adaptação para o contexto jurídico brasileiro do repositório [`anthropics/claude-for-legal`](https://github.com/anthropics/claude-for-legal) — agentes, skills e conectores para os workflows jurídicos mais comuns: contratos, privacidade (LGPD), product counsel, societário, trabalhista (CLT), contencioso, regulatório, governança de IA, propriedade intelectual (INPI/Lei 9.279), e o lado acadêmico (NPJ e estudantes).

> **Não é um produto oficial da Anthropic.** É uma adaptação derivada do upstream open-source `claude-for-legal` (Apache 2.0), com substituição do framework jurídico US → BR. Veja `references/terminology-us-to-br.md` para o mapeamento de institutos.

> **Novo aqui?** Comece pelo [QUICKSTART.md](QUICKSTART.md) — instalação em 60 segundos.

Tudo aqui está disponível **de duas formas a partir de uma só fonte**: como plugin do [Claude Cowork](https://claude.com/product/cowork) ou [Claude Code](https://claude.com/product/claude-code), ou via [Claude Managed Agents API](https://docs.claude.com/en/api/managed-agents) por trás do seu próprio orquestrador. Mesmo system prompt, mesmas skills — você escolhe onde roda.

## Aviso jurídico essencial

> **Todo output destes plugins é rascunho para revisão por advogado(a) inscrito(a) na OAB — não é parecer jurídico, não é conclusão legal, não substitui profissional habilitado(a).** Os plugins foram construídos com guardrails que refletem isso: atribuição de fonte em cada citação, postura conservadora em juízos subjetivos, suposições de jurisdição evidenciadas e travas explícitas antes de qualquer protocolo, envio ou ato de relevância. Cabe ao(à) advogado(a) revisar, validar e assumir responsabilidade profissional pelo produto final. Os plugins aceleram essa revisão; não a substituem.
>
> **Estes plugins não representam posições jurídicas oficiais nem da Anthropic nem dos mantenedores desta adaptação.** Onde uma skill traz checklist, framework, alerta de risco ou caracterização de jurisprudência ou normativos, isso é insumo para a análise do(a) advogado(a) revisor(a), não declaração de uma "posição" sobre o direito. O direito brasileiro em muitas dessas áreas (LGPD, IA, terceirização, marco regulatório de plataformas, reforma tributária) ainda está em consolidação — quem assume a posição jurídica adotada na peça é o(a) advogado(a), não o plugin.

O que tem no repositório:

- **Plugins por área de prática** cobrindo trabalho interno, banca e acadêmico — cada um construído em torno de uma entrevista de setup (cold-start) que aprende o playbook do escritório e um `CLAUDE.md` de perfil que todas as skills consultam.
- **Cookbooks de Managed Agents** para os workflows agendados (renewal-watcher, docket-watcher-PJe, monitor regulatório de DOU, diligence grid, launch radar).
- **Conectores MCP** de produtividade geral (Slack, Google Drive, Box) e mapeamento de conectores BR desejados (PJe, e-SAJ, JusBrasil, INPI, DOU, CVM, ANPD, BCB — ver [CONNECTORS.md](CONNECTORS.md)).
- **[Agentes nomeados](#agentes)** — workflows ponta-a-ponta com nome funcional e comando único: Revisor de Contrato de Fornecedor, Respondedor de Requisição LGPD, Revisor de Rescisão Trabalhista, Construtor de Matriz de Fundamentação, ...

## Agentes

Cada agente é nomeado pelo workflow que executa. São a superfície mais comum — comece pelos que combinam com seu trabalho, depois afine a skill, o perfil de prática e os conectores ao seu jeito.

| Agente | O que faz | Plugin | Comando |
|---|---|---|---|
| **Revisor de Contrato de Fornecedor** | Revisa contrato (MSA) contra seu playbook e produz memo com redline | `comercial` | `/comercial:review` |
| **Triador de NDA** | Triagem VERDE/AMARELO/VERMELHO de NDAs recebidas para que só os difíceis cheguem ao(à) advogado(a) | `comercial` | `/comercial:review` |
| **Rastreador de Aditivos** | Rastreia como o contrato mudou entre base e cada aditivo | `comercial` | `/comercial:amendment-history` |
| **Vigia de Renovação** | Varre a base de contratos por prazos de denúncia e renovação | `comercial` | agente agendado |
| **Debrief de Fechamento** | Varredura semanal de contratos assinados com desvios de playbook | `comercial` | agente agendado |
| **Monitor de Playbook** | Vigia o log de desvios e propõe atualizações de playbook | `comercial` | agente agendado |
| **Roteador de Escalação** | Roteia questões contratuais ao aprovador certo e redige o pedido | `comercial` | `/comercial:escalation-flagger` |
| **Revisão Tabular de Due Diligence** | Revisão tabular em data room — uma linha por documento, cada célula citada | `societario` | `/societario:tabular-review` |
| **Extrator de Issues** | Lê documentos do VDR e extrai issues por categorias e thresholds da casa | `societario` | `/societario:diligence-issue-extraction` |
| **Redator de Deliberação Societária** | Redige deliberações por escrito (Ltda. art. 1.072 §3º CC; S.A. fechada conforme Lei 6.404) em formato da casa | `societario` | `/societario:written-consent` |
| **Construtor de Quadro de Contratos Relevantes** | Monta o quadro de exceções a partir dos achados de DD contra o threshold do contrato de compra | `societario` | `/societario:material-contract-schedule` |
| **Rastreador de Obrigações Societárias** | Calcula prazos de cumprimento (Junta Comercial, CVM, BCB) por tipo e jurisdição da entidade | `societario` | `/societario:entity-compliance` |
| **Condutor de Checklist de Fechamento** | Acompanha cada condição precedente, consentimento, documento e arquivamento bloqueando o closing | `societario` | `/societario:closing-checklist` |
| **Plano de Integração Pós-fechamento** | Plano faseado com tracking de consentimentos e status semanal | `societario` | `/societario:integration-management` |
| **Vigia de Data Room** | Monitora uploads no VDR e posta status do checklist no agendamento | `societario` | agente agendado |
| **Revisor de Rescisão Trabalhista** | Roda uma rescisão proposta contra flags de risco CLT/jurisprudência TST | `trabalhista` | `/trabalhista:termination-review` |
| **Revisor de Admissão** | Revisa proposta/contrato de trabalho e cláusulas restritivas com checagem de jurisdição | `trabalhista` | `/trabalhista:hiring-review` |
| **Triador de Vínculo (PJ vs CLT vs terceirização)** | Testa o engajamento proposto contra art. 3º CLT, Súmula 331 TST e Lei 13.429 | `trabalhista` | `/trabalhista:worker-classification` |
| **Monitor de Licenças e Afastamentos** | Monitora licenças (maternidade, paternidade, saúde, estabilidades) com prazos CLT/INSS | `trabalhista` | agente agendado |
| **Condutor de Investigação Interna** | Abre, atualiza, alimenta e resume investigações internas (assédio, conduta) | `trabalhista` | `/trabalhista:investigation-open` |
| **Redator de Política de RH** | Redige políticas trabalhistas com suplementos de acordo/convenção coletiva onde aplicável | `trabalhista` | `/trabalhista:policy-drafting` |
| **Planejador de Expansão Internacional** | Inicia planejamento PJ/CLT no exterior ou contratação via PEO/EOR e briefing para advogado(a) local | `trabalhista` | `/trabalhista:expansion-kickoff` |
| **Q&A Trabalhista** | Q&A trabalhista jurisdição-aware para o canal de "pergunta rápida" | `trabalhista` | `/trabalhista:wage-hour-qa` |
| **Respondedor de Requisição LGPD** | Redige acuse de recebimento e resposta substantiva a pedidos de titular dentro dos prazos LGPD (art. 19, §1º — 15 dias) | `privacidade` | `/privacidade:dsar-response` |
| **Revisor de Contrato de Tratamento (DPA)** | Revisa contrato de tratamento como controlador ou operador (LGPD art. 39) | `privacidade` | `/privacidade:dpa-review` |
| **Gerador de RIPD** | Gera Relatório de Impacto à Proteção de Dados em formato da casa (LGPD art. 38) | `privacidade` | `/privacidade:pia-generation` |
| **Triador de Privacidade** | Decide se a atividade exige RIPD, simples documentação interna ou pode prosseguir | `privacidade` | `/privacidade:use-case-triage` |
| **Gap Checker Regulatório (Privacidade)** | Diffa um normativo novo ou alterado (Resolução ANPD) contra políticas e práticas atuais | `privacidade` | `/privacidade:reg-gap-analysis` |
| **Monitor de Política de Privacidade** | Varre RIPDs salvos, revisões de DPA e triagens em busca de drift | `privacidade` | `/privacidade:policy-monitor` |
| **Revisor de Lançamento** | Revisa lançamento de produto contra sua calibragem de risco (LGPD, CDC, regulatório setorial) | `produto` | `/produto:launch-review` |
| **Checador de Alegações Publicitárias** | Sinaliza copy que precisa de comprovação, reformulação ou corte (CDC arts. 30/37/38, CONAR) | `produto` | `/produto:marketing-claims-review` |
| **Triagem "isso é problema?"** | Resposta rápida para a pergunta no Slack — pattern-match contra sua calibragem | `produto` | `/produto:is-this-a-problem` |
| **Vigia de Lançamentos** | Vigia o tracker de lançamentos por features que precisam de revisão jurídica | `produto` | agente agendado |
| **Vigia de DOU/Agências** | Monitora feeds regulatórios (DOU, ANVISA, BCB, CVM, ANPD, CADE...) e escreve o digest da segunda | `regulatorio` | agente agendado |
| **Checagem Regulatória sob demanda** | Checa feeds agora e reporta o que mudou desde a última varredura | `regulatorio` | `/regulatorio:reg-feed-watcher` |
| **Diff de Política** | Diffa uma mudança regulatória contra a biblioteca indexada de políticas | `regulatorio` | `/regulatorio:policy-diff` |
| **Rastreador de Gaps** | Tracker de gaps abertos — o que está sinalizado e ainda não fechado | `regulatorio` | `/regulatorio:gaps` |
| **Re-redator de Política** | Redraft marcado de política para fechar um gap — proposta para o(a) dono(a), não edit direto | `regulatorio` | `/regulatorio:policy-redraft` |
| **Tracker de Consultas Públicas** | Revisa consultas públicas abertas (ANPD, BCB, CVM, ANATEL...), registra decisões, monitora prazos | `regulatorio` | `/regulatorio:comments` |
| **Triador de Caso de Uso de IA** | Classifica casos de uso propostos de IA contra seu registro (LGPD art. 20; PL 2338/2023) | `governanca-ia` | `/governanca-ia:use-case-triage` |
| **Avaliador de Impacto de IA (RIPD-IA)** | Roda avaliação de impacto contra os regimes em escopo (LGPD + PL 2338 + setorial) | `governanca-ia` | `/governanca-ia:aia-generation` |
| **Revisor de Fornecedor de IA** | Revisa termos de fornecedor de IA quanto a treinamento em dados, responsabilidade, mudança de modelo e gaps de política | `governanca-ia` | `/governanca-ia:vendor-ai-review` |
| **Gap Checker Regulatório (IA)** | Diffa um normativo novo de IA contra sua postura atual de governança | `governanca-ia` | `/governanca-ia:reg-gap-analysis` |
| **Monitor de Política de IA** | Varre RIPDs-IA, triagens e revisões de fornecedor para drift de política | `governanca-ia` | `/governanca-ia:policy-monitor` |
| **Triador de Anterioridade de Marca** | Primeira pesquisa de anterioridade no INPI com knockout e heurística de confusão (Lei 9.279 art. 124) | `propriedade-intelectual` | `/propriedade-intelectual:clearance` |
| **Redator de Notificação Extrajudicial** | Redige ou faz triagem de notificação extrajudicial, calibrada à postura de enforcement do escritório | `propriedade-intelectual` | `/propriedade-intelectual:cease-desist` |
| **Pedido de Remoção (Marco Civil)** | Redige pedido de remoção a plataforma (judicial — Lei 12.965/2014 art. 19; ou extrajudicial para nudez não consentida — art. 21) | `propriedade-intelectual` | `/propriedade-intelectual:takedown` |
| **Checador de Compliance Open Source** | Classifica licenças open source contra seu modelo de implantação | `propriedade-intelectual` | `/propriedade-intelectual:oss-review` |
| **Triador de FTO** | Primeira leitura estruturada de patentes potencialmente bloqueantes — triagem, não opinião FTO | `propriedade-intelectual` | `/propriedade-intelectual:fto-triage` |
| **Triador de Infração** | Triagem entre Marca / Direitos Autorais / Patente / Segredo de empresa — fatores, não veredito | `propriedade-intelectual` | `/propriedade-intelectual:infringement-triage` |
| **Revisor de Cláusula de PI** | Revisa cláusulas de cessão, titularidade, licenciamento, garantias e indenização | `propriedade-intelectual` | `/propriedade-intelectual:ip-clause-review` |
| **Acompanhamento de Portfólio de PI** | Registros, renovações, anuidades INPI, declarações de uso | `propriedade-intelectual` | `/propriedade-intelectual:portfolio` |
| **Vigia de Anuidades INPI** | Relatório agendado de prazos do portfólio | `propriedade-intelectual` | agente agendado |
| **Construtor de Matriz de Fundamentação** | Matriz elemento-a-elemento, patente ou causa de pedir cível | `contencioso` | `/contencioso:claim-chart` |
| **Vigia de Andamentos (PJe)** | Monitora andamentos processuais e prazos | `contencioso` | agente agendado |
| **Redator de Notificação Extrajudicial (cível)** | Redige notificação extrajudicial com cuidado sobre sigilo de tratativas e trava de envio | `contencioso` | `/contencioso:demand-draft` |
| **Pré-redação de Notificação** | Coleta de contexto antes da redação — partes, fatos, fundamentos, leverage | `contencioso` | `/contencioso:demand-intake` |
| **Triagem de Notificação Recebida** | Triagem de notificação recebida — opções, cross-check de portfólio, handoff | `contencioso` | `/contencioso:demand-received` |
| **Triagem de Intimação/Requisição** | Classifica, escopa e planeja cumprimento de intimação/requisição judicial | `contencioso` | `/contencioso:subpoena-triage` |
| **Construtor de Cronologia** | Monta ou atualiza cronologia a partir de fontes declaradas e uploads | `contencioso` | `/contencioso:chronology` |
| **Preparação para Oitiva** | Constrói roteiro de oitiva (depoimento pessoal, testemunha) ligado à teoria do caso | `contencioso` | `/contencioso:deposition-prep` |
| **Redator de Seção de Petição** | Redige seção em estilo da casa, consistente com a tese | `contencioso` | `/contencioso:brief-section-drafter` |
| **Revisor de Lista de Privilégio/Sigilo** | Primeira passagem em lista de documentos com sigilo profissional (art. 7º XIX EOAB; CPC art. 388) | `contencioso` | `/contencioso:privilege-log-review` |
| **Hold Documental** | Emite, renova, libera ou reporta dever de preservação documental | `contencioso` | `/contencioso:legal-hold` |
| **Intake de Processo** | Intake uniforme para processo novo — escreve matter.md, history.md, apenda ao log | `contencioso` | `/contencioso:matter-intake` |
| **Briefing de Processo** | Briefing aprofundado de um processo — pronto para call com diretor jurídico ou banca externa | `contencioso` | `/contencioso:matter-briefing` |
| **Status de Portfólio** | Distribuição de risco, prazos próximos, processos parados | `contencioso` | `/contencioso:portfolio-status` |
| **Status de Banca Externa** | Gera draft semanal de pedidos de status para o portfólio ativo | `contencioso` | `/contencioso:oc-status` |
| **Intake de Assistido (NPJ)** | Intake estruturado de assistido(a) com cross-area issue spotting e flags de conflito | `npj` | `/npj:client-intake` |
| **Memorando IRAC/RFD** | Memo analítico com estrutura IRAC ou relatório-fundamentos-dispositivo, com gaps sinalizados | `npj` | `/npj:memo` |
| **Roadmap de Pesquisa** | Leis a checar, jurisprudência por tribunal, termos de busca — leads, não citações | `npj` | `/npj:research-start` |
| **Controle de Prazos do NPJ** | Adiciona, reporta, atualiza e fecha prazos com alertas malpractice-aware | `npj` | `/npj:deadlines` |
| **Sumarizador de Status de Caso** | Status por audiência — assistido, professor(a), ou pronto-para-tribunal | `npj` | `/npj:status` |
| **Redator de Carta ao Assistido** | Correspondência rotineira — confirmação de atendimento, pedido de documento, atualização | `npj` | `/npj:client-letter` |
| **Onboarding de Estagiários** | Ramp semestral — procedimentos do NPJ, walkthrough de ferramentas, exercícios | `npj` | `/npj:ramp` |
| **Handoff de Semestre** | Memos de transferência ao fim do semestre — espelho do ramp | `npj` | `/npj:semester-handoff` |
| **Fila de Revisão do(a) Supervisor(a)** | Fila do(a) professor(a) supervisor(a) | `npj` | `/npj:supervisor-review-queue` |
| **Coach de Exame de Ordem** | Prática focada em MPU/objetivas/peça da OAB com priorização por matéria fraca | `estudante-direito` | `/estudante-direito:bar-prep-questions` |
| **Sargento Socrático** | Ele(a) pergunta, você responde, ele(a) contra-argumenta — não dá a resposta | `estudante-direito` | `/estudante-direito:socratic-drill` |
| **Corretor IRAC/RFD** | Corrige redação na estrutura IRAC (peça norte-americana) ou relatório-fundamentos-dispositivo (peça BR) | `estudante-direito` | `/estudante-direito:irac-practice` |
| **Fichador de Julgados** | Faz fichamento de acórdão/decisão no seu formato | `estudante-direito` | `/estudante-direito:case-brief` |
| **Construtor de Esquema** | Monta ou amplia esquema a partir de aulas e doutrina | `estudante-direito` | `/estudante-direito:outline-builder` |
| **Preparo para Cold Call** | Prevê perguntas do(a) professor(a) e treina antes da aula | `estudante-direito` | `/estudante-direito:cold-call-prep` |
| **Previsor de Prova** | Analisa provas anteriores do(a) professor(a) e prevê ênfases | `estudante-direito` | `/estudante-direito:exam-forecast` |
| **Crítico de Escrita Jurídica** | Feedback estrutural — nunca reescreve por você | `estudante-direito` | `/estudante-direito:legal-writing` |
| **Mestre de Flashcards** | Gera ou treina flashcards em buckets Leitner | `estudante-direito` | `/estudante-direito:flashcards` |
| **Planejador de Estudo** | Plano de longo prazo com sessões agendadas, adaptado pelo histórico | `estudante-direito` | `/estudante-direito:study-plan` |
| **Buscador no Registro de Skills** | Busca skills jurídicas comunitárias em registros monitorados | `builder-hub` | `/builder-hub:registry-browser` |
| **Instalador de Skill** | Instala skill comunitária com checagens de trust e QA | `builder-hub` | `/builder-hub:skill-installer` |
| **QA de Skill** | Avalia uma skill contra o Legal Skill Design Framework | `builder-hub` | `/builder-hub:skills-qa` |
| **Recomendador de Skill Comunitária** | Sugere skills comunitárias com base em atividade recente em outros plugins | `builder-hub` | `/builder-hub:related-skills-surfacer` |
| **Atualizador de Skill Comunitária** | Checa atualizações em skills comunitárias instaladas | `builder-hub` | `/builder-hub:auto-updater` |
| **Sync de Registro** | Check periódico de registros monitorados | `builder-hub` | agente agendado |

Para deploy via Managed Agent — `agent.yaml`, leaf-worker subagents, exemplos de steering-event e notas de segurança por agente — veja **[receitas-agentes/](./receitas-agentes)**.

## Layout do Repositório

```
comercial/         # contratos — fornecedor/NDA/SaaS, renovações, escalações
societario/          # M&A DD, checklists de fechamento, atos societários, compliance societário
trabalhista/         # admissão/rescisão, vínculo (CLT/PJ), licenças, investigações
privacidade/            # LGPD — DPA, RIPD, requisições de titular, triagem, monitor
produto/            # revisão de lançamento, claims, "isso é problema?"
regulatorio/         # DOU/agências, diff de política, gaps, consultas públicas
governanca-ia/      # triagem IA, RIPD-IA, revisão fornecedor IA, gap regulatório
propriedade-intelectual/                 # INPI marca/patente, FTO, notificação, Marco Civil, OSS, cláusula PI
contencioso/         # portfólio, processos, holds, notificações, oitiva, matriz de fund.
npj/             # NPJ — setup, ramp estagiário, intake, prazos, memos, handoff
estudante-direito/              # método socrático, esquema, IRAC/RFD, OAB, flashcards
builder-hub/        # discovery/install de skills da comunidade com trust gate
external_plugins/         # plugins de parceiros mantidos pelos vendors
  cocounsel-legal/        # Thomson Reuters — Westlaw Deep Research (cobertura primária EUA)
receitas-agentes/  # cookbooks de Managed Agent — um diretório por agente agendado
  diligence-grid/
  docket-watcher/         # adaptar para PJe / e-SAJ / e-Proc / Projudi
  launch-radar/
  reg-monitor/            # adaptar para DOU + agências BR
  renewal-watcher/
references/
  terminology-us-to-br.md  # mapeamento de institutos EUA → BR
scripts/                  # deploy-managed-agent.sh · validate.py · orchestrate.py · lint-tool-scope.py
.claude-plugin/
  marketplace.json        # registry de plugins
```

Cada diretório de plugin segue o mesmo shape:

```
<plugin>/
  .claude-plugin/plugin.json
  CLAUDE.md               # template de perfil de prática — preenchido por /<plugin>:cold-start-interview
  README.md
  skills/                 # skills — cada uma é o slash command /<plugin>:<skill>
  agents/                 # agentes agendados (se houver)
  hooks/                  # pre- e post-tool hooks (se houver)
```

## Como começar

### Claude Cowork

No Cowork:
1. Abra a aba **Cowork**.
2. Clique **Customize** na barra lateral esquerda.
3. **Browse plugins** e instale os que quiser, **ou** upload um arquivo de plugin custom (qualquer pasta de plugin zipada).

Após instalar, skills disparam automaticamente quando relevantes, slash commands ficam disponíveis via `/`, e os agentes agendados rodam na cadência do frontmatter.

### Claude Code

```bash
# Adicione o marketplace (caminho absoluto pra este repo ou URL do GitHub)
/plugin marketplace add <caminho-pra-este-repo>

# Instale plugins — escolha os que combinam com a sua prática
/plugin install comercial@claude-for-legal-brasil
/plugin install privacidade@claude-for-legal-brasil
/plugin install societario@claude-for-legal-brasil

# Reinicie o Claude Code, depois rode o setup de cada plugin instalado.
# Isso escreve o perfil de prática em ~/.claude/plugins/config/claude-for-legal/<plugin>/CLAUDE.md
/comercial:cold-start-interview
/privacidade:cold-start-interview
/societario:cold-start-interview
```

**Rode o cold-start primeiro.** Toda outra skill no plugin lê do perfil de prática que ele escreve. Pular o setup é o motivo nº 1 de output genérico. A entrevista leva 10–20 minutos por plugin e vai pedir documentos-semente (um MSA assinado, um playbook, uma revisão anterior — o que se encaixar). Mais material-semente é melhor; tem opção **quick start** se quiser produzir em 2 minutos e refinar depois.

**Conecte uma ferramenta de pesquisa cedo.** Tudo é melhor com uma, e citações ficam não-verificadas sem isso. Para Brasil, o estado da arte hoje é: web search (Lexml, planalto.gov.br, stf.jus.br, stj.jus.br, tst.jus.br), JusBrasil quando disponível via API/MCP, e CoCounsel/Westlaw via plugin externo apenas para fontes US.

Atualizações: `/plugin update`.

### Claude Managed Agents

Para agentes agendados — monitor regulatório de DOU, vigia de renovação, vigia de andamento PJe, diligence grid, launch radar — faça deploy por trás do seu próprio orquestrador:

```bash
export ANTHROPIC_API_KEY=sk-ant-...
scripts/deploy-managed-agent.sh reg-monitor
scripts/deploy-managed-agent.sh renewal-watcher
scripts/deploy-managed-agent.sh docket-watcher
scripts/deploy-managed-agent.sh diligence-grid
scripts/deploy-managed-agent.sh launch-radar
```

Cada template em [`receitas-agentes/`](./receitas-agentes) referencia o mesmo system prompt e skills da sua contraparte plugin. O script de deploy resolve referências de arquivo, faz upload das skills, cria leaf-worker subagents e POSTa o orquestrador em `/v1/agents`. Veja [`scripts/orchestrate.py`](./scripts/orchestrate.py) para um loop de eventos de referência que roteia handoff entre agentes via sua camada de orquestração.

> **Research Preview:** delegação de subagente (`callable_agents`) é preview e suporta um único nível. Veja READMEs de cada agente para tier de segurança e orientação de handoff.

## Como tudo se encaixa

| | O que é | Onde fica |
|---|---|---|
| **Plugins** | Pacotes auto-contidos por área de prática — skills, agentes, hooks e template de perfil. Instale o que precisar. | `<plugin>/` |
| **Skills** | Expertise de domínio, convenções e métodos passo-a-passo que Claude usa quando relevante — e ações slash que você dispara: `/comercial:review`, `/privacidade:dsar-response`, `/contencioso:claim-chart`. | `<plugin>/skills/<skill>/SKILL.md` |
| **Agentes** | Workflows agendados ou event-driven (renewal-watcher, docket-watcher, monitor de DOU). Roda em background, posta no canal ou escreve arquivo. | `<plugin>/agents/` |
| **Perfil de prática** | `CLAUDE.md` em linguagem natural descrevendo seu playbook, regras de escalação e estilo da casa. Toda skill lê dele. | `~/.claude/plugins/config/claude-for-legal/<plugin>/CLAUDE.md` |
| **Conectores** | [MCP servers](https://modelcontextprotocol.io/) que ligam Claude aos seus dados — CLM, GED, eDiscovery, plataformas de pesquisa, produtividade. | `.mcp.json` (por plugin) |
| **Managed-agent cookbooks** | `agent.yaml` + subagents depth-1 + exemplos de steering para deploy headless. | `receitas-agentes/<slug>/` |

Tudo é markdown e JSON. Sem build step.

## Plugins por vertical

Agrupados por onde o trabalho mora. O cold-start de cada plugin é o que o adapta ao seu time — comece por aí.

### Transacional e consultivo

| Plugin | O que adiciona |
|---|---|
| **[comercial](./comercial)** | Revisão playbook-aware de contratos com fornecedor, NDAs e SaaS. Rastreio de aditivos. Registro de renovação com alertas de denúncia. Roteamento de escalação. Resumos para stakeholders. |
| **[societario](./societario)** | Due diligence M&A com revisão tabular e citação por célula. Quadros de exceções, checklists de fechamento, deliberações, atas. Tracker de obrigações societárias. Integração pós-fechamento. |
| **[privacidade](./privacidade)** | Triagem LGPD, geração de RIPD, revisão de contrato de tratamento como controlador/operador, resposta a requisições de titular. Monitor de política observa drift entre política e prática. |
| **[produto](./produto)** | Revisão de lançamento contra calibragem de risco da casa. Checagem de claims publicitárias (CDC, CONAR). Triagem "isso é problema?". Avaliação de risco de feature. |
| **[trabalhista](./trabalhista)** | Revisão de admissão e rescisão com flags CLT. Classificação de vínculo (PJ vs CLT vs terceirização). Tracker de licenças (maternidade/paternidade/saúde/CIPA). Investigações internas. Redação de política com suplementos por convenção/acordo coletivo. |
| **[governanca-ia](./governanca-ia)** | Triagem de caso de uso de IA contra seu registro. Avaliações de impacto pelos regimes em escopo (LGPD art. 20 + PL 2338). Revisão de fornecedor de IA. Diff regulatório vs política. |
| **[regulatorio](./regulatorio)** | Vigia de feeds regulatórios (DOU + agências), diff de política, tracker de gaps, tracker de consultas públicas. O digest da segunda que o time efetivamente lê. |
| **[propriedade-intelectual](./propriedade-intelectual)** | Anterioridade INPI, triagem FTO, notificação extrajudicial, pedido de remoção via Marco Civil, compliance OSS, revisão de cláusula PI, acompanhamento de portfólio. |

### Contencioso

| Plugin | O que adiciona |
|---|---|
| **[contencioso](./contencioso)** | Duas superfícies. **Interno/portfólio:** intake de processo, status de portfólio, hold documental, status de banca externa, notificações. **Banca/solo:** cronologia, matriz de fundamentação (patente e cível), prep de oitiva, revisão de lista de sigilo, redação de peça. |

### Aprendizado e prática acadêmica

| Plugin | O que adiciona |
|---|---|
| **[estudante-direito](./estudante-direito)** | Método socrático, fichamento de julgado, esquema, correção IRAC/RFD, prep para cold-call, flashcards, OAB, previsão de prova, planejamento de estudo. **Modo aprendizado, não modo resposta** — nunca escreve a resposta por você. |
| **[npj](./npj)** | Setup do(a) coordenador(a) e ramp semestral do(a) estagiário(a). Guia de supervisor(a) por área com postura pedagógica (assist / guide / teach). Intake estruturado com cross-area issue spotting. Acompanhamento de prazos com cautela preventiva. Scaffolds de memo, cartas (rotineiras + linguagem simples), handoffs de semestre. Construído conforme Provimento CFOAB 166/2015, Resolução CNE/CES 5/2018 e CED-OAB. |

### Ecossistema

| Plugin | O que adiciona |
|---|---|
| **[builder-hub](./builder-hub)** | Discovery e instalação de skills comunitárias com camada de trust real — registros monitorados, framework de QA (`/builder-hub:skills-qa`), updates SHA-pinned e checagem mandatória de trust antes de qualquer coisa entrar no ambiente. |

### Externos / parceiros

Plugins em [`external_plugins/`](./external_plugins) são construídos e mantidos por terceiros. Instalam pelo marketplace como qualquer outro, mas o vendor é dono do código, do conector e do canal de suporte.

| Plugin | Construído por | O que adiciona |
|---|---|---|
| **[cocounsel-legal](./external_plugins/cocounsel-legal)** | Thomson Reuters | Westlaw Deep Research com relatórios totalmente citados — caselaw, statutes, regulations, Practical Law e fontes secundárias. **Cobertura primária EUA.** Útil em DD transfronteiriça; para fontes BR complemente com JusBrasil, Lexml e sítios STF/STJ/TST. Requer assinatura CoCounsel Legal com MCP habilitado. Suporte: cocounselsupport@tr.com. |

## A trust layer para skills da comunidade

A comunidade está construindo skills jurídicas rápido. Mas ninguém certifica skills comunitárias, e quem instala uma skill aleatória do GitHub está instalando código que roda com acesso aos arquivos do caso, ao perfil de prática e aos conectores de pesquisa.

`builder-hub` dá ao ecossistema a trust layer faltante:

- **Revisão de segurança** — scan de conteúdo oculto, detecção de injeção, check estrutural a cada install
- **Allowlist** — gate restritivo por default (registros, publishers, conectores, licenças)
- **License gate** — política sensível ao contexto de implantação (pessoal / interno-escritório / embedded em produto)
- **Freshness gate** — rastreia se o conteúdo de referência embarcado (regulação, lei, procedimento) passou da janela de verificação e avisa na invocação
- **Re-scan a cada update** — skill limpa em v1.0 e envenenada em v1.1 é pega
- **Install log** — registro auditável do que está instalado, de onde, sob qual licença, com qual veredito

Allowlist é restritiva por default. Modo permissivo é escolha explícita. Não-advogado(a) é roteado(a) ao seu contato advogado(a), não pra um botão "instalar mesmo assim".

Skills comunitárias passam pelo mesmo design review (`/builder-hub:skills-qa`) que os plugins first-party. Se você constrói para advogados(as), rode o QA contra sua skill antes de publicar.

## Conectores MCP

> [!IMPORTANT]
> **Conecte uma ferramenta de pesquisa primeiro.** Todo plugin vem com conectores de pesquisa pré-configurados — porém vários cobrem primariamente jurisdição EUA. Para Brasil veja [CONNECTORS.md](CONNECTORS.md) — lista de conectores desejados (PJe, JusBrasil, INPI, DOU, ANPD, CVM, BCB) e qual a melhor opção hoje. Sem conector de pesquisa, citações vêm de dados de treino e ficam marcadas `[verify]`.

Veja [CONNECTORS.md](CONNECTORS.md) para a tabela completa atual e a wishlist de conectores brasileiros.

> Conectores marcados "customer subscription" precisam da conta do cliente e API key. Configure no `.mcp.json` de cada plugin ou via `claude mcp` no setup do Claude Code.

> **Construindo um conector?** Veja [CONNECTORS.md](./CONNECTORS.md).

## Claude para Microsoft 365

Advogados(as) vivem em Word e Excel. **Toda skill desta suite que toca contrato é construída para funcionar na sidebar Claude for Word, com tracked changes como modo de output.** Isso vale para `comercial:review`, `comercial:amendment-history`, `propriedade-intelectual:ip-clause-review`, `governanca-ia:vendor-ai-review`, `privacidade:dpa-review` e a extração de DD em `societario`. O(A) revisor(a) aceita ou rejeita cada mudança como faria com markup humano — numeração, termos definidos, referências cruzadas e estilos são preservados.

As skills para Excel produzem planilhas que abrem limpas: `societario:tabular-review` escreve `.xlsx` multi-sheet com aba de fontes, `contencioso:claim-chart` escreve matriz elemento-a-elemento com colunas de citação, `societario:entity-compliance` escreve o registro de obrigações societárias com colunas de prazo, e `comercial:renewal-tracker` exporta o registro de renovação ordenado por data de denúncia.

Instale Claude for Microsoft 365 do **[Microsoft AppSource](https://marketplace.microsoft.com/en-us/product/office/wa200010453)**. Uma thread única pode atravessar Word, Excel, PowerPoint e Outlook.

## Customizando

Estes são templates de referência. Ficam melhores quando você os afina ao jeito do seu time — e o mecanismo de customização é o próprio plugin, não um arquivo de config escondido.

- **Rode o cold-start.** Ele **é** o mecanismo de customização. Pergunta como sua prática trabalha, lê seus documentos-semente e escreve o perfil. Toda outra skill lê dele.
- **Edite o perfil de prática.** Seu perfil vive em `~/.claude/plugins/config/claude-for-legal/<plugin>/CLAUDE.md`. Edite direto para correções pequenas. Sobrevive a updates.
- **Rode o setup de novo.** `/<plugin>:cold-start-interview` para re-entrevista completa quando a prática mudar (nova jurisdição, novo CLM, nova política).
- **Troque conectores.** Aponte `.mcp.json` para seu CLM, GED, plataforma de eDiscovery, tracker de lançamento, HRIS.
- **Traga seu playbook e seus templates.** Coloque sua terminologia, estilo da casa e templates branded no `CLAUDE.md` e em `references/`.
- **Forke skills para o estilo da casa.** Toda skill é um markdown em `skills/`. Edite os passos, as travas, o formato de output.
- **Adicione agentes agendados.** Os agentes em `<plugin>/agents/` são markdown com schedule cron-style.

Sem build step. Tudo é markdown e JSON.

## Referência de Skills e Comandos

(Tabelas detalhadas por plugin foram preservadas no upstream. Ao adaptar cada plugin individualmente, esta seção será atualizada com as skills brasileiras renomeadas/recalibradas. Por enquanto, os slash commands originais funcionam — execute `/<plugin>:cold-start-interview` em qualquer plugin instalado para começar.)

Os principais comandos por plugin estão na tabela de Agentes acima.

## Contribuindo

Tudo aqui é markdown e JSON. Forke, edite, abra PR.

- **Nova skill** → adicione em `<plugin>/skills/<skill-name>/SKILL.md` com o frontmatter das skills existentes (`name`, `description`, `argument-hint`). Mantenha description sob 1024 caracteres — é o sinal de trigger. Skill é invocável como `/<plugin>:<skill-name>`. Marque skills de pura referência como `user-invocable: false`.
- **Novo agente** → adicione `<plugin>/agents/<nome>.md` com frontmatter de scheduling e o system prompt. Adicione `receitas-agentes/<nome>/` para deploy headless.
- **Skills comunitárias** → use `/builder-hub:skill-installer` para testar uma skill comunitária no seu ambiente. O hub roda `/builder-hub:skills-qa` em cada skill antes de instalar.
- **Validar cookbooks antes de push** → `bash scripts/test-cookbooks.sh`.
- **Adaptação BR de plugins** → quando portar skill/agent do upstream, use `references/terminology-us-to-br.md` como guia canônico de mapeamento. Sinalize equivalências imperfeitas na própria skill (`equivalente funcional`, `não há análogo direto no Brasil — abordagem adotada: …`).

## Licença

Licenciado sob [Apache License, Version 2.0](LICENSE).

Original: Copyright 2026 Anthropic PBC.
Adaptação BR: derivada do upstream sob Apache 2.0 — não é produto oficial Anthropic.
