---
name: matter-intake
description: Intake de nova matéria — perguntas uniformes cobrindo identificação, conflitos, fonte, triagem de risco, materialidade, escritório externo, responsáveis, preservação documental e datas-chave; escreve matter.md e history.md e anexa linha estruturada a _log.yaml. Use quando o(a) usuário(a) disser "nova matéria", "intake desta matéria", ou quiser trazer nova matéria ao portfólio.
argument-hint: "[nome opcional da matéria]"
---

# /matter-intake

1. Carregar `~/.claude/plugins/config/claude-for-legal/contencioso/CLAUDE.md` → calibração de risco (para triagem), panorama (para contexto, método de conflito), stakeholders (para quem envolver).
2. Seguir o workflow e referência abaixo.
3. Rodar intake uniforme: identificação, conferência de conflitos, fonte, triagem de risco, materialidade, escritório externo, responsáveis internos, preservação documental, datas-chave, postura inicial.
4. Gerar slug a partir do nome (minúsculas, hífens, ano).
5. Criar `~/.claude/plugins/config/claude-for-legal/contencioso/matters/[slug]/matter.md` — intake narrativo completo.
6. Criar `~/.claude/plugins/config/claude-for-legal/contencioso/matters/[slug]/history.md` — semeado com o intake como primeira entrada.
7. Anexar linha estruturada a `~/.claude/plugins/config/claude-for-legal/contencioso/matters/_log.yaml`.
8. Confirmar com o(a) usuário(a): "Eis a linha que vou escrever — alguma edição?"

---

# Intake de Matéria

## Propósito

Toda nova matéria passa pelo mesmo intake para o portfólio ficar comparável. Linhas uniformes em `_log.yaml` permitem a skill de status fazer rollup. Narrativa em `matter.md` captura o que a linha não captura. Arquivo de histórico semeado aqui vira o registro de eventos.

## Carregar contexto

- `~/.claude/plugins/config/claude-for-legal/contencioso/CLAUDE.md` — calibração de risco (limites de triagem, materialidade, alçada de acordo), panorama (stakeholders, banco de escritórios externos).
- `~/.claude/plugins/config/claude-for-legal/contencioso/matters/_log.yaml` — confirmar unicidade do slug.

## O intake

### 1. Identificação

- Nome da matéria (como comumente referida, ex. "Acme c/ Empresa 2026")
- Contraparte
- Tipo de matéria: `contratual | trabalhista | pi | regulatório | investigação | produto | consumidor | coletiva | outro`
- Nosso polo: `autor | réu | interessado | investigado`
  - Se o `## Polo` do perfil prático for `autor`, `réu`, ou variante "ambos — padrão X", pré-preencher do default e confirmar. Se `## Polo` for `varia por matéria`, perguntar do zero. Nunca assumir silenciosamente postura que o perfil prático não definiu.
  - O polo dirige skills downstream: matérias polo autor roteiam triagem de risco para valor da causa / economia de honorários de êxito; matérias polo réu roteiam para exposição / provisão / acionamento de seguro.
- Jurisdição (tribunal, foro arbitral, ou regulador). Padrão CNJ: número 20 dígitos (Res. CNJ 65/2008) se já distribuído.

### 2. Conferência de conflitos

Antes de prosseguir, rodar a etapa de conflitos conforme `~/.claude/plugins/config/claude-for-legal/contencioso/CLAUDE.md` → Conferência de conflitos.

- **Status:** `cleared | pending | not-run | waived`
- **Método:** combinar com o que `~/.claude/plugins/config/claude-for-legal/contencioso/CLAUDE.md` declara (`societario | outside-counsel | system-check | informal | outro`). Se o método declarado é `informal`, diga — o registro ainda captura que a base foi julgamento do(a) advogado(a).
- **Conferido por:** nome / time / banca
- **Data:** AAAA-MM-DD
- **Conferido contra:** lista breve dos nomes/entidades efetivamente rodados (contraparte, afiliadas conhecidas, advogado(a) adverso(a) se conhecido(a), testemunhas-chave). Fino tudo bem; "não" não.
- **Notas:** qualquer coisa flagada mas cleared (ex. "Silva no nosso conselho atuou no conselho da contraparte 2019–2021 — cleared como não-sobreposto a esta matéria").

