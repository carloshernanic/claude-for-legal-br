# Diligence Grid — template de managed-agent

> Adaptação BR não oficial. Cookbook calibrado para DD brasileira (Junta Comercial, RFB, e-Social, JUCESP, IBAMA, CADE, CVM, INPI, LGPD, Lei Anticorrupção). Tribunais: PJe, e-SAJ, e-Proc, JusBrasil. Veja `references/terminology-us-to-br.md`.

## Visão geral

Revisão em lote de documentos sobre um data room virtual (VDR). Dois modos:

- **watch** — monitora o VDR para novos uploads desde um cutoff, classifica cada um contra as categorias da request-list de due diligence da equipe contratante, e sinaliza uploads em categorias de alta prioridade (Contratos Materiais, Litígios — PJe/e-SAJ/e-Proc/JusBrasil, PI — INPI).
- **grid** — roda uma revisão tabular contra um schema de colunas sobre uma pasta de documentos. Uma linha por documento, uma coluna por dado, cada célula referenciada a uma citação literal da fonte. O cavalo de batalha da DD em M&A brasileiro.

Mesma fonte do plugin [`societario`](../../societario) — este diretório é o cookbook de Managed Agent para `POST /v1/agents`. O modo grid é a skill `tabular-review`, executando headless sobre uma frota de workers extratores.

## ⚠️ Antes de fazer o deploy

- **Cada célula é uma pista, não uma conclusão.** Um diligence grid não é uma declaração, um quadro de exceções ou um memorando de DD até que um(a) advogado(a) inscrito(a) na OAB tenha lido os documentos subjacentes. A citação literal em cada célula está lá para o(a) revisor(a) verificar rapidamente — use-a.
- **O filtro de materialidade e as classificações de coluna aplicam heurísticas, não juízo jurídico.** Um contrato que o schema classifica como imaterial pode ser exatamente o que mata o deal (ex.: cláusula de change of control disparada em contrato de fornecimento crítico). Uma célula "respondida" ainda pode estar errada se o extrator interpretou mal a cláusula. O tempo de revisão escala com `unclear` + `needs_review` + `answered` — não apenas com os sinalizados.
- **O modo watch classifica metadados e prévias, não documentos completos.** Um novo upload que o classificador rotula "baixa prioridade" pode ainda ser a side letter / aditivo ao contrato social que muda o deal. Trate o relatório do watch como uma fila, não como um filtro.
- **Documentos enviados pela contraparte são input não-confiável também para a toolchain.** A defesa de injeção de fórmula CSV do grid-writer é obrigatória, não opcional — veja a seção de segurança abaixo.

## Deploy

```bash
export ANTHROPIC_API_KEY=sk-ant-...
export BOX_MCP_URL=...
export GDRIVE_MCP_URL=...
export IMANAGE_MCP_URL=...          # opcional; ajuste o default do toolset se usado
export DEFINELY_MCP_URL=...         # opcional; para QA de estrutura de cláusula do passe normalizer
../../scripts/deploy-managed-agent.sh diligence-grid
```

## Eventos de steering

Veja [`steering-examples.json`](./steering-examples.json).

## Segurança e handoffs

Documentos do VDR — contratos, atas de Assembleia Geral / reuniões de Conselho de Administração, side letters, certidões negativas (RFB, FGTS, trabalhista, criminal), uploads da contraparte — são **input não-confiável**. Um contrato enviado pela contraparte pode conter strings destinadas a manipular o(a) revisor(a) ou a toolchain downstream. Isolamento em quatro camadas mantém a "mão de Write" e a "mão de MCP" longe dos documentos:

| Camada | Toca documentos não-confiáveis? | Ferramentas | Conectores |
|---|---|---|---|
| **`doc-reader`** | **Sim** (somente leitura) | `Read`, `Grep` | Box, Google Drive, iManage (leitura) |
| **`extractor`** | **Sim** (somente leitura) | `Read`, `Grep` | Nenhum |
| `normalizer` / Orquestrador | Não | `Read`, `Grep`, `Glob`, `Agent` | Nenhum (definely opcional, somente leitura) |
| **`grid-writer`** (detentor do Write) | Não | `Read`, `Write` | Nenhum |

`doc-reader` e `extractor` retornam JSON com tamanho limitado e validado contra schema. O orquestrador e o `normalizer` veem apenas dados estruturados. O `grid-writer` produz `./out/diligence-grid-<data>.csv`, `./out/diligence-grid-<data>_sources.csv` e `./out/diligence-grid-<data>-summary.md`.

**Injeção de fórmula CSV.** Toda célula escrita pelo `grid-writer` — valores, citações literais, localizações, nomes de documentos, rótulos de coluna — é verificada no primeiro caractere contra `=`, `+`, `-`, `@`, tab e carriage return. Células que casam recebem um apóstrofe simples como prefixo antes de aterrissar no CSV. Contratos enviados pela contraparte rotineiramente contêm strings que Excel e Google Sheets, do contrário, executariam como fórmulas (`=HYPERLINK(...)` para exfiltração, `=cmd|...` DDE em Excel antigo) no momento em que a equipe do deal abrir o arquivo. O CSV de fontes é a maior superfície de exposição — citações literais são a superfície controlada pelo atacante.

