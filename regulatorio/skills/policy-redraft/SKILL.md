---
name: policy-redraft
description: Produz redraft marcado de política como proposta para fechar lacuna achada por /regulatorio:gaps ou /regulatorio:policy-diff. Primeira minuta para revisão interna — não para aplicação direta a documentos de política aprovada. Use quando o usuário disser "redraft da política", "minute o fix de política", "marque a política", ou quando o gap-surfacer entregar lacuna para redação.
argument-hint: "[GAP-ID ou descrição da lacuna]"
---

> **Adaptação BR não oficial.** Calibrado para Lei 9.784/1999, LINDB arts. 20-30 (Lei 13.655/2018), vacatio legis LINDB art. 1º, Dec. 10.411/2020 (AIR). Reguladores BR: ANPD, CVM, BCB, ANVISA, ANATEL, ANS, ANEEL, ANP, ANTT, ANAC, IBAMA, CADE, SENACON.

# /policy-redraft

1. Carregue `~/.claude/plugins/config/claude-for-legal/regulatorio/CLAUDE.md` → índice da biblioteca de políticas + perfil de prática.
2. Use o workflow abaixo.
3. Reúna inputs: a lacuna (saída de `/regulatorio:gaps` ou descrita), o texto atual aprovado da política, o texto da norma.
4. Verifique se a norma está atual (per o check de status de policy-diff). Se não puder verificar, emita o banner `⚠️ STATUS DA NORMA NÃO VERIFICADO`.
5. Produza redraft marcado da(s) seção(ões) afetada(s) — menor edição possível, tags `[verify]` propagadas, comentários inline explicando POR QUÊ cada mudança.
6. Saída: Memorando de Redraft de Política. Escreva em arquivo novo `[nome-política]-proposta-redraft-[YYYY-MM-DD].md` — nunca escreva no documento-fonte da política.
7. NÃO feche a lacuna no tracker. A lacuna fecha quando o redraft for APLICADO E APROVADO, que é ação do owner da política.

---

> Esta skill produz **proposta**, não edição. Escreve em arquivo novo com nome claramente marcado como draft. Nunca sobrescreve documento-fonte de política, e nunca fecha lacuna no tracker — a lacuna fecha quando o redraft for aplicado E aprovado pelo owner.

## Matter context

**Matter context.** Verifique `## Matter workspaces` no CLAUDE.md practice-level. Se `Enabled` for `✗`, pule. Se habilitado e sem matter ativa, pergunte: "Para qual matter é isso? Rode `/regulatorio:matter-workspace switch <slug>` ou diga `practice-level`." Carregue o `matter.md` da matter ativa. Escreva outputs em `~/.claude/plugins/config/claude-for-legal/regulatorio/matters/<matter-slug>/`. Nunca leia outra matter a menos que `Cross-matter context` esteja `on`.

---

## Purpose

Gap-surfacer encontra a lacuna. Policy-diff nomeia o que precisa mudar. Esta skill dá o passo seguinte e produz redraft marcado da seção afetada da política — pequeno, específico, sinalizado — como primeira minuta para revisão do owner.

## Hard guardrails — leia primeiro

São as regras load-bearing. Se alguma seria violada, pare e pergunte.

1. **É PROPOSTA, não edição.** Nunca escreva diretamente no documento-fonte da política. A saída vai para arquivo novo em `[nome-política]-proposta-redraft-[YYYY-MM-DD].md`, ou no matter workspace. Não em `[nome-política].md`.
2. **Nunca feche a lacuna no tracker.** Lacunas fecham quando o redraft é APLICADO E APROVADO — ação do owner, não sua. Se o usuário disser "feche a lacuna agora que você redraftou", recuse: "Eu produzo a proposta. A lacuna fecha quando você tiver revisado, aplicado e aprovado a mudança. Quando feito, me diga e atualizo o tracker."
3. **"Aplique isto para mim" está fora de escopo.** Se o usuário pedir para aplicar o redraft no documento-fonte: "Não aplico mudanças de política — é ação do owner após revisão e aprovação. Produzo a proposta. Quando revisada e aprovada, me diga e atualizo o tracker."
4. **Confirme a versão da política antes de redraftar.** Se o usuário der arquivo, pergunte: "Esta é a versão aprovada da política, e é a mais recente? Um redraft contra política desatualizada cria divergência." Se colar texto, confie mas sinalize na reviewer note.
5. **Menor edição possível.** Risque palavra antes de frase, frase antes de parágrafo, parágrafo antes de seção. Toque apenas seções afetadas pela lacuna. Não restilize a política.
6. **Propague tags `[verify]`.** Qualquer data de vigência, threshold, citação ou exigência vinda de conhecimento do modelo ou fonte não verificada ganha tag no redraft mesmo, não só no memo.

## Step 1: Reúna inputs

Três inputs são obrigatórios. Se algum faltar, pergunte — não infira.

### 1a. A lacuna