Comportamento por status:

- `cleared` → prosseguir.
- `pending` → prosseguir com intake; flagar com destaque em `matter.md` e na linha do log que conflitos estão pendentes; trazer à tona em todo `/matter-update` e `/portfolio-status` até resolver.
- `waived` → raro; exige racional de renúncia de conflito (escrever a renúncia está fora desta skill — capture que existe, quem assinou, e onde vive).
- `not-run` → **PARE. Isso é portão.** A skill não criará `matter.md`, `history.md` nem linha em `_log.yaml` até a postura de conflito ser resolvida. Três caminhos aceitáveis:

  **Caminho 1 — Rodar conflito agora.** Pausa este intake. Limpe conforme `~/.claude/plugins/config/claude-for-legal/contencioso/CLAUDE.md` Conferência de conflitos. Volte com `status: cleared` ou `status: waived` com racional.

  **Caminho 2 — Marcar pendente com responsável + prazo.** Permitido só quando `~/.claude/plugins/config/claude-for-legal/contencioso/CLAUDE.md` Conferência de conflitos declara intake paralelo aceitável. Capture: quem está rodando, quando deve voltar, quais entidades está checando. Intake prossegue; linha de matéria carrega `conflicts.status: pending`; `/portfolio-status` flaga em todo run; `/matter-update` re-pergunta até resolver.

  **Caminho 3 — Bypass com racional documentado.** Só se o(a) usuário(a) reconhecer explicitamente o bypass. Registrar em `conflicts.override`:

  ```yaml
  conflicts:
    status: not-run               # preservado como está
    override:
      by: [nome do(a) usuário(a)]
      date: [AAAA-MM-DD]
      rationale: [por que conflitos foram bypassados — registro permanente; não expira automaticamente]
  ```

  Esse campo é visível em todo `/portfolio-status`, todo briefing `/matter`, e todo `/matter-update` até ser removido. Nunca é removido pela skill — só por edição explícita do(a) usuário(a) em `_log.yaml` depois de conflitos efetivamente conferidos.

  **Não prossiga silenciosamente.** "Eu faço depois" não é resposta aceitável. Um dos Caminhos 1/2/3 tem que ser escolhido, e a escolha entra no registro.

Esta etapa não é a skill decidindo se existe conflito — isso é julgamento do(a) usuário(a) / banca. É garantir que a checagem aconteceu e o registro reflete.

### 3. Fonte

Como chegou?
- `notificacao-extrajudicial | citacao | oficio | regulador | report-interno | ameaca-pre-judicial`
- *Oportunidade de seed doc:* "Se você tem o documento iniciador (petição inicial, notificação, ofício), anexe ou compartilhe o caminho. Afia o intake."

### 4. Triagem de risco — contra calibração da casa

- Severidade: alta | média | baixa (referenciar as faixas de severidade em `~/.claude/plugins/config/claude-for-legal/contencioso/CLAUDE.md`)
- Probabilidade: alta | média | baixa (referenciar as faixas em `~/.claude/plugins/config/claude-for-legal/contencioso/CLAUDE.md`)
- Risco resultante (pela matriz): alta | média | baixa | crítica
- Faixa de exposição (em danos materiais CC art. 402 + danos morais CC art. 944 + sucumbência CPC art. 85)
- Exposição não-monetária (tutela de urgência CPC art. 300? acordo coletivo? publicidade? precedente / Tema Repetitivo / IRDR?)

Se a calibração de risco em `~/.claude/plugins/config/claude-for-legal/contencioso/CLAUDE.md` está fina, não finja precisão. Use o tato do(a) usuário(a) e anote a finura.

### 5. Materialidade

Contra os limites da casa em `~/.claude/plugins/config/claude-for-legal/contencioso/CLAUDE.md`:
- `reserved | disclosed | monitored | none`
- Se `reserved`: valor de provisão (CPC 25) e se finanças foram notificadas
- Se `disclosed`: divulgação em DFP/ITR ou Fato Relevante (Res. CVM 80 / Res. CVM 44) e localização

### 6. Escritório externo