**Xlsx é uma preocupação de deployment.** O cookbook entrega apenas CSVs. A equipe contratante transforma para `.xlsx` com a estrutura de workbook em [`societario/skills/tabular-review/references/excel-output.md`](../../societario/skills/tabular-review/references/excel-output.md) — colunas `_source` ocultas, comentários de célula carregando a citação no hover, fills baseados em estado, dropdown `Verified` por coluna, planilhas `_schema` e `_summary`. Essa transformação acontece na superfície Excel da equipe contratante (Claude in Excel, openpyxl, ou Google Sheets via Sheets API). Enviar o xlsx do agente headless exigiria um runtime confiável e uma superfície de macro que este cookbook deliberadamente não assume.

**Não garantido:** cada célula que este agente produz é uma **pista que precisa de verificação**, não uma conclusão. O(a) revisor(a) lê a fonte, checa a citação, marca a coluna `Verified`. Um(a) advogado(a) inscrito(a) na OAB decide o que vai para uma declaração e garantia, um quadro de exceções, ou um memorando. Todo o conteúdo gerado é **rascunho para revisão por advogado(a) habilitado(a) na OAB** — não é parecer jurídico.

## Notas de adaptação

- **URL do VDR.** Configure `BOX_MCP_URL` / `GDRIVE_MCP_URL` / `IMANAGE_MCP_URL` para casar com seu data room. O default habilita Box e Google Drive; alterne o `default_config` em [`agent.yaml`](./agent.yaml) se rodar iManage ou Datasite como primário. Se seu VDR é Intralinks ou Datasite, adicione uma entrada em `mcp_servers` e `tools` com a URL de MCP correspondente.
- **Schema de colunas.** O padrão de DD em M&A em [`societario/skills/tabular-review/references/ma-diligence-columns.md`](../../societario/skills/tabular-review/references/ma-diligence-columns.md) é o default. Customize por tipo de deal — tech/PI (INPI), saúde (ANVISA/ANS), imobiliário (RGI + certidão de objeto e pé), regulado financeiro (CVM/BCB), energia (ANEEL/ANP), telecom (ANATEL), recuperação judicial, ou setores sob CADE (threshold R$ 750mi + R$ 75mi — Lei 12.529/2011 + Portaria Interministerial 994/2012) — usando os adicionais naquela referência. Categorias típicas BR a cobrir: societário (Junta Comercial — JUCESP/JUCERJA/JUCEMG etc., alterações de contrato social / estatuto social, atas), trabalhista (CLT + previdenciário + e-Social, CCT/ACT, reclamatórias TRT/TST, pejotização), tributário (RFB federal + ICMS estadual + ISS municipal + CARF + Lei 14.596/2023 transfer pricing), ambiental (Lei 6.938/81, licenças LP/LI/LO, IBAMA + CETESB/INEA), regulatório setorial (CVM/BCB/ANS/ANVISA/ANEEL/ANP/ANATEL/CADE), PI (INPI — marcas, patentes, software, Lei 9.279/1996 + Lei 9.610/1998 + Lei 9.609/1998), LGPD (Lei 13.709/2018 — bases legais, RIPD, encarregado/DPO, incidentes ANPD), anticorrupção (Lei 12.846/2013 + Decreto 11.129/2022 + Lei 8.429/1992 improbidade), litígios (PJe / e-SAJ / e-Proc / Projudi / DataJud / JusBrasil / Escavador — TJs, TRTs, TRFs, STJ, STF), imobiliário (RGI, certidão vintenária), contratos (declarações e garantias / W&I ainda nascente no mercado BR). Documentos típicos em data room BR: contrato social / estatuto social, ata de Assembleia Geral, certidões negativas (RFB, FGTS, trabalhista, criminal), certidão de objeto e pé, alvará de funcionamento, licenças ambientais (LP/LI/LO), CCT/ACT, demonstrações financeiras (BR-GAAP / CPC), parecer de auditoria independente.
- **Destino de output.** Outputs aterrissam em `./out/`. Conecte-os à pasta do deal, Google Drive, workspace iManage, ou pasta Box através do seu pipeline de deploy. Não dê ao `grid-writer` um MCP para subir os arquivos; um handoff para sua etapa de upload é mais limpo e mantém a camada de Write isolada.
- **Modo default.** Watch vs grid é selecionado por evento de steering. Se seu workflow é quase sempre um ou outro, semeie o template de evento de steering no seu orquestrador conforme.
- **Categorias da request-list.** O modo watch classifica contra as categorias na configuração `CLAUDE.md` do `societario` da equipe contratante. Rode novamente `/societario:cold-start-interview` lá antes de plugar o modo watch em um deal ativo.
- **Cabeçalho de material de trabalho.** O `grid-writer` prepende o cabeçalho da configuração `## Outputs` da equipe contratante. Confirme o cabeçalho com sua equipe jurídica antes do deploy — ele difere por papel do(a) revisor(a) (advogado(a) inscrito(a) na OAB vs estagiário/paralegal). Cabeçalho padrão recomendado: `MATERIAL DE TRABALHO — PRIVILEGIADO E CONFIDENCIAL (Art. 7º, XIX, EOAB)`.
- **Roteamento Slack.** Este agente nunca posta diretamente. Relatórios são arquivos; um `handoff_request` diz ao seu orquestrador para qual canal rotear. Configure o canal do deal na seção House style do `CLAUDE.md` da equipe contratante.