Uma de:
- Um `GAP-ID` do gap tracker — carregue de `~/.claude/plugins/config/claude-for-legal/regulatorio/gap-tracker.yaml` (ou equivalente matter-level).
- Uma lacuna descrita na mensagem do usuário — capture a exigência, a norma, e a política afetada.
- Sumário de diff colado da saída de `/regulatorio:policy-diff`.

### 1b. O texto atual da política

Um de:
- Caminho de arquivo — leia, depois pergunte: "Esta é a versão aprovada da política, e a mais recente? Um redraft contra política desatualizada cria divergência." Note a resposta na reviewer note.
- Texto colado — confie mas sinalize na reviewer note: "Texto da política foi colado diretamente; assumi versão atual aprovada. Confirme antes de aplicar."
- Nenhum — peça. Não chute o texto a partir do gap tracker ou web search.

### 1c. O texto da norma

Um de:
- Saída do diff (já tem norma extraída e marcada).
- Norma recuperada — note a fonte com tag de provenance.
- Texto colado pelo usuário — tag `[user provided]`.

Se o texto da norma for parcial ou ambíguo, aplique a regra **no silent supplement** do CLAUDE.md: ofereça as opções (colar texto completo, apontar fonte primária, web-search-com-tag-verify, ou parar), e espere.

## Step 2: Verifique se a norma está atual

Use o mesmo padrão de rule-status check de `policy-diff`. Red flags:

- A data de aplicabilidade/vigência passou há mais de 30 dias sem confirmação de não-prorrogação (vacatio legis pode ter sido estendido — LINDB art. 1º).
- A norma tem mais de 12 meses.
- A norma é final em rulemaking politicamente contencioso (ADIs no STF, mandados de segurança coletivos, decisões do TCU).

Quando vir red flag, cheque (via research MCP, web search se habilitado, ou DOU em in.gov.br) para: prorrogações, suspensões, liminares, ADIs, decisões TCU, revogações, ou alterações. Se puder verificar que está em vigor, prossiga. Se não:

> `⚠️ STATUS DA NORMA NÃO VERIFICADO — Não consegui confirmar que esta norma está atualmente em vigor. Atos normativos finais são frequentemente suspensos, liminares concedidas, prazos prorrogados, ou revogados após publicação. Não aplique este redraft até confirmar o status da norma no DOU ou com outside counsel.`

Emita o banner acima do work-product header. Tag cada data de vigência/compliance no redraft como `[data de vigência conforme norma publicada — status não verificado]`.

## Step 3: Produza o redraft

Uma versão marcada da seção afetada.

### Redline granularity — menor edição possível

- Risque palavra antes de frase.
- Risque frase antes de parágrafo.
- Risque parágrafo antes de seção.
- Toque apenas seções afetadas pela lacuna. Não restilize.

### Convenções

- Texto riscado: `~~texto riscado~~`
- Texto inserido: **texto inserido**
- Cada mudança traz comentário inline explicando POR QUÊ — a norma, a citação, a lacuna fechada:

  > `[Mudança: acrescentado "identificadores biométricos" à definição de dados pessoais sensíveis conforme LGPD art. 5º, II e Resolução CD/ANPD nº [N]/[ano] [verify]]`

- Qualquer data de vigência, threshold, citação ou exigência vinda de conhecimento do modelo ou fonte não verificada ganha tag `[verify]` inline — não só no resumo de mudanças.
- Propague source tags do diff: `[DOU]`, `[ANPD]`, `[CVM]`, `[BCB]`, `[CADE]`, `[web search — verify]`, `[model knowledge — verify]`, `[user provided]`. Não tire ao mover do diff para o redraft.

### Disciplina de escopo

Se seção da política não for afetada pela lacuna, deixe quieta. Redraft que toca seções fora da lacuna parece IA opinando sobre o que não foi pedido, e dificulta a revisão.

Se vir segunda lacuna ao redraftar — provisão claramente fora de passo com a norma mas que não estava na lacuna original — não conserte em silêncio. Sinalize na reviewer note: "Ao redraftar para [GAP-ID], notei que [outra provisão] aparenta ter questão relacionada com [exigência]. Não incluída neste redraft. Considere lacuna de follow-on."

## Step 4: Saída — Memorando de Redraft de Política

```markdown
[WORK-PRODUCT HEADER — por config do plugin ## Outputs — varia por role; veja `## Who's using this`]

> **⚠️ Reviewer note**
> - **Sources:** [Research connector: DOU / STF / STJ ✓ verified | not connected — cites from training knowledge, verify before relying]
> - **Read:** [seções da política revisadas; o que não foi lido]
> - **Flagged for your judgment:** [N itens marcados `[review]` inline | nenhum]
> - **Currency:** [status da norma verificado contra [fonte], [data] | não verificado — veja banner acima]
> - **Before relying:** confirme que esta é a versão atual aprovada da política; verifique status da norma e data de vigência; obtenha revisão do owner; siga o processo de aprovação de políticas; atualize o gap tracker apenas quando aplicado e aprovado.