- Banca
- Sócio(a) líder
- **E-mail do(a) sócio(a) líder** (usado por `/oc-status` para minutar pedidos de status)
- Status de contrato de prestação: `assinado | pendente | nenhum`
- Autorização de budget: valor e aprovador(a)
- *Oportunidade de seed doc:* "Caminho do contrato de prestação, se assinado."

Se risco é médio ou maior e nenhum escritório externo designado — flage.

### 7. Responsáveis internos

A partir do panorama em `~/.claude/plugins/config/claude-for-legal/contencioso/CLAUDE.md` — quais stakeholders internos estão envolvidos?
- Líder de negócio
- Parceiro(a) de RH (se trabalhista)
- Contato de Comunicação (se risco reputacional)
- CISO (se dados ou cyber)
- Outros

### 8. Preservação documental

- Emitida? Se sim: data, escopo, custodiantes (lista de nomes).
- Próxima renovação (default: seis meses da emissão; ajustar por matéria).
- Se não e isto é contencioso ativo ou razoavelmente antecipado: flage com urgência; ofereça rodar `/contencioso:legal-hold [slug] --issue` depois do intake. Base: CC arts. 186 e 927 (dever de boa-fé documental); CPC art. 400 (efeito da recusa de exibição — presunção de veracidade); LGPD impõe boa-fé na guarda de dados pessoais.
- *Oportunidade de seed doc:* "Comunicação de preservação, se emitida."

### 9. Datas-chave

- Prazo de resposta (contestação CPC art. 335 — 15 dias úteis; impugnação; recurso — CPC art. 1003 §5º — 15 dias úteis salvo embargos de declaração 5 dias; trabalhista — 8 dias úteis para a maioria dos recursos, Res. CSJT 185/2017; CPP — dias corridos)
- Próxima audiência / conciliação (CPC arts. 334 / 358)
- Limite prescricional (CC arts. 205-206 — 10 anos regra geral, 3 anos para reparação civil; CDC art. 27 — 5 anos para reparação por fato do produto/serviço; CLT art. 11 — 5 anos no curso do contrato / 2 anos da extinção)
- Quaisquer prazos regulatórios

### 10. Postura inicial

Teoria de um parágrafo:
- Qual é a nossa história?
- Qual é a deles?
- Qual é o fato pivô?
- Postura inicial: `brigar | acordar | investigar | esperar`

## Escrita das saídas

### Slug

Minúsculas, hífens, ano no final. Exemplos: `acme-c-empresa-2026`, `trabalhista-silva-2026`, `anpd-consulta-2026`.

Confirmar unicidade do slug em `_log.yaml` antes de escrever.

### `~/.claude/plugins/config/claude-for-legal/contencioso/matters/[slug]/matter.md`

```markdown
[CABEÇALHO DE MATERIAL DE TRABALHO — conforme config do plugin ## Saídas — varia por papel; ver `## Quem está usando`]

# [Nome da Matéria]

**Slug:** [slug]
**Aberta:** [AAAA-MM-DD]
**Nosso polo:** [autor/réu/etc.]
**Status:** [status]

---

## Identificação

[contraparte, jurisdição/foro, tipo de matéria, fonte, nº CNJ se distribuído — padrão Res. CNJ 65/2008]

## Conflitos

**Status:** [cleared / pending / not-run / waived]
**Método:** [societario / outside-counsel / system-check / informal / outro]
**Conferido por:** [nome]
**Data:** [AAAA-MM-DD]
**Conferido contra:** [entidades]
**Notas:** [qualquer flag cleared, referência de renúncia se aplicável]

## Triagem de risco

**Severidade:** [faixa] — [por quê, com referência às definições da casa]
**Probabilidade:** [faixa] — [por quê]
**Risco:** [alta/média/baixa/crítica]
**Exposição:** [faixa em R$ + não-monetária — tutela inibitória/antecipada, publicidade, precedente]

## Materialidade

[reserved/disclosed/monitored/none — com valor de provisão CPC 25, localização da divulgação DFP/ITR/Fato Relevante, ou racional se "none"]

## Escritório externo

[banca, líder, status de contrato, budget]

## Responsáveis internos

