# Conectores

Os plugins ficam muito melhores quando ligados a fontes oficiais. Se você opera uma fonte de dados jurídicos, ferramenta de pesquisa, CLM, GED, plataforma de eDiscovery ou sistema de gestão de escritório (BR ou internacional), queremos seu conector MCP na suite.

## O que faz um bom conector MCP jurídico

- **Servidor MCP remoto sobre HTTPS** com OAuth ou API-key (streamable HTTP ou SSE)
- **Ferramentas de leitura** — search, fetch, list. Ferramentas de escrita (criar, enviar, protocolar) exigem confirmação explícita no lado do cliente; documente isso na descrição.
- **Proveniência nos resultados** — devolva a fonte, data de captura e identificador citável. Os plugins marcam cada citação por fonte; seu conector deve viabilizar isso.
- **Sem instruções nos resultados** — os plugins tratam conteúdo recuperado como dado, não comando. Se seu retorno inclui metadados/notas de sistema, marque-os claramente.
- **Rate limits e erros graciosos** — os plugins têm fallback quando o conector não responde; erro limpo é melhor que timeout.

## Como submeter

1. Publique seu servidor MCP e documente ferramentas, fluxo de auth e cobertura.
2. Abra um PR adicionando-o ao `.mcp.json` do plugin relevante com URL, método de auth e descrição em uma linha.
3. Indique para quais áreas / plugins é mais útil.
4. Testamos contra os workflows do plugin e fazemos merge. Conectores que passam nos checks de qualidade e resistência a injeção vão pro `.mcp.json` padrão; os demais ficam documentados no README do plugin para o usuário adicionar.

## Conectores atuais (herança do upstream — adequação ao BR varia)

Conectores que já vêm no `.mcp.json` padrão. **Aviso:** vários cobrem primariamente jurisdição EUA; estamos documentando quais funcionam bem no Brasil e quais precisam de complemento.

| Conector | Plugins | Aplicabilidade BR |
|---|---|---|
| **Slack** | todos | ✓ neutro |
| **Google Drive** (`gdrive`) | todos | ✓ neutro |
| **CourtListener** | npj, propriedade-intelectual, contencioso, estudante-direito | ✗ só jurisprudência EUA — para BR use JusBrasil/Lexml/STF/STJ via web search ou MCP customizado |
| **Descrybe** | npj, propriedade-intelectual, estudante-direito | ✗ EUA |
| **Definely** | comercial, societario | ✓ análise de contratos é jurisdição-neutra |
| **iManage** | comercial, societario | ✓ GED neutro |
| **Solve Intelligence** | societario, propriedade-intelectual | △ verifique cobertura INPI |
| **TopCounsel** | comercial, societario, contencioso | △ verificar |
| **Box** | societario | ✓ neutro |
| **Ironclad** | comercial | ✓ CLM neutro |
| **DocuSign / DocuSign CLM** | comercial | ✓ aceita ICP-Brasil via integração; alternativa BR: Clicksign, D4Sign, Adobe Sign |
| **Everlaw** | contencioso | △ eDiscovery EUA-cêntrico |
| **Trellis** | contencioso | ✗ docket EUA — para BR use PJe/e-SAJ/e-Proc/Projudi |
| **Aurora** | contencioso | △ verificar |
| **Courtroom5** | npj | ✗ pro se EUA |
| **Lawve AI** | builder-hub | △ verificar |
| **Linear / Jira / Asana** | produto | ✓ neutros |

Veja o `.mcp.json` em cada plugin para a lista canônica.

## Conectores desejados (foco BR)

Os plugins precisam de conectores brasileiros nativos. Se você opera ou constrói algum destes, veja "Como submeter" acima.

### Jurisprudência e legislação (primárias)
- **STF / STJ / TST / TRFs / TJs** — APIs ou scraping autorizado para julgados, súmulas, repetitivos, repercussão geral. Hoje quem é melhor: JusBrasil API (paga), Jusbrasil scraper, BuscaTextual STJ, BuscaTextual STF.
- **Lexml / Planalto** — íntegra de leis federais, decretos e MPs com permalink e data de consolidação.
- **DOU (Imprensa Nacional)** — diário oficial com filtro por agência, retroativo.
- **Diários da Justiça estaduais (DJEs)** — publicações processuais (essencial para `contencioso`).

### Processo eletrônico
- **PJe (CNJ)** — consulta processual unificada via API, push de movimentações.
- **e-SAJ (Softplan — TJSP, TJMS etc.)** — consulta e download de peças.
- **e-Proc (TRFs 2/4)** — idem.
- **Projudi** — tribunais estaduais que ainda usam.
- **eproc/STJ** — peças e andamentos no STJ.

### Órgãos administrativos e reguladores
- **INPI** — busca de marca (push base), patentes, software, status de processo via API.
- **ANPD** — agenda regulatória, resoluções, comunicados, decisões em PIA pública.
- **CVM** — fato relevante, ITR/DFP/IPE, processos administrativos, ofícios circulares.
- **BCB / SISBACEN** — circulares, cartas circulares, normativos, RDE-IED.
- **ANATEL / ANVISA / ANEEL / ANS / ANTT** — consultas públicas, normativos, AIR.
- **CADE** — atos de concentração, processos administrativos, jurisprudência.
- **CGU** — empresas inidôneas, acordo de leniência, CNEP/CEIS.
- **COAF** — comunicações, resoluções, expectativas regulatórias PLD.
- **Junta Comercial (DREI/JUCESP/JUCERJA etc.)** — atos arquivados, certidão simplificada, busca CNPJ.
- **Receita Federal** — CNPJ, situação fiscal, certidões.

### Trabalhista
- **e-Social / Caged** — eventos trabalhistas; obviamente apenas leitura/relatório com escopo restrito.
- **TST / TRTs** — jurisprudência, súmulas, OJs, IRRs.

### CLM / assinaturas BR
- **Clicksign, D4Sign, Adobe Sign BR, Vault, Sertis** — assinatura eletrônica ICP-Brasil e simples.
- **Linte, NetLex, Lawit, Doc9, Contraktor** — CLM brasileiros.

### Outros
- **JusBrasil** — busca consolidada e push de andamento.
- **Lexis BR / Thomson Reuters Checkpoint** — onde houver cobertura BR.

## Dúvidas

Abra uma issue. Para parcerias / integrações, veja contato no README de cada plugin.
