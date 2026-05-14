# Product Counsel Plugin

> Adaptação BR não oficial. Calibrado para LGPD, Marco Civil, CDC, ECA, LBI, Lei 14.478/2022 (Marco Cripto), CONAR. Atenção: art. 19 Marco Civil pode mudar (RE 1.037.396 STF). Veja `references/terminology-us-to-br.md`.

Fluxos jurídicos de produto: revisão de lançamento (launch review), revisão de claims de marketing, feature risk assessment e triagem rápida "isso é um problema?". Construído em torno de uma calibração de risco aprendida do seu histórico real de launch reviews — o que trava na *sua* empresa, não algo genérico.

**Todo output é rascunho para revisão por advogado(a) inscrito(a) na OAB — citado, sinalizado e com gates — não é parecer jurídico.** O plugin faz o trabalho: lê os documentos, aplica seu playbook, encontra os problemas, redige o memorando. Um(a) advogado(a) revisa, verifica e decide. Citações são marcadas pela origem, então você sabe quais vieram de uma ferramenta de pesquisa e quais precisam de checagem. Marcações de privilégio/sigilo profissional (Art. 7º, XIX, EOAB) são aplicadas de forma conservadora para que nada seja quebrado por acidente. Ações consequentes — protocolar, enviar, executar — exigem confirmação explícita.

## Para quem é

| Papel | Fluxos principais |
|---|---|
| **Product counsel** | Launch review, feature risk assessment, manutenção da calibração |
| **Product managers** | Triagem self-serve "isso é um problema?" |
| **Marketing** | Revisão de claims antes de publicar (CDC arts. 30, 36–38; CONAR) |
| **GC / Liderança jurídica** | Feature risk assessments para itens escalados |

## Primeira execução: a entrevista de cold-start

Conecta ao seu launch tracker (Jira/Linear), lê dez de suas launch reviews passadas, aprende o que você realmente bloqueia vs. o que você libera. Monta uma tabela de calibração de risco que todas as outras skills leem.

Sua configuração fica em `~/.claude/plugins/config/claude-for-legal/produto/CLAUDE.md` e sobrevive a updates do plugin.

```
/produto:cold-start-interview
```

## Comandos

| Comando | Faz |
|---|---|
| `/produto:cold-start-interview` | Entrevista de cold-start |
| `/produto:launch-review [PRD ou ticket]` | Launch review completa contra seu framework |
| `/produto:marketing-claims-review [copy]` | Revisão de claims de marketing (CDC + CONAR) |
| `/produto:is-this-a-problem [pergunta]` | Resposta rápida "isso é um problema?" |
| `/produto:matter-workspace` | Gerencia matter workspaces (apenas advocacia privada multi-cliente) — new, list, switch, close, none |

## Skills

| Skill | Propósito |
|---|---|
| **cold-start-interview** | Escreve ~/.claude/plugins/config/claude-for-legal/produto/CLAUDE.md a partir da entrevista + launch reviews passadas |
| **launch-review** | Revisão categoria-a-categoria, calibrada à sua empresa |
| **marketing-claims-review** | Taxonomia de claims: puffery / factual / comparativo / implícito / absoluto — aplicada via CDC arts. 30, 36–38 e Código CONAR |
| **feature-risk-assessment** | Mergulho profundo em um problema quando launch review não basta |
| **is-this-a-problem** | Triagem do mesmo minuto para a pergunta rápida do Slack |
| **matter-workspace** | Criar, listar, alternar e fechar matter workspaces para advocacia multi-cliente; isola cada cliente/matter para que contexto não vaze |

## Comandos interativos vs. agentes agendados

Os comandos acima rodam quando você os invoca — para quando você está trabalhando uma matéria. Os agentes abaixo rodam em schedule — para o que se move enquanto você não está olhando:

| Agente | O que observa | Cadência padrão |
|---|---|---|
| **launch-watcher** | Launch tracker (Jira/Linear) buscando lançamentos próximos que provavelmente precisam de revisão jurídica; filtra tickets com data de lançamento nos próximos 30 dias conforme a tabela de calibração | Diária |

## Integrações

**Conecte uma ferramenta de pesquisa primeiro — as guardrails de citação dependem disso.** Sem uma, toda citação é marcada `[verify]` e a nota do revisor acima de cada deliverable registra que as fontes não foram verificadas. As skills funcionam de qualquer jeito; uma ferramenta de pesquisa (JusBrasil, Escavador, base de jurisprudência do STF/STJ) só tira trabalho de verificação do seu prato.

Acompanha conectores configurados em `.mcp.json`:

- **Slack** — buscar mensagens, ler canais, achar discussões (general bucket)
- **Google Drive** — buscar, ler e baixar documentos (general bucket)
- **Linear** — tracking de issues e gestão de projetos
- **Atlassian** — issues do Jira e páginas do Confluence
- **Asana** — tarefas e tracking de projetos

Com um tracker conectado: cold-start puxa histórico de lançamentos, launch-review puxa contexto do ticket, agente launch-watcher monitora o calendário.