[stakeholders e por que cada um está envolvido]

## Preservação documental

[status, data, escopo]

## Datas-chave

[lista — observar dias úteis CPC art. 219 para prazos do CPC, dias úteis CLT no JT, dias corridos no CPP]

## Teoria inicial

[um parágrafo: nossa história, história deles, fato pivô, postura inicial] `[review — teoria no intake é hipótese de trabalho; confirmar com escritório externo antes de qualquer peça ou comunicação material que assuma este enquadramento]`

## Perguntas em aberto

[qualquer coisa ainda não sabida que importe — ex. "acionamento de seguro pendente", "incerto se há cobertura para X"]

---

## Seed documents

| Doc | Caminho / pointer |
|---|---|
| [ex. petição inicial] | [caminho ou "ainda não compartilhado"] |
```

### `~/.claude/plugins/config/claude-for-legal/contencioso/matters/[slug]/history.md`

Semear o histórico com o intake como entrada zero:

```markdown
# Histórico: [Nome da Matéria]

Log de eventos append-only. Mais recente no topo.

---

## [AAAA-MM-DD] — Matéria aberta

[Fonte, quem trouxe, resumo da triagem inicial, escritório externo designado, preservação documental emitida sim/não.]
```

### Anexar a `~/.claude/plugins/config/claude-for-legal/contencioso/matters/_log.yaml`

Adicionar linha conforme o schema. Exemplo:

```yaml
- id: acme-c-empresa-2026
  name: "Acme S.A. c/ Empresa"
  cnj: "1234567-89.2026.8.26.0100"   # padrão Res. CNJ 65/2008
  type: contratual
  role: reu
  counterparty: "Acme S.A."
  jurisdiction: "TJSP — 1ª Câm. Empresarial"
  # status é derivado da fonte:
  #   source: ameaca-pre-judicial | notificacao-extrajudicial    → status: threatened
  #   source: citacao | oficio | regulador                        → status: active
  #   source: report-interno                                      → status: threatened (default) ou active se processo formal já iniciou
  status: active
  stage: contestacao
  source: citacao
  outside_counsel:
    firm: "Silva & Souza Advogados"
    lead: "Dr. J. Reyes"
    email: "jreyes@silvasouza.example.com"
    engagement: signed
  conflicts:
    status: cleared
    method: societario
    cleared_by: "K. Patel"
    cleared_date: 2026-04-20
    override:                   # populado só em bypass Caminho 3
      by: null
      date: null
      rationale: null
  risk: high
  materiality: reserved
  exposure_range: "R$10M–R$25M"
  internal_owners:
    business_lead: "Jane Silva"
    hr_partner: null
    comms_contact: null
  legal_hold:
    issued: true
    issued_date: 2026-02-15
    scope: "Time comercial 2023–2026"
    custodians: ["Jane Silva", "R. Chen", "T. Patel"]
    last_refresh: 2026-02-15
    next_refresh: 2026-08-15
    released: null
  related_matters: []
  opened: 2026-04-20
  next_deadline: 2026-05-15
  last_updated: 2026-04-20
  path: matters/acme-c-empresa-2026/
```

## Confirmar antes de escrever

Mostre ao(à) usuário(a) a linha e o conteúdo do matter.md:

> Eis o que vou escrever. Flage o que estiver errado ou fino antes de eu commitar.

## Feche com a árvore de próximos passos

Feche com a árvore de próximos passos conforme CLAUDE.md `## Saídas`. Customize as opções ao que esta skill acabou de produzir — os cinco ramos default (redigir o X, escalar, buscar mais fatos, observar e esperar, outra coisa) são partida, não trava. A árvore É a saída; o(a) advogado(a) escolhe.

## O que esta skill NÃO faz

- **Rodar a conferência de conflito.** Registra o resultado, status, método e entidades checadas. A liberação efetiva acontece no sistema (ou julgamento) que o perfil prático declara. Se o(a) usuário(a) diz "cleared", a skill aceita e captura a metadata.
- Decidir a teoria inicial. Captura o que o(a) usuário(a) diz; não inventa.
- Emitir a preservação documental. Flag se faltando. Usuário(a) emite.
