> Adaptação BR não oficial. Gates: LGPD, Marco Civil (atenção RE 1.037.396 STF), CDC, ECA, CONAR, LBI, Marco Cripto (Lei 14.478/2022), Open Finance, PL 2338 (IA). Veja `references/terminology-us-to-br.md`.

# Launch Radar — template de managed-agent

## Visão geral

Varredura agendada do tracker de lançamentos do time de produto — Jira, Linear ou Asana — em busca de lançamentos que provavelmente vão precisar de revisão jurídica nas próximas semanas. Faz a triagem de cada lançamento contra a calibração de risco do(a) product counsel e produz um memorando semanal de radar: o que está vindo, o que precisa de atenção jurídica, o que disparou um sinal. Mesma fonte que o agente do plugin Claude Code [`launch-watcher`](../../produto/agents/launch-watcher.md) — este diretório é o cookbook de Managed Agent para `POST /v1/agents`.

Este é um **cookbook, não um produto.** Não funciona out of the box. Você precisa apontar os conectores MCP para o seu tracker, carregar sua calibração de risco, definir a cadência e configurar para onde o memorando vai. Notas de adaptação abaixo.

## Antes de implantar

- **A triagem do radar é uma decisão de roteamento, não uma revisão jurídica.** "Precisa de revisão" significa que um(a) product counsel deve olhar; "FYI" não significa que o lançamento está liberado; "skip" não libera o lançamento. Revise o radar completo, não só os itens sinalizados — os itens não sinalizados são onde você perde aqueles que precisava ver. Todo conteúdo gerado é **rascunho para revisão por advogado(a) inscrito(a) na OAB**, não substitui parecer.
- **A classificação de risco usa a calibração na sua configuração de plugin.** Se sua calibração está desatualizada, a triagem também está. Novas linhas de produto, novos reguladores (ANPD, BCB, CVM, ANATEL, ANVISA, ANS, ANEEL, SENACON, CADE), novas geografias e novas dependências de terceiros precisam entrar na calibração antes do radar poder rotear por elas.
- **A lista de palavras-chave gatilho é opinativa.** Se a superfície do seu produto não bater com os defaults (ex.: alto uso de biometria — dado sensível, art. 11 LGPD; tratamento de dados de crianças/adolescentes — art. 14 LGPD + ECA; decisão automatizada — art. 20 LGPD; transferência internacional — Resolução CD/ANPD 19/2024; publicidade — CDC arts. 36–38 e CONAR; serviços financeiros — BCB/CVM/Open Finance/PIX; cripto-ativos — Lei 14.478/2022; acessibilidade — LBI e Decreto 9.296/2018/eMAG; IA — PL 2338/2023 e Resolução CNJ 332/2020), recalibre antes do primeiro run ou o memorando vai perder os casos que ele foi feito para pegar.
- **Tickets de tracker são input não confiável.** Um PM pode colocar qualquer coisa em título ou descrição, e um atacante pode abrir um ticket. A triagem roteia pelo conteúdo; ela não responde pelo ticket. Observe ainda a guarda de logs de aplicação (Marco Civil art. 15) e a inviolabilidade de comunicações.

## Deploy

```bash
export ANTHROPIC_API_KEY=sk-ant-...
export LINEAR_MCP_URL=... ATLASSIAN_MCP_URL=... ASANA_MCP_URL=... GDRIVE_MCP_URL=...
../../scripts/deploy-managed-agent.sh launch-radar
```

Defina apenas as URLs MCP dos trackers que você realmente usa. O orquestrador e o `tracker-reader` ignoram MCPs que não estiverem configurados.

## Eventos de steering

Veja [`steering-examples.json`](./steering-examples.json). A cadência típica é uma varredura semanal com horizonte de 4–6 semanas, mais triagem on-demand de ticket único quando um PM pinga o(a) product counsel com "isso é problema?"

## Segurança e handoffs

Tickets de tracker são input não confiável. Um product manager pode colocar texto arbitrário em título, descrição ou comentário — e um atacante pode abrir um ticket. Isolamento em três camadas:

| Camada | Toca conteúdo não confiável do tracker? | Tools | Conectores |
|---|---|---|---|
| **`tracker-reader`** | **Sim** | `Read`, `Grep` apenas | Linear, Jira (atlassian), Asana (somente leitura) |
| `risk-classifier` / Orquestrador | Não | `Read`, `Grep`, `Glob`, `WebFetch`, `Agent` | Somente orquestrador: Linear / Jira / Asana / Drive (somente leitura) |
| **`memo-writer`** (detentor do Write) | Não | `Read`, `Write`, `Edit` | Nenhum |

O `tracker-reader` devolve uma lista JSON de lançamentos com tamanho limitado e schema validado. O `risk-classifier` não tem MCP e não tem rede; trabalha a partir da lista validada mais o arquivo de calibração do usuário. O `memo-writer` é o único worker com Write e produz `./out/launch-radar-<data>.md`. O orquestrador não tem Write e nunca parseia corpos brutos de ticket diretamente.

