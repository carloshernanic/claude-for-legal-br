---
name: closing-checklist
description: >
  O que está travando o fechamento — mantenha o checklist de closing com status,
  caminho crítico e dias até o fechamento. Auto-atualizável: ingere novos itens
  de achados de diligência e da elaboração de schedules, acompanha status,
  surface o que está travando. Use quando o(a) usuário(a) disser "checklist de
  fechamento", "o que falta para fechar", "status do checklist", "adicione ao
  checklist", ou em pull de status agendado.
argument-hint: "[opcional: ID do item + atualização de status]"
---

# /closing-checklist

> **Adaptação BR não oficial.** Calibrado para fechamentos de M&A no Brasil — CADE (Lei 12.529/2011), CVM, BCB (RDE-IED), Juntas Comerciais, ANPD (Lei 13.709/2018). Veja `references/terminology-us-to-br.md`. Toda saída é rascunho para revisão por advogado(a) inscrito(a) na OAB.

1. Leia `~/.claude/plugins/config/claude-for-legal/societario/deals/[code]/closing-checklist.yaml` e use os modos abaixo.
2. Se houver atualização de status: Modo 3 (atualizar item).
3. Caso contrário Modo 4: itens travando, caminho crítico, dias até fechamento.

---

## Contexto de matéria

**Contexto de matéria.** Verifique `## Workspaces de matéria` no CLAUDE.md nível-prática. Se `Habilitado` for `✗` (padrão para in-house), pule este parágrafo — skills usam contexto nível-prática. Se habilitado e sem matéria ativa, pergunte: "Qual matéria? Rode `/societario:matter-workspace switch <slug>` ou diga `nível-prática`." Carregue `matter.md` da matéria ativa. Grave saídas na pasta da matéria. Nunca leia arquivos de outra matéria a menos que `Contexto cross-matter` esteja `on`.

---

## Propósito

Operações fecham quando o checklist está pronto. Tudo cumprido. Nada faltando. Esta skill mantém a lista, ingere novos itens conforme surgem na diligência, e diz ao time o que está travando.

## O checklist

Vive em `~/.claude/plugins/config/claude-for-legal/societario/deals/[code]/closing-checklist.yaml`. Estrutura:

```yaml
deal_code: "Projeto Falcão"
target_close: [DATA]
signing_date: [DATA]
last_updated: [DATA]

condicoes_precedentes:
  - id: CP-001
    item: "Aprovação CADE — expiração do prazo de análise"
    category: "Regulatório"
    responsible: "Advogado(a) externo(a) do comprador"
    due: 2026-04-15
    status: "Submissão protocolada em 2026-03-01, prazo em curso (Lei 12.529/2011 art. 88 — operações acima de R$ 750mi e R$ 75mi)"
    blocking: true
    source: "Contrato de Compra e Venda §7.1(a)"

  - id: CP-002
    item: "Anuência da Acme S.A. para cessão de contrato"
    category: "Anuências de terceiros"
    responsible: "Vendedor — Jane Doe"
    due: 2026-04-20
    status: "Pedido enviado em 2026-03-10, sem resposta"
    blocking: true
    source: "Anexo 3.12(a)(4); Contrato MSA Acme §14.2"

entregaveis_fechamento:
  - id: CD-001
    item: "Certidões de regularidade — Target (federais, RFB/PGFN, FGTS, trabalhista, ações cíveis e tributárias)"
    category: "Societário/Tributário/Trabalhista"
    responsible: "Advogado(a) do vendedor"
    due: 2026-04-28
    status: "Não iniciado"
    blocking: true
    source: "Contrato de Compra e Venda §2.3(b)(iv)"

  # ... etc
```

## Modos

### Modo 1: Inicializar a partir do contrato

Leia o Contrato de Compra e Venda (SPA) assinado (ou em fase final). Extraia:

- Toda condição precedente (varia por contrato — leia os títulos efetivos)
- Todo entregável de fechamento (anexo de entregáveis ou seção correspondente)
- Toda obrigação com prazo pré-fechamento

Cada um vira item do checklist com citação à seção.

