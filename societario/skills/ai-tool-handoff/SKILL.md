---
name: ai-tool-handoff
description: >
  Detecta quando Luminance, Kira ou ferramenta similar de revisão em massa
  está em uso, repassa a extração de cláusulas em alto volume e faz QA da
  saída conforme o nível de confiança em `~/.claude/plugins/config/claude-for-legal/societario/CLAUDE.md`. Use quando o(a)
  usuário(a) disser "mandar para Luminance", "revisão em massa", "extração
  por IA" ou quando diligence-issue-extraction atingir uma categoria de
  alto volume.
---

# AI Tool Handoff

## Contexto de matéria

**Contexto de matéria.** Cheque `## Workspaces de matéria` no CLAUDE.md nível-prática. Se `Habilitado` é `✗` (padrão para usuários(as) in-house), pule o resto deste parágrafo — skills usam contexto nível-prática e o aparato de matéria fica invisível. Se habilitado e não houver matéria ativa, pergunte: "Para qual matéria é isto? Rode `/societario:matter-workspace switch <slug>` ou diga `nível-prática`." Carregue o `matter.md` da matéria ativa para contexto específico e overrides. Grave saídas na pasta da matéria em `~/.claude/plugins/config/claude-for-legal/societario/matters/<matter-slug>/`. Nunca leia arquivos de outra matéria a menos que `Contexto cross-matter` esteja `on`.

---

## Propósito

Luminance e Kira são bons em uma coisa: ler 500 contratos e achar todas as cláusulas de mudança de controle. São menos bons em juízo — decidir se uma cláusula de CoC em particular é efetivamente acionada por esta estrutura de operação.

Esta skill repassa a extração em massa para a ferramenta certa, depois roda a camada de QA sobre o que volta.

**Antes de repassar:** tente `tabular-review` primeiro (`/societario:tabular-review`). Para qualquer coisa que o ambiente do(a) usuário(a) aguente — algumas centenas de documentos, schema de colunas definido — a revisão tabular nativa é mais rápida de configurar, sem custo por documento, e mantém o material de trabalho local. Repasse para Luminance/Kira quando o corpus for realmente grande demais, o time já tiver licença e workflow, ou a matéria exigir ferramenta com cadeia de proveniência validada.

## Carregar contexto

`~/.claude/plugins/config/claude-for-legal/societario/CLAUDE.md` → Revisão assistida por IA:
- Ferramenta em uso (Luminance / Kira / nenhuma)
- Para que é usada (quais tipos de cláusula)
- Nível de confiança (usar as-is / spot-check / re-revisão completa)
- Processo de handoff (quem carrega, quem faz QA)

Se `~/.claude/plugins/config/claude-for-legal/societario/CLAUDE.md` diz que não há ferramenta IA → esta skill é no-op. Tudo passa por diligence-issue-extraction diretamente.

## Quando repassar

Repasse quando todas as condições forem atendidas:
- Categoria tem >50 documentos (abaixo disso, mais rápido só ler)
- Alvo de extração é tipo de cláusula em que a ferramenta é boa (CoC, cessão, exclusividade, MFN, terminação, renovação automática)
- Documentos são razoavelmente uniformes (todos contratos de cliente em papel similar — não mistura de contratos, cartas e atas de conselho)

Não repasse:
- Documentos sob medida ou pesadamente negociados
- Side letters e aditivos (dependentes de contexto, ferramentas perdem a interação com o contrato principal)
- Qualquer coisa em que a pergunta seja "o que isto significa para a operação" e não "essa cláusula existe"

## O handoff

### Passo 1: Preparar o lote

- Identifique documentos para o lote (do inventário do VDR)
- Especifique alvos de extração conforme `~/.claude/plugins/config/claude-for-legal/societario/CLAUDE.md` (quais tipos de cláusula)
- Anote o limiar de materialidade para que a saída da ferramenta possa ser filtrada

### Passo 2: Carregar (ou instruir quem carrega)

Conforme `~/.claude/plugins/config/claude-for-legal/societario/CLAUDE.md` — quem carrega. Se for você, gere as instruções de carga. Se for outra pessoa, gere a solicitação:

```markdown
## Solicitação de carga [Ferramenta] — [Código da operação] — [Categoria]

**Documentos:** [N] docs da pasta VDR [caminho]
**Carregar em:** [Workspace/matéria da ferramenta]
**Alvos de extração:**
- Mudança de controle / cessão
- Exclusividade
- [etc. conforme `~/.claude/plugins/config/claude-for-legal/societario/CLAUDE.md`]

**Filtrar saída:** Sinalizar apenas onde o alvo está presente — não é necessário "nenhuma cláusula de CoC encontrada" para cada doc.

**Devolver até:** [data]
```

### Passo 3: Fazer QA da saída

Quando a ferramenta devolver resultados, aplique o nível de confiança:

**"Usar as-is":** Ingere diretamente nos achados de diligência. (Só se `~/.claude/plugins/config/claude-for-legal/societario/CLAUDE.md` disser isso — é raro.)

**"Spot-check X%":** Amostre aleatoriamente X% dos documentos sinalizados. Para cada, leia a cláusula real e compare com a extração da ferramenta. Se a taxa de erro for baixa, aceite o lote. Se houver erros, amplie a amostra.

**"Re-revisão humana completa dos sinalizados":** Ferramenta estreita o universo (500 docs → 80 com cláusulas de CoC). Humano(a) lê os 80. Ferramenta poupou o tempo de ler os 420 limpos.

### Passo 4: Camada de juízo

A ferramenta achou as cláusulas. Agora aplique o juízo:

Para cada cláusula de CoC sinalizada: é efetivamente acionada por esta operação?
- Compra de ações vs. compra de ativos vs. incorporação/cisão (Lei 6.404 arts. 224-234; CC arts. 1.116-1.122) — gatilhos diferentes
- Como "mudança de controle" está definida no contrato — controle acionário majoritário? controle do conselho? outra métrica? (atenção: para S.A. aberta art. 254-A Lei 6.404 garante tag-along 80%)
- Há carve-out para este tipo de transação?

Esta é a parte que a ferramenta não consegue fazer. Saída vai para achados de diligência em formato da casa.

## Saída

> O sumário de QA abaixo deriva de documentos do VDR que são privilegiados, confidenciais ou ambos. Herda o status de sigilo e confidencialidade das fontes — distribuição fora do círculo de sigilo pode quebrar a proteção. Armazene com os arquivos privilegiados da matéria.

```markdown
## Sumário de Handoff de Ferramenta IA — [Categoria]

**Ferramenta:** [Luminance / Kira]
**Documentos processados:** [N]
**Alvos de extração:** [tipos de cláusula]

### QA

**Nível de confiança:** [conforme `~/.claude/plugins/config/claude-for-legal/societario/CLAUDE.md`]
**Tamanho da amostra:** [N] docs spot-checked
**Taxa de erro:** [X]% — [Aceito / Amostra ampliada / Re-revisão completa disparada]

### Resultados

| Tipo de cláusula | Docs sinalizados | Após camada de juízo | Material |
|---|---|---|---|
| Mudança de controle | [N] | [N efetivamente acionados pela estrutura da operação] | [N acima do limiar] |
| Cessão | [N] | [N] | [N] |

**→ [N] achados adicionados às questões de diligência**
**→ [N] consentimentos adicionados ao checklist de fechamento**
```

## Encerre com a árvore de decisão de próximos passos

Encerre com a árvore de decisão de próximos passos conforme CLAUDE.md `## Outputs`. Customize as opções para o que esta skill acabou de produzir — os cinco ramos padrão (redigir o X, escalar, buscar mais fatos, observar e aguardar, outra coisa) são ponto de partida, não amarração. A árvore é a saída; o(a) advogado(a) escolhe.

## O que esta skill não faz

- Não roda Luminance ou Kira — gerencia o handoff e o QA. Um(a) humano(a) (ou a própria interface da ferramenta) roda a extração.
- Não substitui a saída da ferramenta pelo próprio juízo por inteiro — se `~/.claude/plugins/config/claude-for-legal/societario/CLAUDE.md` diz spot-check 10%, cheque 10%, não 100%.
- Não decide o nível de confiança — está em `~/.claude/plugins/config/claude-for-legal/societario/CLAUDE.md`, definido no cold-start com base na experiência do time com a ferramenta.
