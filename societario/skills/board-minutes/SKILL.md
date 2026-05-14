---
name: board-minutes
description: >
  Minuta atas de reunião do conselho de administração, da diretoria ou de comitês
  no formato da casa. Detecta automaticamente reuniões de conselho/comitê na
  agenda, pede a ordem do dia, slides e materiais prévios, e produz minuta
  completa no formato extraído das atas seed. Também trata deliberação por
  escrito em lugar de reunião. Gatilho: "ata de conselho", "minutar ata",
  "próxima reunião do conselho", "ata de comitê", "deliberação por escrito",
  ou detecção em agenda de reunião de conselho/comitê.
---

# Atas de Conselho

> **Adaptação BR não oficial.** Calibrado para Lei 6.404/1976 (S.A.), Código Civil (Ltda.), exigências da Junta Comercial e RCVM 80 (S.A. aberta). Veja `references/terminology-us-to-br.md`. Toda saída é rascunho para revisão por advogado(a) inscrito(a) na OAB — não substitui parecer jurídico.

## Contexto de matéria

**Contexto de matéria.** Verifique `## Workspaces de matéria` no CLAUDE.md nível-prática. Se `Habilitado` for `✗` (padrão para in-house), pule o resto deste parágrafo — skills usam contexto nível-prática e o maquinário de matéria é invisível. Se habilitado e não houver matéria ativa, pergunte: "Qual matéria? Rode `/societario:matter-workspace switch <slug>` ou diga `nível-prática`." Carregue `matter.md` da matéria ativa para contexto e overrides específicos. Grave saídas na pasta da matéria em `~/.claude/plugins/config/claude-for-legal/societario/matters/<matter-slug>/`. Nunca leia arquivos de outra matéria a menos que `Contexto cross-matter` esteja `on`.

---

## Propósito

Ata de conselho/diretoria é registro societário. Precisa ser precisa, completa e em formato que aguente escrutínio — diligência em rodada de investimento, fiscalização da CVM, sala de dados de M&A, ou questionamento em assembleia. Esta skill minuta no formato da casa, para que seu tempo seja gasto revisando e corrigindo, não formatando.

**Cuidado especial S.A.:** atas de reunião do conselho de administração e de AGO/AGE devem ser arquivadas na Junta Comercial em até 30 dias (art. 289 Lei 6.404/76). Atas que aprovam atos com publicação obrigatória (DOU + jornal de grande circulação, ou apenas em meio eletrônico para S.A. fechada com receita até R$ 78 mi — art. 294 Lei 6.404) também devem ser publicadas.

## Carregue contexto

- `~/.claude/plugins/config/claude-for-legal/societario/CLAUDE.md` → seção `## Conselho & Secretaria`:
  - Formato das atas (narrativa longa / ata de deliberações / híbrido)
  - Template extraído de atas seed (estrutura, linguagem das deliberações, cabeçalho)
  - Composição do conselho e comitês
  - Deliberações por escrito — para que são usadas e limites
- Se `CLAUDE.md` não tiver formato de atas: rode cold-start primeiro. Não prossiga com formato genérico.

---

## Passo 1: Identifique a reunião

### Detecção em agenda

Se o conector de calendário estiver autorizado, busque eventos com termos típicos:

**Termos de busca:** "Conselho de Administração", "Reunião do Conselho", "RCA", "Comitê de Auditoria", "Comitê de Remuneração", "Comitê de Indicação e Governança", "Comitê de Riscos", "AGO", "AGE", "Assembleia"

**Janela:** 30 dias adiante. Se não encontrar nada, 14 dias para trás (atas frequentemente são minutadas depois do fato).

Apresente o que encontrar:

> Encontrei as seguintes reuniões na sua agenda:
>
> 1. **[Nome da reunião]** — [Data], [Hora], [Local/Virtual]
> 2. **[Nome da reunião]** — [Data], [Hora], [Local/Virtual]
>
> A ata é para qual? Ou é outra reunião não listada?