**Pesquise antes de popular itens regulatórios/aprovações.** Aprovações antitruste, de investimento estrangeiro e setoriais (ex.: notificação CADE — Lei 12.529/2011 art. 88 com thresholds atuais R$ 750mi e R$ 75mi, sujeitos a atualização por portaria conjunta MJ/MF; aprovação BCB para investimento externo via RDE-IED; aprovações setoriais — ANATEL, ANEEL, SUSEP, ANS, ANVISA conforme setor) têm mecânica, limiares e janelas de prazo específicos que mudam. Extraia o nome de cada condição regulatória do contrato, então pesquise a mecânica vigente (quem protocola, quando, o que dispara prazo adicional, qual o prazo de análise). Cite fontes primárias e verifique vigência. Não popule premissa de prazo de memória.

**Cláusula de efeito adverso relevante (MAC).** Puxe a definição do contrato — MAC é negociada, não padrão. No Brasil, jurisprudência STJ sobre MAC ainda é escassa; análise tipicamente recorre a (i) onerosidade excessiva (CC arts. 478-480), (ii) imprevisão (CC art. 317), e (iii) interpretação contratual (CC arts. 113 e 423). Pesquise a interpretação dos tribunais para a linguagem específica antes de sinalizar evento como gatilho potencial de MAC.

**Extração de exigências de anuência em contratos materiais** depende das regras supletivas e da cláusula anti-cessão específica de cada contrato. Cessão de posição contratual exige anuência (CC arts. 286-298 cessão de crédito; arts. 299-303 assunção de dívida; jurisprudência STJ sobre cessão de posição contratual). Pesquise por contrato em vez de assumir padrão.

### Modo 2: Ingerir da diligência (a parte "auto-atualizável")

Modo 2 é acionado quando skill upstream produz achado com ação pré-fechamento. Skills upstream e tipos de output que este modo ingere:

- **Achados de `diligence-issue-extraction`** — qualquer achado marcado para ação de fechamento (anuência, aprovação em assembleia, deliberação do conselho, protocolo regulatório, quitação, mecânica de escrow, carta de quitação). Não só "anuências" — veja seção Handoffs daquela skill.
- **Itens de mudança de controle/cessão de `material-contract-schedule`** — cláusulas de mudança de controle, anti-cessão, MFN identificadas na elaboração do schedule.
- **Output de `deal-team-summary`** — o briefing executivo agrega achados de extração e ocasionalmente surface item de ação de fechamento que leitura mecânica dos memos individuais perderia (ex.: matéria de §117 Lei 6.404 — abuso de controle, ou pacote composto de anuências de credores na liberação de garantias).

O schema de handoff cobre o leque completo de ações pré-fechamento, não só anuências:

```yaml
handoff:
  # Campos obrigatórios
  item: "[Contraparte ou ação, uma linha]"
  category: "[Anuências de terceiros | Ato societário (assembleia/conselho) | Protocolo regulatório | Quitação / liberação de garantia | Escrow / retenção | Entregável de fechamento]"
  source: "[Nome do contrato / dispositivo / caminho VDR + Bates]"
  blocking: true  # a menos que o contrato tenha qualificador de materialidade
  severity: "[🔴 / 🟠 / 🟡 / 🟢 — carregado upstream, veja regra de piso de severidade em CLAUDE.md]"

  # Campos de anuência / ação de terceiro
  counterparty: "[ex.: Dunmore Participações Ltda.]"
  guarantor: "[ex.: garantia da matriz do comprador exigida, ou N/A]"
  conditions: "[qualquer condição substantiva — ex.: 'substituição de garantia pela matriz do comprador antes da anuência produzir efeitos']"
  notice_deadline: "[ex.: 30 dias antes do fechamento, ou data específica]"

  # Campos de ato societário
  approval_body: "[Assembleia | Conselho | Comitê | Regulador]"
  approval_threshold: "[ex.: maioria absoluta dos não-controladores em matéria de art. 115 §1º Lei 6.404; tag-along 254-A; aprovação de operação com parte relacionada]"
  statutory_or_charter_source: "[ex.: Lei 6.404 art. 254-A; Estatuto art. IV §2; CC art. 1.071]"

  # Timing
  estimated_time_to_complete: "[ex.: 30 dias]"
  must_occur_before: "[ex.: fechamento | assinatura | fim de período]"
```

Preserve todo campo populado pela skill upstream. Anuência Dunmore com condição de substituição de garantia e prazo de 30 dias deve surfacer no checklist com os três elementos (anuência, garantia, prazo), não colapsar a "anuência Dunmore à mudança de controle". Quando upstream fornecer severidade, carregue — veja regra de piso de severidade em `CLAUDE.md`.