**Handoff:** quando um lançamento precisa de um memorando de revisão jurídica completa em vez de uma entrada de radar, o orquestrador emite um `handoff_request` para a skill `launch-review` (rodando em sessão nova) em vez de redigir o memorando inline. O `scripts/orchestrate.py` faz o roteamento.

## Notas de adaptação

Coisas que você vai precisar mudar antes disso ser útil:

- **Ponteiro de tracker.** Edite `mcp_servers` em [`agent.yaml`](./agent.yaml) e [`subagents/tracker-reader.yaml`](./subagents/tracker-reader.yaml) para a URL MCP do seu tracker. Se você só usa um entre Jira/Linear/Asana, remova os outros dois. Se seu tracker não estiver nessa lista, troque pelo MCP que você usa e atualize o system prompt do `tracker-reader` conforme.
- **Calibração de risco.** O `risk-classifier` lê a calibração do usuário em `../../produto/CLAUDE.md` (populada por `/produto:cold-start-interview`). Se você ainda não rodou o cold-start, faça isso primeiro ou escreva à mão um CLAUDE.md com tabelas "Normalmente bloqueia / Normalmente exige trabalho / Normalmente FYI" antes da primeira varredura. Sem calibração, o classificador cai para gatilhos por palavra-chave apenas, o que é ruidoso. Inclua na calibração os gates BR: LGPD (bases legais art. 7º e art. 11; RIPD art. 38; titulares art. 18; comunicação de incidente art. 48 + Resolução CD/ANPD 15/2024; decisão automatizada art. 20; transferência internacional Resolução CD/ANPD 19/2024), Marco Civil (Lei 12.965/2014 — atenção ao RE 1.037.396 STF sobre art. 19, ainda pendente; Tema 786 STF sobre direito ao esquecimento), CDC (publicidade arts. 36–38, práticas abusivas art. 39, arrependimento art. 49, vícios/fatos arts. 12–25, banco de dados arts. 43–44), ECA + Lei 14.811/2024 (deepfakes/proteção infantojuvenil) + CONANDA, CONAR (autorregulação publicitária), LBI/eMAG (Decreto 9.296/2018 — acessibilidade), Marco Cripto (Lei 14.478/2022) + ICVM/RCVM aplicável a tokens de valores mobiliários, Open Finance (Res. Conjunta BCB/CMN 1/2020) e PIX (Res. BCB 1/2020), reguladores setoriais (ANVISA, ANATEL, ANS, ANEEL, BCB, CVM), e regime de IA (Resolução CNJ 332/2020; PL 2338/2023).
- **Cadência e horizonte de varredura.** Default é semanal / 6 semanas. Sua cadência de lançamento pode justificar diária ou quinzenal; lead times curtos pedem horizonte maior. Configure a cadência no seu scheduler (cron, Temporal, Airflow, EventBridge), não dentro do agente. O horizonte é passado no evento de steering.
- **Canal de entrega.** O memorando vai para `./out/` por padrão. Para postar no Slack em vez disso ou além disso: (a) adicione um MCP do Slack ao cookbook e atualize o `memo-writer` para postar após escrever, ou (b) faça sua camada de orquestração coletar `./out/launch-radar-<data>.md` e encaminhar. Esse padrão mantém a entrega fora do agente, facilitando testes; escolha o que se encaixa na sua operação.
- **Palavras-chave gatilho.** A lista do system prompt do `launch-watcher` é opinativa. Apague categorias que não se aplicam, acrescente termos do seu domínio (dados sensíveis art. 11 LGPD, biometria, dados de crianças/adolescentes art. 14 LGPD + ECA, decisão automatizada art. 20 LGPD, transferência internacional, RIPD, publicidade infantil — CONAR/CONANDA, recall — Portaria SENACON 618/2019, acessibilidade — LBI/eMAG, cripto — Lei 14.478/2022, Open Finance, PIX, setoriais ANVISA/ANATEL/ANS/ANEEL, deepfakes — Lei 14.811/2024, IA — PL 2338/2023) e recalibre os limiares de severidade contra sua tabela de calibração. Re-implante após mudanças.
- **Cabeçalho de privilégio.** O `memo-writer` prepende o cabeçalho de work-product da configuração de plugin. Confirme a marcação exata com seu(sua) GC/responsável antes de implantar — variações por jurisdição se aplicam. Padrão BR: `MATERIAL DE TRABALHO — PRIVILEGIADO E CONFIDENCIAL (Art. 7º, XIX, EOAB)`.

## O que você ganha e o que não ganha

- **Você ganha:** um manifest funcional, um pipeline com isolamento por camadas de segurança, um memorando que cita cada lançamento de volta para a URL do tracker e um caminho de handoff para a skill completa de launch-review.
- **Você não ganha:** um agente production-ready. Aponte para seu tracker, carregue sua calibração, defina a cadência, rode uma avaliação e peça para o(a) product counsel revisar os primeiros outputs contra sua própria leitura dos mesmos tickets antes de confiar.
- **E principalmente você não ganha:** um substituto para o(a) product counsel. Este agente faz triagem. Quem revisa, sinaliza e decide é o(a) advogado(a) inscrito(a) na OAB. Todo item "precisa de revisão" no memorando é uma pista, não um veredito.
