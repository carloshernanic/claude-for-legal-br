# Especificação de Saída Google Sheets

> **Adaptação BR não oficial.** Veja `references/terminology-us-to-br.md` na raiz do plugin.

Para times no Google Workspace. Mesma estrutura da saída em Excel, mecânica diferente. Se tanto o caminho Excel quanto o Sheets estiverem disponíveis, pergunte ao(à) usuário(a) o que prefere — não chute pelo ambiente.

## Como escrever

Três caminhos, em ordem de preferência:

1. **Google Sheets MCP** (se uma MCP `gdrive` ou `gsheets` com capacidade de write/create estiver conectada). Crie a planilha, escreva as abas, configure formatação via API.
2. **API do Google Sheets via ADC** (se o(a) usuário(a) tiver `gcloud auth application-default login --enable-gdrive-access` configurado e Python `google-api-python-client` disponível). Use `sheets.spreadsheets().create()` e `batchUpdate` para formatação.
3. **Fallback: CSV + importação manual.** Escreva os CSVs, peça ao(à) usuário(a) que importe para o Sheets. Também escreva um `format-instructions.md` para que possa aplicar o color coding e a validação de dados manualmente.

Não assuma acesso de escrita que você não verificou. Cheque primeiro; caia graciosamente.

## Estrutura da pasta de trabalho

Espelhe a especificação Excel exatamente — mesmas abas, mesma semântica, mecânica nativa de Sheets:

**Aba: `Revisao`** (o grid principal)
- Linha 1: cabeçalho de material de trabalho (célula mesclada)
- Linha 2: labels das colunas
- Linha 3+: uma linha por documento
- Coluna A: nome / link do documento (se as fontes estão no Drive, hyperlink para o arquivo — esta é vantagem de Sheets sobre Excel)
- Colunas B em diante: uma por coluna do schema
- **Citações-fonte vão em notas de célula** (notas de Sheets, não comentários — notas são anotações persistentes, comentários são threads de colaboração). Notas aparecem ao passar o cursor e exportam para `.xlsx` como comentários.
- Preenchimento de célula por estado: default = `answered`, amarelo claro = `unclear` ou `needs_review`, cinza claro = `not_present`. Use `repeatCell` com `userEnteredFormat.backgroundColor` em `batchUpdate`.
- Coluna `Verificado` depois de cada grupo: em branco por padrão, validação de dados dropdown `✓ | ✗ | ?` via `setDataValidation`.

**Aba: `Sinalizacoes`**
- Igual à especificação Excel. Uma linha por célula sinalizada.

**Aba: `_schema`**
- Definições de coluna do `.review-schema.yaml`.

**Aba: `_sumario`**
- Contagens, colunas sinalizadas, lembrete de verificação.

## Vantagens específicas de Sheets a usar

- **Hyperlinks para documentos-fonte.** Se os documentos revisados estão no Drive (comum para exports de VDR e repositórios internos), o nome do documento de cada linha deve ser hyperlink para o arquivo. Este é o padrão click-to-source, e Sheets faz nativamente.
- **Revisão compartilhada.** Sheets lida com revisão concorrente melhor que `.xlsx` local. Se o time da operação quer dividir o trabalho de verificação, este é o formato.
- **Intervalos nomeados (named ranges) para o schema.** Defina intervalo nomeado sobre cada coluna para que fórmulas downstream (tabelas dinâmicas, contagens condicionais) fiquem legíveis.
- **Formatação condicional por coluna de estado.** Se você escrever coluna `_state` oculta por coluna de dado, dá para dirigir o color coding por ela com regras de formatação condicional — mais limpo que formatação célula-a-célula e sobrevive à ordenação.

## Pegadinhas específicas de Sheets

- **Notas são por célula e invisíveis na impressão.** Se a saída vai ser impressa ou virar PDF para reunião com sócio(a), grave também as citações na aba `Sinalizacoes` para sobreviverem.
- **Sheets tem limite de 10 milhões de células.** Você não bate em revisão jurídica, mas se alguém tentar gridar 50.000 documentos com 30 colunas mais colunas-fonte, avise.
- **Defaults de compartilhamento.** Pelo perfil de prática do plugin, isto é material de trabalho de advogado(a). Crie a planilha com compartilhamento restrito (apenas owner) e diga ao(à) usuário(a) para compartilhar deliberadamente. Não use default "qualquer pessoa com o link". O sigilo profissional do art. 7º, XIX, EOAB pressupõe controle de distribuição.
- **Escape de fórmula.** Se uma citação verbatim começa com `=`, `+`, `-` ou `@`, prefixe com aspas simples (`'`) para o Sheets não tentar interpretar como fórmula. Modo de falha real: cláusula que começa "- As partes acordam..." vai renderizar como erro de fórmula sem o escape.

## Cabeçalho de material de trabalho

Use o cabeçalho do plugin config `## Outputs`:
- Para advogado(a) inscrito(a) na OAB: `MATERIAL DE TRABALHO — PRIVILEGIADO E CONFIDENCIAL (Art. 7º, XIX, Lei 8.906/94 — EOAB)`
- Para não-advogado(a): `NOTAS DE PESQUISA — NÃO É PARECER JURÍDICO — REVISE COM ADVOGADO(A) INSCRITO(A) NA OAB ANTES DE AGIR`

Quando a matéria envolver CADE, CVM, RFB ou ANPD em exercício de poderes de fiscalização, adicione a nota de ressalva do CLAUDE.md sobre os limites do sigilo profissional.

## O que NÃO fazer

Igual à especificação Excel: nada de percentuais de confiança, nada de citações truncadas, nada de células mescladas na região de dados, e sempre escreva as abas `_schema` e `_sumario`.

## Defesa contra injeção de fórmula

Antes de escrever qualquer célula em Excel, Sheets ou CSV, neutralize injeção de fórmula. Texto vindo de contraparte (citações de contrato, nomes de parte, dados de despachante, exports de CLM) é controlado por atacante. Célula começando com `=`, `+`, `-`, `@`, tab, CR ou LF será interpretada como fórmula ou quebrará a estrutura da linha.

- **Prefixe com aspas simples:** `'=SUM(A1:A10)` → `=SUM(A1:A10)` (mostrado como texto, não executado)
- **Aplica-se a toda célula que contém texto vindo de documento, resultado de ferramenta ou paste de usuário(a).** Cabeçalhos de coluna que você controla e valores computados que você produz são seguros.
- **CSV: também escape vírgulas embutidas, aspas duplas, quebras de linha** (quoting RFC 4180).
- Isto não é opcional. Uma planilha que seu(sua) usuário(a) abre no Excel que dispara macro ou exfiltra dados via DDE é ataque de supply chain ao(à) usuário(a).