## Quick start

```
/produto:cold-start-interview
```

Depois:

```
/produto:is-this-a-problem "Podemos fazer A/B test na página de preços?"
```

→ Resposta do mesmo minuto calibrada à sua tabela de risco (cuidado com CDC art. 30 — oferta vincula; e discriminação de preço por perfil pode esbarrar em CDC art. 39 e LGPD art. 20).

```
/produto:launch-review PROJ-1234
```

→ Revisão completa, categoria-a-categoria, com action items.

## Como aprende

Seu perfil de prática em `~/.claude/plugins/config/claude-for-legal/produto/CLAUDE.md` não é estático — melhora conforme você usa o plugin. As skills te avisam quando um output usou um default que você devia tunar. Você pode rerodar o setup, editar o arquivo direto, ou dizer a uma skill para registrar uma nova posição.

## Notas

- A tabela de calibração é o coração. Se ela está errada, toda revisão está errada. Rerode o setup quando sua postura de risco mudar (novo regulador — ex.: nova Resolução ANPD, novo entendimento STJ, novo GC, novo TAC).
- `is-this-a-problem` foi feito para PMs se auto-servirem. Responde rápido e roteia para uma revisão de verdade quando deveria.
- Feature risk assessment é para os 10% de lançamentos que precisam de profundidade. A maioria não — não gere papelada à toa.

## Áreas regulatórias cobertas (Brasil)

O plugin foi calibrado para os principais regimes brasileiros relevantes a produto:

- **Privacidade e dados:** LGPD (Lei 13.709/2018) — bases legais arts. 7º e 11; direitos do titular art. 18; tratamento de dados de crianças/adolescentes art. 14; transferência internacional arts. 33–36; ANPD como autoridade.
- **Internet e responsabilidade do provedor:** Marco Civil (Lei 12.965/2014) — art. 19 (responsabilidade só por descumprimento de ordem judicial específica; **atenção: RE 1.037.396 pendente no STF — fluxo pode mudar**); art. 21 (nudez não consensual — único caso de notificação extrajudicial com efeito); guarda de logs arts. 13–15.
- **Consumidor:** CDC (Lei 8.078/1990) — publicidade arts. 30, 36–38; práticas abusivas arts. 39; direito de arrependimento art. 49 (7 dias em e-commerce); responsabilidade pelo fato e vício do produto/serviço arts. 12–25; cláusulas abusivas art. 51. Aplicabilidade B2B depende de hipossuficiência/vulnerabilidade (jurisprudência STJ).
- **Crianças e adolescentes:** ECA (Lei 8.069/90) + LGPD art. 14 (consentimento específico e em destaque de um dos pais ou responsável); CONANDA Resolução 163/2014 (publicidade infantil é abusiva).
- **Publicidade e marketing:** CDC arts. 36–38 (identificação, enganosa, abusiva) + Código CONAR (autorregulação) + Resolução CONAR sobre influenciadores + CONANDA (infantil) + Lei 9.294/96 (tabaco, álcool, medicamentos).
- **Acessibilidade:** LBI (Lei 13.146/2015) + Decreto 9.296/2018 + eMAG / WCAG 2.1 como referência.
- **Pagamentos e fintech:** BCB / Lei 12.865/2013, Resolução BCB 4.658, regras do PIX, Open Finance (Resolução Conjunta 1/2020) + LGPD.
- **Cripto:** Lei 14.478/2022 (Marco Legal dos Ativos Virtuais) + Instrução CVM 588 (quando o token for valor mobiliário) + ANPD/BCB para interfaces de dados/pagamento.
- **Concorrência e práticas comerciais:** CADE (Lei 12.529/2011); CADE atento a app stores e plataformas, embora sem regulação setorial específica de stores no Brasil.
- **Esquecimento / remoção de conteúdo:** STF RE 1.010.606 (Tema 786, 2021) — não há direito autônomo ao esquecimento; LGPD art. 18 prevê eliminação; remoção por conteúdo difamatório segue art. 19 Marco Civil.
- **Cookies e tracking:** Guia ANPD sobre cookies + LGPD (consentimento ou legítimo interesse com balanceamento e Relatório de Impacto).
- **Coletivização e enforcement:** Ação Civil Pública (Lei 7.347/1985), ações coletivas CDC arts. 81–104, Procons estaduais, SENACON, MP.

## Prerequisites

Algumas features referenciam integrações externas (gestão documental, launch trackers, eDiscovery, gestão de processos, feeds regulatórios — ex.: DOU, ANPD, CVM, BCB, Procons). Estas não vêm embutidas — se você tem um MCP server para alguma dessas no seu ambiente, as features relevantes vão usá-lo. Sem nenhuma, o plugin volta para upload de arquivo e fluxos manuais. Rode `/produto:cold-start-interview --check-integrations` para ver o que está disponível no seu ambiente.

## Configuração

Sua configuração fica em `~/.claude/plugins/config/claude-for-legal/produto/CLAUDE.md` e sobrevive a updates do plugin — você só roda o setup uma vez.
