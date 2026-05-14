# Especificação de Saída Excel

> **Adaptação BR não oficial.** Veja `references/terminology-us-to-br.md` na raiz do plugin.

O arquivo Excel é o entregável que a maioria dos times de operação vai efetivamente abrir. Acerte.

## Se Claude in Excel / Office agent estiver disponível

Construa a pasta de trabalho diretamente no Excel via Office agent. Este é o caminho preferido porque preserva formatação, deixa o(a) revisor(a) trabalhar na ferramenta nativa e suporta o padrão de comentário de célula nativamente.

## Se não, use openpyxl

Cheque com `python3 -c "import openpyxl"`. Se não instalado, ofereça instalar (`pip3 install openpyxl`) ou caia para CSV.

## Estrutura da pasta de trabalho

**Aba 1: `Revisao`** (o grid principal)
- Linha 1: cabeçalho de material de trabalho (célula mesclada, o cabeçalho do plugin config `## Outputs`)
- Linha 2: labels das colunas
- Linha 3+: uma linha por documento
- Coluna A: nome / caminho do documento
- Colunas B em diante: uma por coluna do schema, na ordem do schema
- Depois de cada coluna de dado, uma coluna `_source` oculta com `[citação] | [localização]`
- Comentário de célula na célula da coluna de dado = a citação e localização (mostra ao passar o cursor mesmo com `_source` oculta)
- Preenchimento de célula por estado: sem preenchimento = `answered`, `#FFF2CC` (amarelo claro) = `unclear` ou `needs_review`, `#EFEFEF` (cinza claro) = `not_present`
- Coluna `Verificado` depois de cada grupo de [dado + _source]: em branco por padrão. O(a) revisor(a) preenche. Validação de dados (dropdown): `✓`, `✗`, `?`.

**Aba 2: `Sinalizacoes`**
- Uma linha por célula sinalizada como `unclear` ou `needs_review`
- Colunas: Documento, Coluna, Estado, Valor (se houver), Citação, Localização, Nota
- Esta é a fila de trabalho de verificação. Ordene por coluna para que o(a) revisor(a) possa batchar julgamentos similares.

**Aba 3: `_schema`**
- As definições de coluna do `.review-schema.yaml`, uma linha por coluna: id, label, type, options, prompt
- Torna o arquivo autodocumentado. Um(a) sócio(a) que abrir seis meses depois consegue ver exatamente o que foi perguntado.

**Aba 4: `_sumario`**
- Contagem de documentos, contagem de colunas, data da rodada
- Por-coluna contagens de answered / not_present / unclear / needs_review
- Lista de colunas que a passada de normalização sinalizou
- O texto-lembrete de verificação

## O que NÃO fazer

- Não escreva coluna com percentual de confiança. Não é informação. O estado + citação é o sinal.
- Não trunque citações para caberem numa célula. Quebre o texto ou ponha a citação inteira no comentário.
- Não mescle células na região de dados. Advogados(as) vão ordenar e filtrar.
- Não escreva a tabela sem as abas `_schema` e `_sumario`. A autodocumentação é o que torna o arquivo confiável.

## Cabeçalho de material de trabalho

Use o cabeçalho do plugin config `## Outputs`:
- Para advogado(a) inscrito(a) na OAB: `MATERIAL DE TRABALHO — PRIVILEGIADO E CONFIDENCIAL (Art. 7º, XIX, Lei 8.906/94 — EOAB)`
- Para não-advogado(a): `NOTAS DE PESQUISA — NÃO É PARECER JURÍDICO — REVISE COM ADVOGADO(A) INSCRITO(A) NA OAB ANTES DE AGIR`

Lembrar que o sigilo profissional tem limites perante CADE, CVM, RFB e ANPD em poderes de fiscalização — quando a pegada da matéria envolver essas autoridades, adicione a nota de ressalva do CLAUDE.md.

## Defesa contra injeção de fórmula

Antes de escrever qualquer célula em Excel, Sheets ou CSV, neutralize injeção de fórmula. Texto vindo de contraparte (citações de contrato, nomes de parte, dados de despachante, exports de CLM) é controlado por atacante. Célula começando com `=`, `+`, `-`, `@`, tab, CR ou LF será interpretada como fórmula ou quebrará a estrutura da linha.

- **Prefixe com aspas simples:** `'=SUM(A1:A10)` → `=SUM(A1:A10)` (mostrado como texto, não executado)
- **Aplica-se a toda célula que contém texto vindo de documento, resultado de ferramenta ou paste de usuário(a).** Cabeçalhos de coluna que você controla e valores computados que você produz são seguros.
- **CSV: também escape vírgulas embutidas, aspas duplas, quebras de linha** (quoting RFC 4180).
- Isto não é opcional. Uma planilha que seu(sua) usuário(a) abre no Excel que dispara macro ou exfiltra dados via DDE é ataque de supply chain ao(à) usuário(a).
