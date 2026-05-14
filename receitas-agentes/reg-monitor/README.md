# Reg Monitor — template de managed-agent

> Adaptação BR não oficial. Fontes: DOU, sites das agências (ANPD, CVM, BCB, ANVISA, ANATEL, ANS, ANEEL, CADE, COAF), STF, STJ, TST, CARF, CNJ. AIR/LINDB/Lei 9.784. Veja `references/terminology-us-to-br.md`.

## Visão geral

Checa feeds regulatórios em um cronograma, filtra pelo limiar de materialidade do time que faz o deploy, executa uma verificação rápida de gaps contra a biblioteca de políticas para itens sempre materiais, e escreve um digest. Mesma origem do agente Claude Code [`reg-change-monitor`](../../regulatorio/agents/reg-change-monitor.md) e das skills [`reg-feed-watcher`](../../regulatorio/skills/reg-feed-watcher) / [`policy-diff`](../../regulatorio/skills/policy-diff) — este diretório é o cookbook de Managed Agent para `POST /v1/agents`.

## ⚠️ Antes de fazer o deploy

- **Itens do digest são leads triados, não conclusões jurídicas.** O filtro de materialidade aplica um limiar configurável, não juízo jurídico. Uma mudança regulatória que o agente classifica como "informativa" ainda pode ser material para o seu negócio. Uma mudança sinalizada como "material" pode acabar não se aplicando. Revise cada digest; advogado(a) inscrito(a) na OAB decide se um item exige ação, divulgação, mudança de política ou escalonamento.
- **A verificação de gap de política é uma primeira passada, não uma avaliação jurídica de aplicabilidade.** A superfície de gap compara texto regulatório novo contra sua biblioteca de políticas por heurísticas. Um "gap" é um lead para um(a) advogado(a) avaliar; um resultado "alinhado" não certifica conformidade.
- **O limiar de materialidade é sua calibração, não lei.** Se a seção `## Limiar de materialidade` estiver desatualizada ou tiver sido ajustada para outra postura de risco, a triagem está desatualizada. Reconfira antes de habilitar execuções agendadas.
- **A watchlist é uma asserção de cobertura que você fez.** Um regulador fora da watchlist ainda pode publicar algo material. Não cobrir um regulador é bug de configuração, não bug de feed.

## Deploy

```bash
export ANTHROPIC_API_KEY=sk-ant-...
export GDRIVE_MCP_URL=...
../../scripts/deploy-managed-agent.sh reg-monitor
```

## Eventos de steering

Veja [`steering-examples.json`](./steering-examples.json). A varredura semanal padrão usa o primeiro exemplo. Os outros dois cobrem verificações profundas direcionadas em um desenvolvimento específico e análise de gap em um item sinalizado.

## Segurança & handoffs

Conteúdo de feed regulatório (entradas do DOU, posts de RSS das agências, notificações de alertas de provedores) é **input não confiável.** Isolamento em três tiers:

| Tier | Toca docs não confiáveis? | Ferramentas | Conectores |
|---|---|---|---|
| **`feed-reader`** | **Sim** | `Read`, `Grep`, `WebFetch` apenas | Nenhum |
| `materiality-filter` / Orquestrador | Não | `Read`, `Grep`, `Glob`, `Agent` | gdrive (apenas orquestrador) |
| **`digest-writer`** (detentor de Write) | Não | `Read`, `Write`, `Edit` | Nenhum |

`feed-reader` retorna JSON validado por schema e com limite de tamanho. `materiality-filter` é computação pura sobre esse JSON mais a configuração regulatório-jurídica em disco — sem MCP, sem web. `digest-writer` produz `./out/reg-digest-<YYYY-MM-DD>.md` e emite um `handoff_request` para entrega via Slack.

**Handoffs:** o orquestrador roteia o `handoff_request` do `digest-writer` para um worker de envio Slack usando o canal da configuração de House style do time. O agente nunca envia mensagens de Slack por conta própria.

**Não garantido:** este agente faz aflorar mudanças e sinaliza gaps potenciais de política; advogado(a) decide se uma mudança regulatória exige ação e quem é responsável pela resposta.

## Notas de adaptação

Antes de confiar na saída no seu workflow:

- **Aponte o `feed-reader` para suas fontes.** O alvo padrão BR é o **DOU** (Diário Oficial da União — in.gov.br) e os diários/RSS de cada agência reguladora. Cobertura mínima sugerida: **ANPD** (proteção de dados), **CVM** (in.cvm.gov.br — mercado de capitais), **BCB** (bcb.gov.br/estabilidadefinanceira/normativos — Resoluções BCB/CMN, Circulares, Cartas Circulares, Comunicados), **ANVISA**, **ANATEL**, **ANS**, **ANEEL**, **ANP**, **ANTT**, **ANAC**, **IBAMA**, **CADE** (concorrência), **SENACON** e **Procons** estaduais (consumo), **COAF** (PLD/FT), **CFM** (saúde). Para jurisprudência regulatória relevante adicione: **STF** (Recursos Extraordinários e Temas de Repercussão Geral, ADIs, ADPFs, Súmulas Vinculantes), **STJ** (Temas Repetitivos, Súmulas), **TST** (Súmulas, OJs), **CARF** (contencioso tributário administrativo) e **CNJ** (Resoluções). Se sua firma assina Thomson Reuters Regulatory Intelligence, Bloomberg Law ou feeds proprietários BR (LexML, JusBrasil, Aasp, Migalhas), adicione os endpoints ao allowlist de `web_fetch` do feed-reader e ajuste o plano de varredura do orquestrador. Se só houver fontes públicas, DOU + RSS das agências é suficiente.
- **Configure as URLs MCP do Thomson Reuters (opcional).** TR está comentado no manifesto; conecte e troque para `enabled: true` se seu time pagar por ele.
- **Tipos de norma a rastrear.** Defina no plano de varredura quais espécies o agente deve capturar: **Lei** (federal), **Medida Provisória** (MP), **Decreto**, **Portaria**, **Instrução Normativa**, **Resolução** (CD/ANPD, CVM, BCB/CMN, etc.), **Circular**, **Carta Circular**, **Comunicado**, **Súmula** e **Resolução conjunta**. Para consultas públicas (análogo funcional ao notice-and-comment), cada agência tem regime próprio — base legal: **Lei 9.784/1999** (Processo Administrativo Federal) e, para análise de impacto, **AIR — Análise de Impacto Regulatório** (Lei 13.874/2019 da Liberdade Econômica + Decreto 10.411/2020). Decisões administrativas devem observar **LINDB** (DL 4.657/1942, alterada pela Lei 13.655/2018), arts. 20–30.
- **Calendário regulatório típico (BR).** Inclua no scan plan: resoluções pendentes da **ANPD**, audiências públicas da **CVM**, consultas do **BCB**, atos de concentração do **CADE**.
- **Sigilo regulatório.** Muitos processos administrativos têm publicidade restrita; a **LGPD** afeta tratamento de dados pessoais (incluindo CNPJ ativo associado a pessoa física). Não exfiltre conteúdo de processos sob sigilo e respeite os marcadores de confidencialidade publicados pelos órgãos.
- **Configure o canal de entrega do digest.** O digest-writer emite um `handoff_request` que nomeia um canal de Slack. O orquestrador lê esse canal da configuração regulatório-jurídica em **House style → Digest regulatório**. Defina antes da primeira execução agendada ou o handoff vai para dead-letter. Times que preferem o digest por e-mail ou em página do Confluence devem trocar o alvo do handoff no allowlist do orquestrador.
- **Ajuste o limiar de materialidade.** O materiality-filter lê a seção `## Limiar de materialidade` da sua configuração — sempre material / digno de revisão / FYI. Confirme que os tiers refletem sua postura de risco atual antes de habilitar execuções agendadas; limiar baixo inunda o digest, alto demais e você perde obrigações com prazo.
- **Atualize a watchlist.** O materiality-filter também lê a tabela `## Reguladores que monitoramos`. Acrescente ou remova reguladores conforme seu footprint muda.
- **Confirme o cabeçalho de material de trabalho.** O append headless em `agent.yaml` instrui o agente a prepender o cabeçalho de material de trabalho da sua configuração. Verifique a redação com a Diretoria Jurídica/GC antes de ligar. Use: `MATERIAL DE TRABALHO — PRIVILEGIADO E CONFIDENCIAL (Art. 7º, XIX, EOAB)`.
- **Cadência.** Semanal é o padrão. Ambientes regulatórios ativos (ciclos de rulemaking em serviços financeiros — BCB/CVM, regulação de IA cross-border, ANPD em fase de produção normativa) podem justificar diário. A cadência vive no seu próprio engine de workflow — o cookbook não se agenda sozinho.