Se o conector não estiver autorizado ou não retornar nada: pergunte direto — qual reunião, qual data, qual tipo (Conselho de Administração pleno / qual comitê / Diretoria / Assembleia).

### Metadados da reunião a confirmar

- **Tipo de reunião:** Conselho de Administração / Diretoria / [Comitê] / AGO / AGE
- **Data e hora**
- **Local ou plataforma** (presencial / Teams / Zoom / híbrida — Lei 6.404 art. 124 §2º-A admite participação à distância em assembleia desde que prevista no estatuto; reuniões de conselho admitidas em qualquer caso)
- **Convocação:** A convocação foi regular? (Para AGO: anúncio na imprensa com 21 dias de antecedência — art. 124 Lei 6.404; S.A. fechada admite carta com aviso de recebimento. Para RCA: prazo do estatuto. Conselho fiscal: idem.)

---

## Passo 2: Presenças

Pergunte a lista de presentes, ou ofereça puxar da convocação se houver conector.

**Conselheiros presentes:**
- Puxe a composição do conselho de `CLAUDE.md` como ponto de partida
- Pergunte quem efetivamente compareceu, quem faltou e se houve justificativa

**Diretoria presente:**
- Quem da diretoria participou? (Diretor(a)-presidente, CFO, DRI, outros)
- Diretores costumam ser listados em bloco separado de conselheiros

**Convidados:**
- Auditor independente presente?
- Advogado(a) externo(a)? (Nome e escritório)
- Banco de investimento, consultores, peritos?
- Convidados para itens específicos (note presença limitada àquele item)

**Mesa:**
- Quem presidiu?
- Quem secretariou?

**Quórum:**

- Verifique o estatuto/contrato social para quórum de instalação e de deliberação. S.A. — art. 138 Lei 6.404/76 (CA) e arts. 125-129 (AGO/AGE). Ltda. — arts. 1.071-1.080 CC. Se o estatuto for omisso, pesquise a regra supletiva e cite. Registre o que confirmou (fonte e dispositivo) nas notas de redação.
- Confirme que o quórum foi atingido. Se não: pare e sinalize antes de minutar. Não produza ata implicando reunião válida. Encaminhe para advogado(a) — caminho de remediação (ratificação, nova reunião, deliberação por escrito quando admitida) depende do tipo societário e da natureza do ato.

---

## Passo 3: Materiais

Peça os materiais. São fonte para a ordem do dia e deliberações.

> Pode compartilhar a ordem do dia e materiais prévios da reunião? Mesmo um esboço de pauta basta para estruturar a ata. Se houve apresentação do management ou slides, envie também — uso para preencher os sumários de cada item.
>
> Se nada foi distribuído antes, me diga os itens da pauta verbalmente e crio placeholders para cada um.

**Da pauta e slides, extraia:**
- Itens em ordem
- Quaisquer deliberações propostas (busque linguagem: "aprovar", "autorizar", "ratificar", "eleger", "fixar", "deliberar")
- Anexos referenciados (apresentações, demonstrações financeiras, pareceres, laudos de avaliação)
- Votações esperadas

**Sem materiais:** Peça os itens verbalmente e siga com placeholders para conteúdo de discussão.

---

## Passo 4: Minute a ata

Use o formato da casa em `CLAUDE.md`. Não caia em formato genérico. As atas seed são o template — replique estrutura, cabeçalho, linguagem das deliberações, nível de detalhe da discussão.

### Estrutura padrão (adapte ao formato da casa)

**Cabeçalho:**
```
ATA DA [N]ª REUNIÃO DO CONSELHO DE ADMINISTRAÇÃO
[OU: ATA DA [N]ª REUNIÃO DO [NOME DO COMITÊ]]
[OU: ATA DA ASSEMBLEIA GERAL [ORDINÁRIA/EXTRAORDINÁRIA]]
DE [DENOMINAÇÃO DA COMPANHIA]
CNPJ: [CNPJ] · NIRE: [NIRE]

[Data, hora e local]
```

