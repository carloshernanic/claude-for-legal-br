---
name: policy-diff
description: Diff de uma mudança regulatória específica contra a biblioteca indexada de políticas. Use quando uma norma mudou e for preciso saber quais políticas ela toca e qual a lacuna, quando o usuário disser "diff esta norma contra nossas políticas", "qual política isso afeta", ou "análise de lacunas", ou quando o reg-feed-watcher entregar um item material.
argument-hint: "[nome da norma, ou cole texto/sumário da norma]"
---

> **Adaptação BR não oficial.** Calibrado para Lei 9.784/1999, LINDB arts. 20-30 (Lei 13.655/2018), vacatio legis LINDB art. 1º, Dec. 10.411/2020 (AIR). Reguladores BR: ANPD, CVM, BCB, ANVISA, ANATEL, ANS, ANEEL, ANP, ANTT, ANAC, IBAMA, CADE, SENACON, Procons, COAF.

# /policy-diff

1. Carregue `~/.claude/plugins/config/claude-for-legal/regulatorio/CLAUDE.md` → índice da biblioteca de políticas.
2. Use o workflow abaixo.
3. Extraia exigências da norma. Mapeie contra políticas indexadas.
4. Saída: análise de lacuna por exigência, qual política precisa atualização.

---

## Matter context

**Matter context.** Verifique `## Matter workspaces` no CLAUDE.md practice-level. Se `Enabled` for `✗` (default para usuários in-house), pule o resto deste parágrafo — skills usam contexto practice-level e a maquinaria de matter é invisível. Se habilitado e sem matter ativa, pergunte: "Para qual matter é isso? Rode `/regulatorio:matter-workspace switch <slug>` ou diga `practice-level`." Carregue o `matter.md` da matter ativa. Escreva outputs na pasta da matter em `~/.claude/plugins/config/claude-for-legal/regulatorio/matters/<matter-slug>/`. Nunca leia arquivos de outra matter a menos que `Cross-matter context` esteja `on`.

---

## Purpose

Uma norma mudou. Você tem políticas. Esta skill encontra quais políticas a mudança toca e qual é a lacuna entre "o que a norma agora exige" e "o que a política diz".

## Load context

`~/.claude/plugins/config/claude-for-legal/regulatorio/CLAUDE.md` → índice da biblioteca de políticas (políticas, localizações, owners).

## Scope integrity

Se o usuário pedir para excluir seção, exigência, ou categoria do diff:

1. Faça — o usuário é dono do escopo.
2. Mas sinalize, loud e permanente: "⚠️ LIMITAÇÃO DE ESCOPO: Seção [X] excluída a pedido do usuário. Este diff não reflete a política completa. Lacunas na área excluída NÃO são identificadas." Acima do header, propagada a qualquer artefato downstream.
3. Entregue a flag ao `gap-surfacer`: "Este diff teve escopo limitado. Não represente como quadro completo de compliance." Inclua o banner verbatim em qualquer entrada do gap tracker derivada deste diff.
4. Note o que a exclusão significa: "Excluir gestão de fornecedores significa que o diff mostrará 'nenhuma política endereça gestão de fornecedores' — o que é pior do que mostrar a lacuna."

Um artefato de compliance construído sobre exclusão de escopo não revelada parece ocultação em investigação. A flag é a diferença entre "limitamos a revisão" e "escondemos o problema". Em PAS de ANPD/CVM/CADE, atestações sobre compliance podem ser pedidas — e descobertas sobre escopo escondido prejudicam a defesa.

## Workflow

### Step 0: Verifique o status da norma antes de diff

Antes de fazer diff de uma norma contra política, confirme que a norma está realmente em vigor. Red flags de que pode não estar:

- A data de aplicabilidade/vigência passou há mais de 30 dias mas não há confirmação que não foi prorrogada (vacatio legis pode ter sido estendido por norma posterior — LINDB art. 1º)
- A norma tem mais de 12 meses
- A norma é final em rulemaking politicamente contencioso (resoluções da ANPD, CVM ou BCB são frequentemente questionadas; ADIs pendentes no STF)

Quando vir red flag, cheque (via research MCP, web search se habilitado, ou DOU em in.gov.br) para: prorrogações, suspensões, liminares, decisões de TCU, ADIs com efeito suspensivo, revogações, ou alterações. Se puder checar e a norma estiver confirmada em vigor, prossiga. Se não puder verificar (sem ferramentas conectadas), emita este banner ACIMA do header, antes de qualquer conteúdo:

> `⚠️ STATUS DA NORMA NÃO VERIFICADO — Não consegui confirmar que esta norma está atualmente em vigor. Atos normativos finais são frequentemente suspensos, liminares concedidas, prazos prorrogados, ou revogados após publicação. Não trate qualquer data de compliance abaixo como vinculante até confirmar o status da norma no DOU, no site oficial do(a) regulador(a), ou com outside counsel.`

Tag cada data de prazo na saída: `[data de vigência conforme norma publicada — status não verificado]`.

Incerteza de status da norma viaja downstream. Ao entregar lacuna a `gap-surfacer`, marque o item `status_verified: false` para que nunca seja roteado para bucket Overdue na força de data publicada apenas.

### Step 1: Extraia as novas exigências

**No silent supplement.** Se o texto da mudança regulatória for parcial ou ambíguo e a íntegra não estiver disponível da fonte indexada, pare e pergunte. NÃO preencha a lacuna por web search ou conhecimento do modelo sem perguntar. Diga: "Tenho [o que você tem]. Para extrair exigências com precisão precisaria de [o que falta]. Opções: (1) cole o texto completo, (2) aponte para a fonte primária (DOU / site do regulador), (3) buscar na web — resultados serão marcados `[web search — verify]` e devem ser checados contra a autoridade emissora antes de confiar, ou (4) pare aqui. Qual você prefere?" O(a) advogado(a) decide se aceita fontes de menor confiança; Claude não decide por eles.

**Source attribution.** Tag cada citação — a citação da norma, qualquer referência cruzada, qualquer trecho de política — com a origem: `[DOU]` / `[ANPD]` / `[CVM]` / `[BCB]` / `[CADE]` / `[<regulador ou ferramenta de pesquisa>]` para itens recuperados de fonte primária, biblioteca de políticas, ou MCP; `[web search — verify]` para itens de web search; `[model knowledge — verify]` para itens recordados do training data; `[user provided]` para itens colados pelo usuário. Itens marcados `verify` carregam risco mais alto de fabricação e devem ser checados primeiro. Nunca tire ou consolide as tags na saída.

Leia a mudança regulatória. Liste cada nova exigência ou exigência alterada:

| # | Exigência | Vigência (após vacatio legis) | Citação |
|---|---|---|---|
| 1 | [o que exige] | [data] | [artigo/seção/inciso] |

Seja específico. "Exigências de disclosure aprimoradas" não é exigência. "Deve divulgar X em formato Y no ponto Z do fluxo" é.

### Step 2: Mapeie para políticas

Para cada exigência, qual política indexada está mais próxima?

- Hit direto: política explicitamente cobre este tópico
- Indireto: política cobre tópico relacionado, isto é nova sub-questão
- Sem hit: nenhuma política endereça isto — lacuna é "política não existe"

### Step 3: Diff

Para cada hit direto ou indireto, leia a política e compare:

```markdown
### Exigência [N]: [nome]

**Nova norma exige:** [exigência]

**Nossa política ([nome], última atualização [data]) diz:**
> "[trecho relevante]"

**Lacuna:** [Nenhuma — política já cobre | Parcial — política endereça X mas não Y | Total — política contradiz ou não endereça]

**Mudança necessária:** [específica — "acrescentar parágrafo sobre X" não "atualizar a política"]

**Policy owner:** [do índice]
```

### Step 4: Lacunas sem hit

Exigências sem hit de política são destacadas separadamente:

```markdown
### Nova política necessária

Exigência [N]: [exigência]

Nenhuma política existente cobre isto. Opções:
- Elaborar nova política (owner sugerido: [quem cuida do tópico mais próximo])
- Acrescentar à [política relacionada] como nova seção
- Determinar que isto não exige política (compliance pontual, não recorrente)
```

## Branches por tipo de input regulatório

### Pre-rule branch (Tomada de Subsídios / AIR em consulta)

Se o input regulatório for uma tomada de subsídios, AIR (Dec. 10.411/2020) ainda em consulta, ou minuta de norma sem exigências impostas, NÃO rode diff completo de fechamento de lacunas. Em vez disso, produza **análise de pré-posicionamento**:

- Nomeie as políticas que provavelmente precisarão mudar uma vez que a norma final seja publicada (não hoje).
- Sinalize se alguma das áreas de questão da AIR/tomada de subsídios intersecta a prática da empresa de forma que justifique manifestação.
- Note o prazo da consulta pública e o owner da decisão de manifestação em `~/.claude/plugins/config/claude-for-legal/regulatorio/CLAUDE.md`.
- NÃO produza linhas "sem lacuna" por exigência para uma tomada de subsídios — não há exigências a fazer diff contra. Produza um parágrafo nomeando a exposição futura e as políticas que tocaria.

### Negative-finding branch (norma final / consulta pública contra política que não é o alvo certo)