Adicione ao checklist. Deduplique por (contraparte + tipo de ação), não por nome livre do item — anuência Dunmore e quitação Dunmore são itens diferentes mesmo nomeando Dunmore. Ao deduplicar, mescle campos em vez de sobrescrever.

### Modo 3: Atualização de status

Usuário(a) (ou agente dataroom-watcher) fornece atualização. Localize item, atualize status e data.

```
/societario:closing-checklist
CP-002: Acme respondeu, formulário de anuência anexado, falta contra-assinatura
```

### Modo 4: O que está travando

```markdown
[CABEÇALHO DE MATERIAL DE TRABALHO — conforme config ## Outputs — varia por papel; veja `## Quem está usando`]

> Este relatório de status decorre do contrato, achados de diligência e registros internos. Herda o status de privilégio e confidencialidade — distribuição fora do círculo de sigilo (contraparte, times de negócios mais amplos) pode quebrar o sigilo. Confirme a lista de distribuição antes de enviar.

## Status do Checklist de Fechamento — [Deal code] — [data]

**Fechamento previsto:** [data] ([N] dias)
**Itens:** [N] total — [N] concluídos, [N] em andamento, [N] não iniciados

### 🔴 Travando e em risco

| ID | Item | Vencimento | Status | Dias |
|---|---|---|---|---|
| [CP-XXX] | [item] | [data] | [status] | **[N]** |

### 🟡 Travando, no prazo

[mesma tabela]

### ✅ Concluído

[N] itens — [lista colapsada]

### Não travando (pós-fechamento, informativo)

[N] itens

---

**Caminho crítico:** [O(s) item(ns) que, se atrasar(em), empurra(m) a data de fechamento]
```

## Análise de caminho crítico

Nem todo item travando é igual. Anuência que leva 30 dias é caminho crítico. Certidão de regularidade que leva 5 dias para emitir não é, mesmo ambos travando. Aprovação CADE em rito ordinário pode levar 240 dias úteis (art. 88 §9º Lei 12.529/2011) — frequentemente caminho crítico em deals com notificação obrigatória.

Para cada item travando, estime tempo-para-concluir. Aqueles em que `(data prazo - hoje) < tempo estimado` estão em risco. Vão para o topo de todo status report.

Se o checklist tem mais de ~10 itens, ou quando o(a) usuário(a) pedir: ofereça o dashboard (veja CLAUDE.md `## Outputs → Oferta de dashboard`). Modele a oferta para este output — contagens por status (concluído / em andamento / não iniciado / em risco), visão de caminho crítico agrupada por workstream, e grade ordenável com item, dono, prazo e dias.

## Integração: agente dataroom-watcher

O agente verifica o checklist diariamente, puxa atualizações de status de email/Slack se conectados, e posta o relatório de "o que está travando" para o canal do time. Modo 4 é a saída do agente.

## Gate de ação consequente (certificar fechamento)

**Antes de produzir certificação "pronto para fechar / todas as CPs satisfeitas" ou memo de fechamento:** Leia `## Quem está usando` em `CLAUDE.md`. Se o Papel for **Não-advogado(a)**:

> Certificar que condições de fechamento foram satisfeitas (ou produzir memo de fechamento afirmando isso) tem consequências jurídicas — é o sinal que dispara o fluxo de pagamento e obrigações pós-fechamento. Você revisou com advogado(a)? Se sim, prossiga. Se não, eis briefing:
>
> - Lista completa de CPs com status (concluído, em andamento, não iniciado)
> - Itens em que evidência de cumprimento é frágil ou ausente
> - Renúncias (waivers) ou cartas-acordo necessárias para itens que não fecharão a tempo
> - Questões em aberto (anuências de contrapartes pendentes, risco de MAC/atualização de declarações)
> - O que perguntar ao(à) advogado(a): isto está pronto para ser declarado fechado; algum CP está sendo ignorado indevidamente; o que precisa entrar em anexo de exceções
>
> Para localizar advogado(a): consulte a OAB de sua seccional, que oferece busca pública e referência.

Não produza certificação final "pronto para fechar" após este gate sem "sim" explícito. Tracking de status e relatórios "o que está travando" não exigem o gate.

---

## O que esta skill não faz

- Não obtém anuências, protocola formulários ou minuta documentos. Acompanha que precisam ser feitos.
- Não decide o que está travando — o contrato decide. Esta skill lê o contrato.
- Não fecha a operação. Diz quando você pode.