**Abertura:**
- Data, hora, local (físico ou plataforma)
- Convocação: [edital publicado em DOU + jornal nos dias X, Y, Z / carta com AR / dispensada por presença unânime (art. 124 §4º Lei 6.404)]
- Presença: [N de M conselheiros — quórum confirmado]
- Mesa: Presidente [nome], Secretário(a) [nome]

**Ordem do dia:**
Liste tópicos.

**Deliberações — uma seção por item:**

```
[TÍTULO DO ITEM]

O(a) Sr(a). [presidente/relator(a)] [apresentou / relatou / submeteu à discussão] [tema].

[Sumário da discussão — veja notas abaixo]

[Se deliberação:]
Submetido o tema à deliberação, o(a) [Conselho / Comitê / Assembleia] [aprovou por unanimidade / por N votos favoráveis e M contrários, com P abstenções] a seguinte deliberação:

DELIBERA-SE [ou RESOLVE-SE / FICA APROVADO — use a linguagem da casa] [texto da deliberação].
```

**Encerramento:**
"Nada mais havendo a tratar, foi encerrada a reunião, da qual se lavrou a presente ata que, lida e achada conforme, vai assinada por todos os presentes."

**Assinaturas:**
Estatuto define se todos os presentes assinam ou se a ata em livro próprio é assinada apenas pela mesa com lista de presença em anexo. Para S.A.: livro de atas de reuniões do CA, livro de atas das assembleias gerais, livro de presenças (art. 100 Lei 6.404).

**Assinatura eletrônica:** admitida — MP 2.200-2/2001 (ICP-Brasil) ou Lei 14.063/2020 (eletrônica simples/avançada). DREI/IN 81/2020 admite assinatura eletrônica para arquivamento em Junta Comercial. Confirme a exigência da Junta competente (JUCESP, JUCERJA etc.).

---

### Notas de redação

**Sumários de discussão:** Decida quanto da discussão capturar — siga o formato da casa:

- *Narrativa longa:* Sumarize a substância — perguntas, informações apresentadas, fatores considerados. Não cite indivíduos a menos que a atribuição específica importe legalmente (dissidência registrada para fins do art. 159 §6º Lei 6.404, voto contrário para preservação de responsabilidade).
- *Ata de deliberações:* Anote apenas o que foi apresentado e a deliberação. Sem conteúdo de discussão além de "o conselho deliberou sobre a matéria."
- *Híbrido:* Narrativa completa para itens-chave (M&A, contas, fato relevante, distribuição de dividendos), apenas-ação para rotineiros.

Quando houver material: extraia conteúdo dos slides e apresentações. O conselho "recebeu e analisou" a apresentação — sumarize o que cobriu.

Sem materiais: insira `[PLACEHOLDER — sumarizar discussão aqui]` e sinalize claramente. Não invente conteúdo.

**Deliberações:** Use a linguagem exata das atas seed — "DELIBERA-SE", "RESOLVE-SE", "FICA APROVADO". É estilo da casa, não intercambiável.

**Anexos:** Numere em ordem (Anexo I, II, III). Comuns: apresentação ao conselho, demonstrações financeiras, parecer de auditoria, laudo de avaliação, parecer jurídico, lista de presença, declaração de desimpedimento de administradores eleitos (Lei 6.404 art. 147 §3º).

**Conflito de interesse:** se algum conselheiro/administrador tem interesse particular ou conflitante (art. 156 Lei 6.404 para administrador; art. 115 §1º para acionista), a ata deve registrar a manifestação e o impedimento de voto. Sinalize.

---

## Passo 4.5: Gate de ação consequente (aprovar ata)

**Antes de produzir versão final pronta para aprovação:** Leia `## Quem está usando` em `CLAUDE.md`. Se o Papel for **Não-advogado(a)**:

> Aprovar atas as torna registro oficial do que o conselho/diretoria/assembleia decidiu — são prova primária de autorização para os atos praticados. Você revisou com advogado(a)? Se sim, prossiga. Se não, eis um briefing:
>
> - O que foi decidido (deliberações, votos, presenças)
> - O que a minuta captura e o que é placeholder
> - Questões em aberto (sinalizações de presença, quórum, conflito, conformidade com estatuto/CC/Lei 6.404)
> - O que pode dar errado (deliberações mal redigidas, divulgações faltando, vícios de quórum, conflito não tratado, vazamento de matéria privilegiada em sumários de discussão, descumprimento de prazo de arquivamento na Junta)
> - O que perguntar ao(à) advogado(a) (a profundidade da discussão está adequada para este conselho; sessões executivas estão segregadas; algum item precisa de mais documentação; ata precisa ser publicada/arquivada)
>
> Para localizar advogado(a): consulte a OAB de sua seccional, que oferece busca pública de inscritos e referência de advogado(a).

Não produza versão final pronta para aprovação após este gate sem um "sim" explícito. Versão marcada como MINUTA para revisão de advogado(a) é ok.

---

## Passo 5: Output e prompts de revisão

Produza a minuta completa. A ata em si é registro societário, não privilegiada; não aplique cabeçalho de material de trabalho à ata circulada. As notas de redação, placeholders e checklist abaixo são material de trabalho — anteponha o cabeçalho conforme `CLAUDE.md` `## Outputs` (varia por papel):

```
[CABEÇALHO DE MATERIAL DE TRABALHO — conforme config ## Outputs — varia por papel; veja `## Quem está usando`]
```

Depois da minuta, adicione checklist de revisão:

```
[CABEÇALHO DE MATERIAL DE TRABALHO — conforme config ## Outputs]

CHECKLIST DE REVISÃO — verifique antes de circular:

□ Presença confirmada (compare com lista efetiva)
□ Quórum confirmado correto
□ Texto das deliberações confere com o que foi efetivamente aprovado
□ Votos registrados — abstenções ou votos contrários a anotar?
□ Anexos numerados e referenciados
□ Sessão executiva realizada? (Acrescente nota separada)
□ Conflitos de interesse declarados? (Impedimento de voto conforme art. 156 Lei 6.404 / art. 115)
□ Hora de encerramento preenchida
□ Para S.A.: prazo de 30 dias para arquivamento na Junta Comercial (art. 289 Lei 6.404) e publicação se aplicável (art. 289 e 294)
□ Para companhia aberta: avaliar dever de divulgação via IPE/CVM (Res. CVM 44 — fato relevante; Res. CVM 80 — periódicos)
□ Advogado(a) externo(a) revisou? (Se exigido pelo processo)
```

Sinalize seções com placeholder.

Adicione como nota final pré-aprovação, removida antes da aprovação:

> Esta é minuta para revisão de advogado(a), não ata aprovada. Atas aprovadas são registro oficial e carregam consequências jurídicas — advogado(a) inscrito(a) na OAB revisa, edita e assume responsabilidade profissional antes da aprovação. Não aprovar minuta não revisada.

---

## Deliberações por escrito

Para deliberações por escrito em lugar de reunião, use `/societario:written-consent`. Aquela skill trata a busca de precedente, confirmação do regime legal (CC art. 1.072 §3º para Ltda.; Lei 6.404 art. 121 par. único para S.A. fechada — hipóteses limitadas) e o aviso de escopo para atos relevantes one-off.

---

## O que esta skill não faz

- Não comparece à reunião nem captura discussão em tempo real — minuta a partir de materiais e input do(a) advogado(a).
- Não decide se a deliberação é juridicamente válida ou suficiente — minuta no formato da casa; juízo de adequação é do(a) advogado(a).
- Não finaliza atas — a minuta exige revisão antes da circulação.
- Não distribui atas — output é para o(a) advogado(a) revisar, editar e circular pelo próprio processo.
- Não arquiva na Junta Comercial nem publica — execução é manual ou via despachante.