Se cada exigência na lista extraída sair como "sem lacuna contra [a política nomeada]", NÃO produza análise completa por exigência — comprima a um único parágrafo curto:

```markdown
## Policy Diff: [Nome da norma] — [Nome da política]

[NORMA] aparentemente não exige mudança em [NOME DA POLÍTICA]. [NOME DA POLÍTICA]
§[X] já cobre [Y]. As políticas que esta norma de fato toca são
[outra-política-1] e [outra-política-2] — refaça `/regulatorio:policy-diff` contra elas.

Revisar em [próximo ciclo — ex.: "na próxima revisão anual de políticas"] ou se
[trigger — ex.: "a norma for alterada ou tiver vacatio legis prorrogado"].
```

Um parágrafo, uma recomendação, nota de roteamento. Não repita o achado "sem lacuna" por exigência — a tabela resumo cuida disso. Um negative finding contra a política-alvo errada é problema de roteamento, não análise de compliance.

### Gap branch (norma final / consulta pública com ao menos uma lacuna contra a política-alvo)

Análise completa por exigência conforme especificado abaixo. O formato de diff detalhado é para diffs que de fato encontram lacunas.

## Output

```markdown
[WORK-PRODUCT HEADER — por config do plugin ## Outputs — varia por role; veja `## Who's using this`]

## Policy Diff: [Nome da norma]

**Norma:** [nome, link DOU/site do(a) regulador(a)]
**Vigência:** [data após vacatio legis — LINDB art. 1º]
**Exigências extraídas:** [N]

### Bottom line

[N lacunas exigem ação até [data] — top 3: X, Y, Z]

### Sumário

| # | Exigência | Política afetada | Lacuna | Owner |
|---|---|---|---|---|
| 1 | [curta] | [nome da política ou "nenhuma"] | Nenhuma/Parcial/Total | [nome] |

### Diffs detalhados

[Cada bloco de exigência do Step 3]

### Novas políticas necessárias

[Do Step 4, se houver]

### Exigências sem lacuna

[Lista — útil saber o que já está coberto]

---

**Verifique citações antes de confiar nelas.** As citações regulatórias e referências de política acima foram geradas por IA e não foram checadas contra fonte primária. Antes de agir sobre qualquer exigência aqui, confirme a norma contra o DOU (in.gov.br), planalto.gov.br, site oficial da agência (ANPD, CVM, BCB, ANVISA, ANATEL, ANS, ANEEL, ANP, ANTT, ANAC, IBAMA, CADE, SENACON), ou plataforma de pesquisa do escritório — cheque precisão, data de vigência, e status atual. Citações regulatórias geradas por IA podem ser fabricadas, mal citadas, ou desatualizadas. Source tags em cada exigência (ex.: `[DOU]`, `[ANPD]`, `[web search — verify]`) mostram a origem; tags `verify` carregam risco mais alto de fabricação e devem ser checadas primeiro.
```

## Config-dependent fallbacks

Esta skill lê o índice da biblioteca de políticas de `~/.claude/plugins/config/claude-for-legal/regulatorio/CLAUDE.md`. Quando o índice estiver vazio ou ainda `[PLACEHOLDER]`:

- **Biblioteca de políticas vazia:** sinalize cada exigência como "sem hit de política" por padrão e acrescente: "A biblioteca de políticas na sua configuração está vazia, então cada exigência é sinalizada como lacuna de nova política. Se você tem políticas que endereçam estas exigências, acrescente-as à biblioteca com `/regulatorio:cold-start-interview --redo` ou editando `~/.claude/plugins/config/claude-for-legal/regulatorio/CLAUDE.md`, e refaça o diff."
- **Owner faltando para política mapeada:** deixe a célula Owner em branco no sumário e acrescente: "Owners de política não estão setados para [lista]. Atribua-os com `/regulatorio:cold-start-interview --redo` ou editando a biblioteca de políticas em `~/.claude/plugins/config/claude-for-legal/regulatorio/CLAUDE.md` para que gap-surfacer possa rotear."

Nada a dizer sobre config quando a biblioteca estiver populada e owners setados.

## Handoff

Para gap-surfacer: toda lacuna Parcial ou Total vira item rastreado com owner e prazo.

## Close with the next-steps decision tree

Encerre com o decision tree de próximos passos por CLAUDE.md `## Outputs`. Customize as opções ao que esta skill acabou de produzir.

## What this skill does not do

- Redige as atualizações da política. Identifica o que precisa atualização; policy-drafting (ou humano) redige.
- Interpreta texto regulatório ambíguo definitivamente. Se a norma pode ser lida de duas maneiras, diga e sinalize para advogado(a). LINDB art. 30 (consulta de regulador) pode ser caminho quando ambiguidade é grave.
