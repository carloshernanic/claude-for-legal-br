# Regulatory Counsel Plugin

> Adaptação BR não oficial. Calibrado para reguladores brasileiros (ANPD, CVM, BCB, ANVISA, ANATEL, ANS, ANEEL, ANP, ANTT, ANAC, IBAMA, CADE, SENACON), Lei 9.784/99, LINDB, Lei 13.874/2019, Dec. 10.411/2020 (AIR). Veja `references/terminology-us-to-br.md`.

Monitora feeds regulatórios, faz o diff de novas normas contra a sua biblioteca de políticas e expõe lacunas. Aprende o seu limiar de materialidade para não alertar a cada discurso de diretor de agência. Preparado para fontes oficiais (DOU, diários setoriais das agências) e bases jurisprudenciais (STF, STJ, TRFs).

**Todo output é rascunho para revisão por advogado(a) inscrito(a) na OAB — citado, sinalizado e gated — não é parecer jurídico.** O plugin faz o trabalho: lê os documentos, aplica seu playbook, encontra os problemas, redige a nota técnica. Um(a) advogado(a) revisa, verifica e decide. As citações são tagueadas por fonte para você saber quais vieram de ferramenta de pesquisa e quais precisam ser checadas. Marcadores de sigilo profissional (Art. 7º, XIX, EOAB) são aplicados de forma conservadora para que nada seja exposto por acidente. Ações consequentes — protocolar, enviar, executar — ficam atrás de confirmação explícita.

## Para quem é

| Papel | Workflows principais |
|---|---|
| **Compliance / advogado(a) regulatório(a)** | Manutenção de watchlist, triagem de lacunas, coordenação de atualização de políticas |
| **Privacy / product counsel** | Recebe alertas filtrados relevantes à sua área (ex.: ANPD, BCB Open Finance) |
| **Diretor(a) jurídico(a) / GC** | Destinatário de escalonamento para lacunas materiais com prazo |

## Primeira execução: cold-start

Pergunta quais reguladores você acompanha (ANPD, CVM, BCB, ANVISA, ANATEL, ANS, ANEEL, ANP, ANTT, ANAC, IBAMA, CADE, SENACON, Procons, órgãos estaduais), conecta sua pasta de políticas, aprende o que "material" significa para você. Constrói a watchlist e indexa a biblioteca de políticas.

```
/regulatorio:cold-start-interview
```

## Skills

| Skill | Faz |
|---|---|
| `/regulatorio:cold-start-interview` | Cold-start: watchlist + índice de políticas + limiar de materialidade |
| `/regulatorio:reg-feed-watcher` | Checa feeds agora, reporta o que é novo (DOU, sites das agências, RSS) |
| `/regulatorio:policy-diff [reg]` | Diff de uma alteração normativa específica contra a biblioteca de políticas |
| `/regulatorio:gaps` | Tracker de lacunas abertas — o que foi sinalizado e ainda não foi fechado |
| `/regulatorio:comments` | Revisa consultas públicas / tomadas de subsídios abertas, registra decisões, rastreia prazos |
| `/regulatorio:policy-redraft` | Proposta de redraft marcado de política que fecha uma lacuna — primeira versão para revisão interna, não edição direta dos documentos-fonte |
| `/regulatorio:matter-workspace` | Gerencia matter workspaces (apenas prática privada multi-cliente) — new, list, switch, close, none |
| **gap-surfacer** *(referência)* | Framework compartilhado de rastreamento de lacunas e consultas públicas carregado por `/gaps` e `/comments` |

## Skills interativas vs. agentes agendados

As skills acima rodam quando você as invoca — para quando você está trabalhando um caso. Os agentes abaixo rodam em cronograma — para o que se move quando você não está olhando:

| Agente | O que observa | Cadência padrão |
|---|---|---|
| **reg-change-monitor** | Feeds regulatórios (DOU, sites das agências) — filtra pelo limiar de materialidade aprendido no cold-start e posta um digest que é sinal, não ruído | Semanal (diário se o ambiente regulatório estiver ativo, ex.: AIR em andamento, consulta pública aberta) |

## Conectores e verificação de citações

**Conecte uma ferramenta de pesquisa primeiro — os guardrails de citação dependem disso.** Sem uma, toda citação é tagueada `[verify]` e a nota do revisor acima de cada deliverable registra que fontes não foram verificadas. O plugin funciona dos dois jeitos; só faz mais da verificação para você quando há uma ferramenta de pesquisa conectada.

Os conectores de pesquisa jurídica deste plugin não são apenas fontes de dados — são a diferença entre uma citação verificada e uma citação que você tem que checar. Uma citação obtida via ferramenta conectada (DOU, jurisprudência STF/STJ) é tagueada com sua fonte e pode ser rastreada. Uma citação do conhecimento do modelo ou de web search é tagueada `[verify]` ou `[verify-pinpoint]` e deve ser checada contra fonte primária antes que alguém dependa dela. O plugin tiera suas citações para que seu tempo de verificação vá onde importa.

## Integrações

Vem com o bucket geral de conectores em `.mcp.json`:

- **Slack** — busca em mensagens, leitura de canais, encontra discussões
- **Google Drive** — busca, leitura e fetch de documentos

Conectores para bases regulatórias brasileiras (DOU via Imprensa Nacional, consultas públicas das agências, jurisprudência STF/STJ) podem ser adicionados quando URLs de parceiros estiverem disponíveis. RSS/e-mail direto das agências (ANPD, CVM, BCB, ANVISA, ANATEL etc.) como fallback.

## Pré-requisitos

Notificações ao owner (atribuição de lacunas, lembretes de prazo, alertas de consulta pública) requerem um servidor MCP Slack no seu ambiente. Sem isso, o tracker de lacunas e o tracker de consultas públicas ainda funcionam — apenas as notificações não vão postar, e as skills vão sinalizar itens sem gate no status report.

## Como ele aprende

Seu perfil de prática em `~/.claude/plugins/config/claude-for-legal/regulatorio/CLAUDE.md` não é estático — melhora à medida que você usa o plugin. As skills te dizem quando um output usou um default que você deveria afinar. O agente `reg-change-monitor` observa os feeds regulatórios e sinaliza mudanças contra sua biblioteca de políticas. Você pode rodar o setup de novo, editar o arquivo direto, ou dizer a uma skill para registrar uma nova posição.

## Notas

- Filtragem por materialidade é o valor. Tudo é "tecnicamente uma mudança regulatória" — o plugin aprende o que de fato importa aqui (ex.: Resolução CD/ANPD vs. comunicado interno de diretoria).
- Policy diff compara contra políticas indexadas. Se a biblioteca de políticas não estiver conectada, diffs rodam contra o que você cola.
- Este é a versão automatizada do `reg-gap-analysis` do privacidade. Use os dois em conjunto: este observa, aquele aprofunda.
- Federalismo regulatório brasileiro é limitado (CF arts. 22 competência privativa da União e 24 competência concorrente). O plugin sinaliza quando uma matéria pode envolver normas estaduais/municipais sobrepostas.
- Compliance regulatório integra com Lei Anticorrupção (Lei 12.846/2013) e Decreto 11.129/2022 (programa de integridade). Onde aplicável, o plugin sinaliza interfaces com autorregulação setorial (CONAR para publicidade, ANBIMA para mercado financeiro).

## Configuração

Sua configuração é armazenada em `~/.claude/plugins/config/claude-for-legal/regulatorio/CLAUDE.md` e sobrevive a updates do plugin — você só roda o setup uma vez.
