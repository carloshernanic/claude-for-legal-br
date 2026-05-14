---
name: tabular-review
description: >
  Revisão tabular — uma linha por documento, uma coluna por dado, cada célula
  citada à fonte. Construída para diligência de M&A ("revisar estes 200 contratos
  do target para mudança de controle, cessão e cláusulas de onerosidade
  excessiva") mas serve para qualquer revisão em lote que precise sair em
  planilha. Use quando o(a) usuário(a) disser "revisão tabular", "grid de
  revisão", "monte um grid", "extraia estes campos destes contratos", "revise
  estes documentos para X, Y, Z", "me dê uma planilha de", "revisão em lote",
  ou apontar para uma pasta de documentos e pedir comparação.
---

# /tabular-review

> **Adaptação BR não oficial.** Calibrado para Lei 6.404/1976, Código Civil, Lei de Arbitragem 9.307/1996, CADE (Lei 12.529/2011), CVM, LGPD, INPI. Veja `references/terminology-us-to-br.md` na raiz do plugin.

1. Carregue `~/.claude/plugins/config/claude-for-legal/societario/CLAUDE.md` → estrutura de diligência, limiares de materialidade, formato da casa.
2. Confirme: quais documentos, quais colunas, onde vai a saída.
3. Monte o schema tipado. Escreva `.review-schema.yaml`. Confirme com o(a) usuário(a).
4. Rodada-amostra (3–5 docs). Ajuste schema. Confirme.
5. Fan-out — um sub-agente por documento, em paralelo. Cada célula: valor + estado + citação verbatim + localização.
6. Passada de normalização. Sinalize outliers e inconsistências.
7. Saída: `.xlsx` ou Google Sheets (pergunte qual), além de `.csv` + `_sources.csv` + markdown sempre. Cabeçalho de material de trabalho.
8. Sumário: carga de verificação (contagens de not_present / unclear / needs_review por coluna), colunas sinalizadas, onde estão os arquivos, lembrete de que cada célula é uma pista, não um achado.

```
/societario:tabular-review
/societario:tabular-review --schema .review-schema.yaml --docs ./vdr/02-Contratos/
/societario:tabular-review --template ma-diligence
```

**`--schema <path>`:** Use schema existente em vez de montar um novo. Útil para re-rodadas e adições incrementais.

**`--template <name>`:** Parta de um template em `references/`. Atualmente: `ma-diligence`.

**`--docs <path>`:** Origem dos documentos. Pasta local, ID de pasta no Drive, ou caminho no VDR. Se omitido, pergunta.

**`--output <xlsx|gsheets|csv>`:** Formato de saída. Se omitido, pergunta.

**`--sample <n>`:** Tamanho da amostra para checagem do schema. Padrão 5.

---

## Contexto da matéria

**Contexto da matéria.** Confira `## Workspaces de matéria` no CLAUDE.md nível-prática. Se `Habilitado` for `✗` (padrão para usuários in-house), pule o resto deste parágrafo — skills usam contexto nível-prática e a maquinaria de matéria fica invisível. Se habilitado e não houver matéria ativa, pergunte: "Para qual matéria? Rode `/societario:matter-workspace switch <slug>` ou diga `practice-level`." Carregue o `matter.md` da matéria ativa para contexto e overrides específicos. Grave saídas na pasta da matéria em `~/.claude/plugins/config/claude-for-legal/societario/matters/<matter-slug>/`. Nunca leia arquivos de outra matéria, salvo se `Contexto cross-matter` estiver `on`.

---

## Propósito

Você tem uma pilha de documentos e uma lista de perguntas que precisam ser respondidas consistentemente em cada um. Uma request list de diligência. Auditoria de contratos com fornecedores. Revisão de portfolio de locações. A saída é uma tabela: linhas-documento, colunas-dados, cada célula rastreável até as palavras exatas na fonte.

Isto não é spotting de issues. `diligence-issue-extraction` acha os 30 problemas escondidos em 2.000 documentos. Esta skill responde às mesmas 15 perguntas sobre os 2.000 documentos. Ambas são legítimas; respondem perguntas diferentes.

Também não substitui um(a) humano(a) lendo o documento. Cada célula que esta skill produz é uma **pista que precisa de verificação**, não um achado. A saída é desenhada para tornar a verificação rápida, não para puli-la.

## Carregue contexto

- `~/.claude/plugins/config/claude-for-legal/societario/CLAUDE.md` → estrutura de diligência, limiares de materialidade, formato da casa
- `~/.claude/plugins/config/claude-for-legal/societario/deals/[code]/deal-context.md` se trabalhando numa operação específica
- Schema existente, se o(a) usuário(a) tiver (`.review-schema.yaml`)

## O sistema de tipos de coluna

O que torna uma revisão tabular útil é que a Coluna C significa a mesma coisa na linha 1 e na linha 200. Texto livre derrapa. Tipos seguram.

Cada coluna tem um **tipo** que restringe o formato da resposta:

| Tipo | O que retorna | Use para |
|---|---|---|
| `verbatim` | Citação exata do documento, caractere por caractere | Termos definidos, linguagem de cláusula operativa, qualquer coisa em que as palavras importem |
| `classify` | Um valor de uma lista fixa que você define | Sim/Não, presente/ausente, variantes de cláusula (ex.: "consentimento livre" / "consentimento não pode ser negado sem motivo razoável" / "omisso") |
| `date` | Data ISO | Data-base, vencimento, prazo de notificação |
| `duration` | Número + unidade | Prazo, antecedência mínima, sobrevida |
| `currency` | Número + código de moeda | Limites, thresholds, fees, preço de aquisição |
| `number` | Número puro | Contagens, percentuais, referências de página |
| `free` | Texto livre curto | Use com parcimônia — é o tipo que derrapa. Só quando os outros genuinamente não couberem. |

**A regra do verbatim:** Toda coluna não-`verbatim` também captura a citação exata da fonte que sustenta a resposta, como campo acompanhante. A resposta na célula é a interpretação; a citação é a prova. Uma célula `classify` que diz "consentimento não pode ser negado sem motivo razoável" é inútil sem a frase de onde veio, porque o trabalho do(a) revisor(a) é checar se essa é a leitura certa.

## Os três estados de "não encontrado"

Célula em branco esconde informação. Force um de três estados explícitos sempre que não puder produzir resposta positiva:

| Estado | Significado | Quando usar |
|---|---|---|
| `not_present` | O documento foi lido e a cláusula não está lá | Você está confiante de que o assunto não é tratado |
| `unclear` | Tem algo lá mas você não consegue classificar com confiança | Redação ambígua, cláusula parcial, dispositivos conflitantes |
| `needs_review` | Você achou algo mas o(a) humano(a) tem de decidir | Edge case, redação incomum, a resposta depende de juízo que o schema não captura |

Estes são três pedaços de informação diferentes. Um time de operação lida com "o contrato é omisso sobre cessão" muito diferentemente de "a cláusula de cessão é ambígua". Colapsar isso numa célula em branco perde a distinção.

## Workflow

### Passo 0: o quê e onde

Confirme:
1. **Documentos.** Onde estão? VDR MCP (Box, Datasite, iManage, Intralinks), pasta local, pasta do Google Drive ou lista de arquivos. Quantos? Se >200, avise que vai levar tempo e ofereça começar com subconjunto filtrado por materialidade.
2. **Schema.** Quais colunas? Dois caminhos:
   - Usuário(a) escolhe template em `references/` (M&A diligência padrão é o default)
   - Usuário(a) descreve colunas em linguagem natural e você estrutura no schema tipado
3. **Saída.** Excel (`.xlsx`) ou Google Sheets — pergunte em qual o time trabalha. CSV e markdown sempre escritos como fallback. Saída vai para a pasta da operação, Drive ou onde o(a) usuário(a) disser.

### Passo 1: monte e confirme o schema

Transforme a lista de colunas do(a) usuário(a) em schema estruturado. Para cada coluna: um `id` estável, um `label` legível, um `type`, um `prompt` (a pergunta que um(a) revisor(a) lendo o documento faria) e, para colunas `classify`, uma lista de `options`.

Grave em `.review-schema.yaml` ao lado da saída. Este arquivo é o artefato reutilizável — o(a) usuário(a) pode editar, adicionar coluna, re-rodar contra novos documentos. Mostre ao(à) usuário(a) e confirme antes do fan-out.

```yaml
schema:
  name: "Diligência M&A — Projeto [Código]"
  created: 2026-05-07
  columns:
    - id: contraparte
      label: "Contraparte"
      type: verbatim
      prompt: "Quem é a parte contratante além do target?"
    - id: data_base
      label: "Data-base"
      type: date
      prompt: "Quando o contrato entrou em vigor?"
    - id: mudanca_controle
      label: "Mudança de Controle"
      type: classify
      options: [omisso, anuencia_necessaria, anuencia_nao_pode_ser_negada_sem_motivo, rescisao_automatica, apenas_notificacao]
      prompt: "O contrato trata mudança de controle do target? O que exige?"
    - id: cessao
      label: "Restrições de Cessão"
      type: classify
      options: [omisso, anuencia_necessaria, anuencia_nao_pode_ser_negada_sem_motivo, livremente_cessivel, cessivel_a_partes_relacionadas]
      prompt: "O target pode ceder este contrato? Que restrições se aplicam? (CC arts. 286-298 — cessão de crédito; 299-303 — assunção de dívida)"
    # ... mais colunas
```

### Passo 2: rodada-amostra

Não jogue fan-out em 200 documentos com schema não testado. Rode 3–5 documentos primeiro. Mostre as linhas ao(à) usuário(a). Procure por:
- Colunas onde a maioria das respostas é `unclear` — o prompt está ambíguo, reescreva
- Colunas `classify` em que respostas não encaixam nas options — adicione opções ou mude para `free`
- Colunas `verbatim` devolvendo paráfrase — reforce que é caractere-por-caractere

Ajuste o schema, re-rode a amostra, confirme. Isso evita que o(a) usuário(a) tenha de jogar fora uma rodada completa.

### Passo 3: fan-out

Um sub-agente por documento, em paralelo. Cada sub-agente:

1. Lê o documento inteiro (não um chunk de RAG — o inteiro).
2. Para cada coluna, encontra o dispositivo relevante.
3. Devolve linha estruturada: para cada coluna, `{value, state, quote, location}`.
   - `value` é a resposta tipada (ou null se `state` não for `answered`)
   - `state` é `answered | not_present | unclear | needs_review`
   - `quote` é o texto suporte verbatim (exato, sem paráfrase, sem reticências dentro de uma frase — se cortar, corte em fim de frase e marque)
   - `location` é onde a citação mora (número de cláusula, título, página — o que o documento fornecer)

**A citação não é opcional, e a regra do verbatim é mecânica, não exortação.** Cada sub-agente tem de cumprir tudo abaixo antes de devolver célula com `state: answered`:

- A `quote` DEVE ser cópia caractere-por-caractere de texto contíguo do documento-fonte, recuperável no `location` que o sub-agente citar. NÃO componha citação a partir de título de cláusula + boilerplate que você espera estar lá. NÃO parafraseie e chame de verbatim. NÃO reconstrua citação a partir de memória de como tais cláusulas "geralmente" são redigidas. NÃO preencha lacunas da fonte com reticências costurando texto não contíguo.
- O `location` deve ser específico o bastante para que a passada de normalização re-abra o documento e re-leia o mesmo trecho — número de cláusula, título ou página que o(a) revisor(a) consegue navegar.
- Se o sub-agente não conseguir localizar e copiar o texto exato (fonte truncada, OCR ruim, dispositivo implícito mas não escrito, título visível mas corpo não carregado), o estado da célula é `needs_review`, o `value` é null e `notes` DEVE conter `quote_unavailable: <razão>`. NUNCA é aceitável marcar `state: answered` com citação composta ou reconstruída.
- A mesma regra se aplica a colunas tipo `verbatim` E às citações-fonte acompanhantes de células `classify` / `date` / `duration` / `currency` / `number` / `free`. A citação de suporte carrega a mesma obrigação verbatim do valor da célula.

A passada de normalização no Passo 4 spot-checka isso re-lendo a fonte no `location` citado e comparando a `quote` armazenada caractere-por-caractere contra o texto-fonte. Diferença rebaixa a célula para `needs_review`, anota `quote_mismatch` e sinaliza toda a coluna para spot-check ampliado — se um sub-agente compôs citação, outros na mesma rodada podem ter feito o mesmo.

### Passo 4: normalize

Após o fan-out, leia a tabela inteira coluna por coluna. Esta é a passada que pega o modo de falha de toda ferramenta de revisão tabular: a mesma cláusula interpretada inconsistentemente entre documentos.

Para cada coluna `classify`:
- Cheque que todo valor `answered` está na lista de opções. Outliers são reclassificados ou rebaixados a `needs_review`.
- Cheque clusters: se 180 documentos dizem `anuencia_necessaria` e 20 dizem `anuencia_nao_pode_ser_negada_sem_motivo`, provavelmente é real. Se 195 dizem `anuencia_necessaria` e 5 dizem `livremente_cessivel`, olhe os 5 — ou são genuinamente diferentes ou foram mal classificados.

Para cada coluna `date` / `duration` / `currency`:
- Cheque consistência de formato. Normalize.
- Sinalize valores implausíveis (prazo de 99 anos, cap de R$ 1) como `needs_review`.

Para cada coluna `verbatim` E para as citações-fonte acompanhantes em toda outra coluna:
- Spot-check re-abrindo o documento-fonte no `location` citado para amostra aleatória (no mínimo 3–5 linhas por coluna, ou 10% das linhas, o que for maior) e comparando a `quote` armazenada caractere-por-caractere com a fonte.
- Se alguma citação for composta, parafraseada, reconstruída ou não puder ser localizada no trecho citado: rebaixe a célula para `needs_review` com `quote_mismatch` em notas e sinalize a coluna inteira — expanda o spot-check para o resto da coluna em vez de assumir que as outras linhas estão limpas. Uma citação fabricada já basta para justificar ampliar a checagem.
- Uma célula com `state: answered` e citação que não bate é falha de severidade maior do que célula `unclear` ou `needs_review` — falseia a trilha probatória. Rebaixe com agressividade.

### Passo 5: saída

Escreva a tabela em três formatos:

**Markdown** (sempre, para revisão dentro da sessão):
```markdown
| Documento | Contraparte | Data-base | Mudança de Controle | Cessão | ⚠️ Sinalizações |
|---|---|---|---|---|---|
| MSA Fornecedor — Acme | Acme Ltda. | 2023-04-01 | anuencia_necessaria | anuencia_necessaria | — |
| Contrato de Fornecimento — Beta | Beta S.A. | 2021-11-15 | ⚠️ unclear | omisso | CoC ambíguo cl. 14.2 |
```

**CSV** (`.csv`, sempre):
Um arquivo para os valores, outro arquivo acompanhante para citações e localizações (`_sources.csv`). Mantém o principal limpo e a trilha probatória completa.

**Excel** (`.xlsx`) ou **Google Sheets** — o que o(a) usuário(a) usa. Pergunte; não chute. Ambos seguem a mesma estrutura de pasta de trabalho (ver `references/excel-output.md` e `references/gsheets-output.md`). Para Excel: Claude in Excel (Office agent) se disponível, `openpyxl` como fallback. Para Sheets: Sheets MCP se disponível, Sheets API via ADC, importação de CSV como fallback. Na saída em planilha:
- Cada coluna de dado é pareada com coluna-fonte oculta contendo citação e localização. Comentários de célula (Excel) ou notas (Sheets) na coluna visível mostram a citação ao passar o cursor.
- Cor por estado: branco = answered, amarelo = unclear ou needs_review, cinza = not_present.
- Coluna `Verificado` por coluna de dado, em branco por padrão. O(a) revisor(a) marca. Este é o padrão verify/flag que torna a tabela auditável — o time da operação vê de relance o que humano(a) de fato checou.
- Aba `_schema` com as definições de coluna, para o arquivo ser autodocumentado.

Antepôe o cabeçalho de material de trabalho do plugin config `## Outputs` como linha topo. Junto, inclua nota de distribuição:

> Esta revisão deriva de documentos-fonte que podem ser privilegiados, confidenciais ou ambos. Herda o status de sigilo profissional (Lei 8.906/94, art. 7º, XIX — EOAB) e confidencialidade das fontes — distribuição fora do círculo do sigilo pode comprometer a proteção. Guarde com os arquivos privilegiados da matéria e tome decisões de distribuição deliberadamente.

### Passo 6: sumário

Depois de escrita a tabela, dê um readout de uma tela ao(à) usuário(a):
- Contagem de documentos, contagem de colunas, linhas completadas
- Contagem de `not_present`, `unclear`, `needs_review` por coluna — esta é a carga de verificação
- Quaisquer colunas em que a passada de normalização sinalizou >10% das linhas
- Onde estão os arquivos de saída
- Lembrete: cada célula é uma pista, não um achado. Verificação é obrigatória antes de isto informar uma rep, um anexo ao SPA ou um memo.

## Encerre com a árvore de decisão de próximos passos

Termine com a árvore de decisão de próximos passos do CLAUDE.md `## Outputs`. Customize as opções para o que esta skill acabou de produzir — os cinco ramos default (redigir o X, escalar, buscar mais fatos, observar e aguardar, outra coisa) são ponto de partida, não trava. A árvore é a saída; o(a) advogado(a) escolhe.

## O que esta skill NÃO faz

- **Não substitui ler os documentos.** Diz onde olhar.
- **Não produz scores de confiança.** 0,73 não é informação. Os estados `unclear` / `needs_review` e as citações verbatim são o sinal de confiança — se a citação não sustenta o valor, sinalize.
- **Não pula documento em silêncio.** Todo documento que o(a) usuário(a) apontou recebe uma linha. Documento que não pôde ser lido recebe linha de `needs_review` com nota.
- **Não finge que paráfrase é citação.** A trilha probatória é o ponto inteiro.

## Relação com outras skills

- `diligence-issue-extraction` acha problemas; esta extrai dados. Se uma extração revelar problema (cláusula de onerosidade excessiva com gatilho específico, "poison pill", tag-along não-padrão art. 254-A Lei 6.404), anote e sugira rodar diligence-issue-extraction no documento.
- `material-contract-schedule` monta uma tabela específica (o anexo de contratos materiais). Pode consumir a saída desta skill diretamente — o anexo é visão filtrada e reformatada de uma revisão tabular.
- `ai-tool-handoff` passa revisão em lote para Luminance/Kira quando o corpus é grande demais ou o time prefere plataforma dedicada. Esta é a opção in-house para o que ela consegue lidar — rode primeiro, passe o resíduo.

## Salvaguardas de saída

Toda saída recebe o cabeçalho de material de trabalho. Toda célula recebe citação-fonte ou estado sinalizado. O sumário diz explicitamente que verificação é obrigatória. A coluna `Verificado` no Excel torna o estado de verificação auditável. Isto não é ferramenta que deixa pular leitura; é ferramenta que torna a leitura mais rápida.
