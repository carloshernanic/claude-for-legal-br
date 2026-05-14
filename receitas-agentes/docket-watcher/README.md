# Docket Watcher — template de managed-agent

> Adaptação BR não oficial. Calibrado para tribunais brasileiros (PJe/CNJ, e-SAJ/Softplan, e-Proc/TRF, Projudi, DataJud) e prazos do CPC (Lei 13.105/2015). Tribunais trabalhistas usam PJe-JT obrigatório. Veja `references/terminology-us-to-br.md`.

## Visão geral

Monitora o andamento processual (autos) dos processos em curso no portfólio contencioso. As fontes variam por tribunal: **PJe** (padrão CNJ — Justiça Federal, parte das estaduais, JT), **e-SAJ** (Softplan — TJSP, TJMS, TJAC e outros), **e-Proc** (TRF2, TRF4 e algumas estaduais), **Projudi** (estados remanescentes), além de **DataJud** (CNJ — dados consolidados) e consolidadores comerciais como **JusBrasil** e **Escavador**. Para cada processo ativo o agente busca novas movimentações desde a última checagem (despachos, decisões, sentenças, acórdãos, intimações, juntadas), mapeia o tipo de movimentação para prazos candidatos (CPC arts. 218–235; CPC 1.003), cruza com o histórico do processo e com as entregas pendentes e produz um relatório de andamento + um feed estruturado de prazos.

Mesma origem do agente [`docket-watcher`](../../contencioso/agents/docket-watcher.md) do plugin `contencioso` do Claude Code — este diretório é o cookbook de Managed Agent para `POST /v1/agents`.

## ⚠️ Antes de implantar

- **Prazos computados são leads, não compromissos de agenda.** As regras de contagem variam por tribunal, vara, magistrado e provimento local, e podem ser modificadas por despacho específico no processo. A contagem do CPC é em **dias úteis** (art. 219), suspensa em recesso (art. 220), com peculiaridades trabalhistas (CLT + CPC subsidiário) e da Fazenda Pública (prazo em dobro — CPC art. 183). Perder prazo tem consequência disciplinar (EOAB) e civil. **Advogado(a) inscrito(a) na OAB confere cada prazo computado** contra a regra controlante e eventuais decisões específicas antes de lançá-lo no controle de prazos. O agente é insumo dessa decisão, não substituto.
- **A classificação da movimentação é heurística.** Uma juntada classificada como decisão interlocutória quando era despacho de mero expediente — ou o contrário — gera regra de prazo errada. Leia o ato; não confie no rótulo.
- **Tribunal desconhecido não tem default.** Se a tabela de regras por tribunal não cobre aquele juízo (ex.: TJ estadual sem regra de plantão configurada, JEF/Juizado especial com prazo próprio), o mapper deve emitir `confidence: low` + `needs_verification: true`, jamais um default silencioso. Se vir prazo "confiante" em tribunal obscuro, trate a tabela como desatualizada até prova em contrário.
- **Autos sem movimentação não é o mesmo que tudo certo.** Cartório/secretaria juntam tarde. Intimações por **Push** (CNJ) ou DJE podem aparecer dias depois do ato. "Sem novas movimentações" é uma afirmação sobre o feed, não sobre o processo.
- **Segredo de justiça (CPC art. 189).** Processos em segredo de justiça (família, infância, dados sensíveis, arbitragem judicial) exigem cuidado redobrado: o agente não deve expor conteúdo de autos sigilosos em relatórios não controlados. Marque `sigilo: true` e restrinja o destinatário.

## Implantar

```bash
export ANTHROPIC_API_KEY=sk-ant-...
export PJE_MCP_URL=...
export ESAJ_MCP_URL=...
export EPROC_MCP_URL=...
export DATAJUD_MCP_URL=...
export GDRIVE_MCP_URL=...
../../scripts/deploy-managed-agent.sh docket-watcher
```

> As variáveis acima são placeholders — substitua pelo conjunto de MCPs efetivamente usados (PJe/CNJ, e-SAJ, e-Proc, Projudi, DataJud, JusBrasil/Escavador). O nome das variáveis no script de deploy é tratado como código e não foi alterado nesta adaptação.

## Eventos de steering

Consulte [`steering-examples.json`](./steering-examples.json).

## Segurança e handoffs

Os autos processuais (no que não estão em segredo de justiça — CPC art. 189) são **públicos por força do art. 5º LX da CF e do CPC art. 11**, mas são também **ENTRADA NÃO CONFIÁVEL**. A parte controla o texto das petições e pode embutir prompts, URLs e instruções voltadas ao agente. Isolamento em três camadas:

| Camada | Toca em peças/autos? | Tools | Conectores |
|---|---|---|---|
| **`docket-reader`** | **Sim** | `Read`, `Grep` apenas | PJe, e-SAJ, e-Proc, DataJud (somente leitura) |
| `deadline-mapper` / Orquestrador | Não — vê apenas JSON estruturado | `Read`, `Grep`, `Glob`, `Agent` | gdrive (tabela de regras por tribunal, somente leitura) |
| **`tracker-writer`** (titular do Write) | Não | `Read`, `Write`, `Edit` | Nenhum |

`docket-reader` retorna JSON com tamanho limitado e validado por schema. `deadline-mapper` não tem MCP nem web — aplica as regras que a equipe configurou. `tracker-writer` produz `./out/docket-report-<data>.md` e `./out/deadlines.yaml` e nunca vê o conteúdo bruto das peças.

Cabeçalho de saída obrigatório: `MATERIAL DE TRABALHO — PRIVILEGIADO E CONFIDENCIAL (Art. 7º, XIX, EOAB)`.

## Notas de adaptação

Este cookbook é ponto de partida. Não funcionará em produção até que você tenha feito o seguinte:

- **Configure as URLs de MCP.** `PJE_MCP_URL`, `ESAJ_MCP_URL`, `EPROC_MCP_URL`, `DATAJUD_MCP_URL` (e os que mais usar — Projudi, JusBrasil, Escavador) devem apontar para os endpoints do seu deployment, com a autenticação exigida (certificado A1/A3 da OAB para sistemas que assim exigem, token, etc.). `GDRIVE_MCP_URL` (ou substituto) aponta para onde estão as tabelas de regra de prazo por tribunal.
- **Carregue o portfólio.** O agente lê `matters/_log.yaml` mais o `numero_cnj` (numeração unificada — Resolução CNJ 65/2008) e o `tribunal` de cada processo a partir da configuração do plugin contencioso da equipe. Se o seu sistema de controle de prazos é a fonte da verdade, exponha-o por MCP ou via sincronização agendada para o caminho de configuração.
- **Configure as regras por tribunal.** Forneça ao deadline-mapper uma tabela de regras locais para cada tribunal do portfólio. Regras do CPC podem ser codificadas uma única vez (prazos recursais — apelação, agravo de instrumento e embargos infringentes em 15 dias úteis; embargos de declaração em 5 dias úteis — CPC art. 1.003; cumprimento de sentença em 15 dias — CPC art. 523); o que muda por tribunal são plantões, recesso forense, provimentos específicos da corregedoria e particularidades de cada JT/TRT/TJ. Tribunal desconhecido deve gerar `confidence: low` + `needs_verification: true`.
- **Cuidado com competência.** Federal vs estadual (CF arts. 108–109), trabalhista (CF art. 114), eleitoral (CF art. 121), militar (CF arts. 122–124). A regra de prazo correta depende do ramo: a JT, por exemplo, tem prazos próprios e PJe-JT obrigatório (Resolução CSJT 185/2017). Não trate "tribunal" como atributo monolítico.
- **Conecte a entrega.** Decida onde a saída vai: seu sistema de controle de prazos consome `./out/deadlines.yaml`; o relatório narrativo vai para Teams/Slack/e-mail ou para o workspace de gestão de matérias; flags críticas roteiam para quem deva ser acordado.
- **Defina a periodicidade.** Semanal para a maioria dos processos; **diária** para qualquer um com audiência de conciliação (CPC 334) ou de instrução (CPC 358) em até 14 dias, qualquer processo em fase de instrução tardia ou cumprimento de sentença, qualquer processo com `risk: critical` e qualquer processo sob **Push/intimação eletrônica** ativa em que a regra de presunção (CPC art. 5º da Lei 11.419/2006) corra contra o escritório.

## Prazos computados são leads, não compromissos de agenda

**Os prazos computados por este agente exigem verificação humana contra a regra controlante (CPC, CLT, provimentos do tribunal, decisões específicas no processo) antes de serem agendados. Perder prazo processual tem consequência disciplinar (EOAB) e responsabilidade civil. Este agente sugere prazos; o(a) advogado(a) confirma e lança no controle.**

Todo prazo carrega os campos `confidence` e `needs_verification`. O relatório segrega entradas de baixa confiança e marca um aviso de verificação em qualquer prazo que não derive de regra inequívoca do CPC. Trate isso como **piso**, não teto, da revisão humana. Magistrados alteram defaults por decisão; provimentos de corregedoria mudam; a data em que o ato foi efetivamente publicado/intimado (via DJE ou Push) pode divergir da data exibida no andamento.

**Não garantido:** o agente recomenda um prazo; o(a) advogado(a) responsável confirma contra a regra controlante e lança a data.

> **Aviso permanente.** Toda saída deste agente é **rascunho para revisão por advogado(a) inscrito(a) na OAB** — não é parecer jurídico, não substitui análise profissional. Cabe ao(à) advogado(a) revisar, validar e assumir responsabilidade profissional pelo controle de prazos.