## Policy Redraft: [Nome da política]

**Lacuna:** [GAP-ID ou descrição curta]
**Norma:** [nome, citação, vigência — após vacatio legis]
**Política:** [nome, última atualização]
**Status:** PROPOSTA — ainda não revisada nem aprovada

### Bottom line

[Uma frase: qual é a lacuna. Uma frase: o que o redraft faz. Uma frase: o que precisa revisão.]

### Seção(ões) marcadas da política

[Texto redlined, com comentários inline `[Mudança: ...]`. Apenas seções afetadas.]

### Resumo de mudanças

| # | Provisão | Atual | Proposto | Por quê | Verify |
|---|---|---|---|---|---|
| 1 | §2.1 Definição de dado pessoal | "…nome, endereço, CPF…" | "…nome, endereço, CPF, identificadores biométricos…" | LGPD art. 5º, II expande dados sensíveis para cobrir biométricos | [DOU] |
| 2 | §4.3 Prazo de retenção | "30 dias" | "14 dias" | Nova norma impõe cap de 14 dias | `[verify — model knowledge]` |

### Antes de aplicar — checklist

- [ ] Confirme que esta é a versão atual aprovada da política sendo redraftada.
- [ ] Verifique status da norma e data de vigência (DOU, site oficial da agência, ou outside counsel).
- [ ] Obtenha revisão do owner da política.
- [ ] Siga seu processo de aprovação de políticas.
- [ ] Atualize o gap tracker quando aplicado e aprovado — não antes.

---

**What next? Pick one and I'll help you build it out:**

1. **Aplicar e obter aprovação** — você revisa, circula ao owner, leva ao processo de aprovação. Quando aprovado, me diga e marco a lacuna como fechada.
2. **Mais informação sobre [X]** — se mudança específica precisa de mais grounding (citação verificada, threshold checado, questão de jurisdição resolvida), me diga qual e investigo.
3. **Escalar para [owner / GC]** — se o redraft levanta algo acima da competência do owner, redijo escalada curta com fatos, mudança proposta, e qual decisão é necessária.
4. **Watch and wait** — se status da norma é incerto ou owner indisponível, acrescento nota de revisita no gap tracker.
5. **Outra coisa** — me diga o que faria.
```

## Filename

O nome do arquivo deixa claro que é draft. Use:

`[nome-política]-proposta-redraft-[YYYY-MM-DD].md`

Não `[nome-política].md`. Não `[nome-política]-v2.md`. As palavras "proposta-redraft" e a data são load-bearing — previnem o draft de ser confundido com a versão atual.

Escreva no matter workspace se houver matter ativa; senão no cwd ou local nomeado pelo usuário. Não escreva na pasta-fonte da biblioteca de políticas.

## Config-dependent fallbacks

Esta skill lê o índice da biblioteca de políticas e owners de `~/.claude/plugins/config/claude-for-legal/regulatorio/CLAUDE.md`. Quando valor necessário estiver vazio ou `[PLACEHOLDER]`:

- **Owner de política faltando:** ainda produza o redraft. Note na reviewer note: "Nenhum owner está setado para [política] em `## Policy library`. Atribua um com `/regulatorio:cold-start-interview --redo` para que o caminho de aprovação seja roteável."
- **Biblioteca vazia e a lacuna não nomeia política específica:** pare e pergunte: "Preciso do texto atual da política para redraftar. Cole o texto da política afetada, ou aponte o arquivo."

Nada a dizer sobre config quando valores populados.

## Interações com outras skills

- **Inputs upstream** vêm de `policy-diff` (análise de lacuna por exigência) e `gap-surfacer` (o tracker). Propague suas source tags e flags `[verify]`.
- **Estado do gap tracker:** esta skill NÃO altera o tracker. Não marca a lacuna como fechada, in-progress, não toca `notified`. Se quiser trilha de papel de que existe redraft, o owner ou usuário pode atualizar a entrada com nota de resolução quando aplicado e aprovado — veja `/regulatorio:gaps --close`.
- **Severity floor:** se a lacuna upstream é 🔴 ou 🟠, o Bottom line do memo carrega essa severidade. Demoção silenciosa é contradição que advogado(a) revisor(a) não consegue ver. Veja CLAUDE.md `## Cross-skill severity floor`.

## Close with the next-steps decision tree

Incluído no template de saída acima.

## What this skill does not do

- Aplica o redraft na fonte. Isso é ação do owner.
- Fecha a lacuna no tracker. Lacunas fecham quando aplicado e aprovado.
- Reescreve a política toda. Menor edição possível para fechar a lacuna.
- Produz redrafts multi-política. Uma lacuna, uma política, um memo. Comando `:package` para fan-out multi-política é skill futura.
- Produz o workflow de "apply". Comando `:apply` com gate de aprovação é skill futura.
