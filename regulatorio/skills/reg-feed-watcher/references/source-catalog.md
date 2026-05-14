# Catálogo de Fontes Regulatórias — Brasil

> **Adaptação BR não oficial.** Catálogo inicial para o reg-feed-watcher. A cold-start interview configura quais fontes monitorar; este catálogo provê as opções. URLs verificadas em **maio 2026** — feeds mudam; verifique se uma fonte deixar de retornar resultados.

**Como ler este catálogo:**
- **Formato** — o que o feed retorna: JSON API (estruturado, melhor), RSS/Atom (semi-estruturado, bom), página HTML (precisa de scraping ou change detection), Email apenas (exige Gmail/Outlook MCP).
- **Tier** — *Primária* significa o(a) próprio(a) regulador(a); *Secundária* significa comentarista, agregador, ou escritório resumindo fontes primárias. Sempre trace fonte secundária ao primário antes de tratar como autoritativo.
- **Auth** — None significa aberto; Key significa chave de API gratuita-mas-registrada; Paid significa assinatura.
- **Notas** — qualquer pegadinha (rate limits, retirada de feed, passos de discovery).

Fontes marcadas ⚠️ foram reportadas por usuários ou reguladores como não confiáveis ou descontinuadas — verifique antes de configurar.

---

## Federal — Primária

| Fonte | URL de feed | Formato | Cobre | Auth | Notas |
|---|---|---|---|---|---|
| DOU (Diário Oficial da União / Imprensa Nacional) | `https://www.in.gov.br/leiturajornal` | HTML + busca | Todos os atos normativos federais (leis, decretos, portarias, resoluções, instruções normativas) | None | Filtre por seção (1: atos oficiais e normativos; 2: militares; 3: contratos e editais; extra: atos especiais), data, palavra-chave. API documentada em desenvolvedor.in.gov.br (varia). **Use primeiro** — maioria dos atos federais flui aqui. |
| Planalto (legislação federal consolidada) | `https://www4.planalto.gov.br/legislacao` | HTML | Leis, decretos, e medidas provisórias federais | None | Útil para texto consolidado. |
| Senado Federal — Atividade Legislativa | `https://legis.senado.leg.br/dadosabertos/` | API JSON | PLs em tramitação no Senado, comissões, votações | None | Dados abertos do Senado. |
| Câmara dos Deputados — Dados Abertos | `https://dadosabertos.camara.leg.br/swagger/api.html` | API JSON | PLs em tramitação na Câmara | None | Bem documentada. |
| Congresso Nacional — LexML | `https://www.lexml.gov.br/busca/feed` | RSS/HTML | Leis federais e estaduais, atos normativos | None | Buscador transversal. |
| ANPD — Notícias e Normas | `https://www.gov.br/anpd/pt-br` | HTML + RSS quando disponível | LGPD, resoluções CD/ANPD, consultas públicas, atos de fiscalização | None | Página de "notícias" e de "normas e regulamentos" no portal gov.br/anpd. Suba o "Consulta Pública" se aberta. |
| CVM — Atos Normativos | `https://conteudo.cvm.gov.br/legislacao` | HTML + RSS por categoria | Resoluções CVM, instruções, deliberações, ofícios-circulares; PAS, termos de compromisso (Lei 6.385 art. 11 §5º) | None | RSS para Resoluções (RCVM 80, 160, etc.). Consultas públicas em página dedicada. |
| BCB — Normativos | `https://www.bcb.gov.br/estabilidadefinanceira/buscanormas` | HTML + busca | Resoluções, circulares, comunicados, instruções normativas BCB; Open Finance (Resolução Conjunta 1/2020); PIX (DICT, regulamento) | None | A busca é a porta principal. Diário do BCB também publica. |
| ANVISA — Atos Normativos | `https://www.gov.br/anvisa/pt-br/assuntos/regulamentacao` | HTML | RDC, IN, RE, consultas públicas, alertas; cosméticos, medicamentos, alimentos, dispositivos | None | Sub-área "Consultas Públicas". |
| ANATEL — Atos | `https://www.gov.br/anatel/pt-br/legislacao` | HTML | Resoluções, regulamentos, consultas públicas, atos de outorga | None | Marco Civil da Internet (Lei 12.965/2014) embora regulamentação seja MJ/SENACON. |
| ANS — Normas | `https://www.gov.br/ans/pt-br/acesso-a-informacao/legislacao` | HTML | RN, súmulas normativas, OPS, ROL de procedimentos | None | Sub-área "Consultas Públicas". |
| ANEEL — Atos Regulatórios | `https://www.gov.br/aneel/pt-br/acesso-a-informacao/legislacao` | HTML | Resoluções, despachos, atos, consultas públicas; tarifa, geração distribuída | None | |
| ANP — Legislação | `https://www.gov.br/anp/pt-br/acesso-a-informacao/legislacao` | HTML | Resoluções ANP, contratos, leilões | None | |
| ANTT — Atos | `https://www.gov.br/antt/pt-br/acesso-a-informacao/legislacao` | HTML | Resoluções, deliberações, transporte rodoviário/ferroviário, marco saneamento (Lei 14.026/2020) por interface intersetorial | None | |
| ANAC — Atos Regulatórios | `https://www.gov.br/anac/pt-br/assuntos/regulados` | HTML | RBAC, IS, decisões, consultas | None | |
| IBAMA — Atos | `https://www.gov.br/ibama/pt-br/acesso-a-informacao/legislacao` | HTML | IN IBAMA, autos de infração, licenciamento ambiental | None | Concorrente com órgãos estaduais (CETESB-SP, INEA-RJ, FEAM-MG etc.). |
| ICMBio | `https://www.gov.br/icmbio/pt-br` | HTML | Unidades de conservação, autuações | None | |
| CADE — Decisões e Atos | `https://www.gov.br/cade/pt-br/centrais-de-conteudo` | HTML | Acórdãos, atos de concentração, sanções, acordos de leniência (Lei 12.529 art. 86), TCCs | None | "Sancionados" e "Atos de Concentração" são as áreas mais consultadas. |
| SENACON / Procons | `https://www.gov.br/mj/pt-br/assuntos/seus-direitos/consumidor` | HTML | Portarias SENACON, jurisprudência consumerista, sistema SINDEC | None | Procons estaduais em sites próprios (procon.sp.gov.br, etc.). |
| COAF | `https://www.gov.br/coaf/pt-br` | HTML | Resoluções, comunicações; PLD/FT (Lei 9.613 + 12.683); Circular BCB 3.978 | None | |
| Receita Federal | `https://www.gov.br/receitafederal/pt-br/acesso-a-informacao/legislacao` | HTML | IN RFB, ADE, soluções de consulta | None | |
| CGU | `https://www.gov.br/cgu/pt-br/acesso-a-informacao/legislacao` | HTML | Anticorrupção, Dec. 11.129/2022 (programa de integridade) | None | |
| MTE / Normas Regulamentadoras | `https://www.gov.br/trabalho-e-emprego/pt-br/acesso-a-informacao/participacao-social/consultas-publicas` | HTML | Portarias, NRs, CCT/ACT | None | |
| ICMBio, FUNAI, ANS, ANCINE | gov.br/<sigla> | HTML | Várias | None | |

---

## Jurisprudência regulatória — Primária

| Fonte | URL | Formato | Cobre | Auth | Notas |
|---|---|---|---|---|---|
| STF | `https://portal.stf.jus.br/jurisprudencia/` | HTML + busca + RSS por tema | ADI, ADC, RE, ARE, decisões monocráticas, súmulas vinculantes, Temas de Repercussão Geral | None | Súmulas Vinculantes e Temas de Repercussão Geral têm efeito vinculante. |
| STJ | `https://www.stj.jus.br/sites/portalp/Inicio` | HTML + busca | Recurso especial, agravo, súmulas, Temas Repetitivos | None | Temas Repetitivos vinculam tribunais inferiores. |
| TST | `https://www.tst.jus.br/` | HTML | Súmulas, orientações jurisprudenciais, RR | None | Útil para temas trabalhistas em interseção regulatória. |
| TCU | `https://portal.tcu.gov.br/jurisprudencia` | HTML + Pesquisa Integrada | Acórdãos, súmulas, jurisprudência selecionada | None | Decisões TCU afetam licitações, concessões, regulação econômica. |
| CARF | `https://carf.fazenda.gov.br/sincon/public/pages/ConsultarJurisprudencia/consultarJurisprudenciaCarf.jsf` | HTML | Acórdãos do Conselho Administrativo de Recursos Fiscais | None | Relevante para temas tributários. |
| CNJ | `https://www.cnj.jus.br/atos-normativos/` | HTML | Resoluções CNJ, recomendações; Resolução CNJ 332/2020 + 615/2025 (IA no Judiciário); Provimento 100/2020 (e-notariado) | None | |

---

## Estadual — Primária

Cobertura é desigual. Estados com fiscalização ativa em consumo, ambiente e dados priorizados aqui. Muitos órgãos estaduais publicam apenas em HTML — se sem RSS, configure como "manual" ou setup change detection.

| Fonte | URL | Formato | Cobre | Auth | Notas |
|---|---|---|---|---|---|
| MP-SP (Ministério Público de São Paulo) | `https://www.mpsp.mp.br/portal/page/portal/Inicio` | HTML | TACs, inquéritos civis, recomendações | None | TAC é o equivalente funcional do consent decree americano. |
| MP-RJ | `https://www.mprj.mp.br/` | HTML | Mesmo | None | |
| MP-MG | `https://www.mpmg.mp.br/` | HTML | Mesmo | None | |
| Procon-SP | `https://www.procon.sp.gov.br/` | HTML | Autuações, ranking de empresas reclamadas, atos consumeristas | None | |
| CETESB (SP) | `https://cetesb.sp.gov.br/` | HTML | Licenciamento ambiental, autos de infração SP | None | Concorrente com IBAMA (CF art. 24). |
| INEA (RJ) | `https://www.inea.rj.gov.br/` | HTML | Mesmo, no RJ | None | |
| FEAM (MG) | `http://www.feam.br/` | HTML | Mesmo, em MG | None | |
| Juntas Comerciais (JUCESP, JUCERJA, etc.) | Sítios estaduais | HTML | Registro de atos societários | None | |
| Secretarias Estaduais da Fazenda | Sítios estaduais | HTML | ICMS, ISS (municipal); ECF/EFD | None | Reforma EC 132/2023 cria IBS+CBS+IS. |

---

## Autorregulação setorial

| Fonte | URL | Formato | Cobre | Auth | Notas |
|---|---|---|---|---|---|
| ANBIMA | `https://www.anbima.com.br/pt_br/regular.htm` | HTML | Códigos ANBIMA (distribuição, gestão, ofertas) | None | Autorregulação do mercado de capitais; código vinculante por adesão. |
| B3 | `https://www.b3.com.br/pt_br/regulacao/regulacao/` | HTML | Regulamento de listagem, sanções, regras de pregão | None | |
| CONAR | `http://www.conar.org.br/` | HTML | Decisões em publicidade enganosa/abusiva (CDC arts. 36–38) | None | |
| FEBRABAN | `https://portal.febraban.org.br/` | HTML | Autorregulação bancária | None | |

---

## Internacional (referência subsidiária para empresas multinacionais)

| Fonte | URL | Formato | Cobre | Auth | Notas |
|---|---|---|---|---|---|
| EUR-Lex (OJ) | `https://eur-lex.europa.eu/` | Webservice + RSS por busca | GDPR, DSA, DMA, AI Act, demais EU regulations e directives | Key (free) | Para empresas com operação UE/UK. |
| EDPB News | `https://www.edpb.europa.eu/news/news_en` | RSS | Guidelines, decisões vinculantes, sumários de fiscalização | None | |
| OECD AI Policy Observatory | `https://oecd.ai/en/` | HTML + newsletter | Políticas nacionais de IA, guidance OECD | None | |

---

## Secundárias / Agregadores

**Trate conteúdo destas fontes como pistas, não autoridade.** Fonte secundária dizendo "a ANPD publicou X" significa: ache X em gov.br/anpd, depois confie. Tag itens puxados como `[secondary source]` no digest.

| Fonte | URL | Formato | Cobre | Auth | Notas |
|---|---|---|---|---|---|
| Migalhas | `https://www.migalhas.com.br/` | RSS / Email | Notícia jurídica geral, jurisprudência selecionada | None | |
| JOTA | `https://www.jota.info/` | RSS / Paywall parcial | Cobertura regulatória aprofundada (tributário, antitruste, consumo, dados) | None / Paid | |
| Conjur | `https://www.conjur.com.br/` | RSS | Jurisprudência STF/STJ, comentários | None | |
| Valor Econômico — Legislação | `https://valor.globo.com/legislacao/` | RSS / Paywall | Cobertura econômica e regulatória | Paid | |
| Folha de S.Paulo — Painel S.A. | `https://www1.folha.uol.com.br/colunas/painelsa/` | RSS / Paywall | Antitruste, mercado de capitais | Paid | |
| Estadão — Broadcast | `https://www.estadao.com.br/` | RSS / Paywall | Mercado, regulação | Paid | |
| Mattos Filho — Insights | `https://www.mattosfilho.com.br/` | HTML / Email | Alertas regulatórios | None | |
| Pinheiro Neto — Insights | `https://www.pinheironeto.com.br/` | HTML / Email | Mesmo | None | |
| Machado Meyer — Insights | `https://www.machadomeyer.com.br/pt/inteligencia-juridica` | HTML / Email | Mesmo | None | |
| Veirano — Newsletter | `https://www.veirano.com.br/` | HTML / Email | Mesmo | None | |
| Demarest — Newsletter | `https://www.demarest.com.br/` | HTML / Email | Mesmo | None | |
| BMA — Newsletter | `https://www.bmalaw.com.br/` | HTML / Email | Mesmo | None | |
| TozziniFreire — Insights | `https://tozzinifreire.com.br/` | HTML / Email | Mesmo | None | |
| Lefosse | `https://lefosse.com/` | HTML / Email | Mesmo | None | |
| Stocche Forbes | `https://www.stoccheforbes.com.br/` | HTML / Email | Mesmo | None | |

---

## Fontes sem feeds (precisam de monitoramento web ou email)

Algumas fontes importantes não publicam feeds. Monitorá-las requer:
- Detecção de mudança em página web (não built-in)
- Forwarding de newsletter por email (exige integração Gmail/Outlook MCP)
- Checagem manual via caminho "manual entry" do reg-feed-watcher

| Fonte | URL | Notas |
|---|---|---|
| Maioria dos Procons estaduais | Sítios estaduais | Páginas HTML; sem RSS |
| Vários órgãos estaduais ambientais | Sítios estaduais | HTML apenas |
| Sítios de algumas Juntas Comerciais | Sítios estaduais | HTML apenas |

---

## Pacotes iniciais sugeridos

**Time in-house de privacidade (foco LGPD + multinacionais com UE):**
DOU (filtros ANPD, MJ), ANPD site, MP-SP, EDPB, EUR-Lex (GDPR/AI Act), JOTA, Migalhas, IAPP (referência subsidiária).

**Time in-house comercial/regulatório (amplo):**
DOU (todas as agências relevantes), CVM, BCB, CADE, ANATEL, ANS, MP-SP, STF (Temas de Repercussão Geral), STJ (Temas Repetitivos). Acrescente JOTA + Valor para cobertura agregada.

**Time de IA governance:**
DOU (filtro: ANPD, MCTI, MJ), ANPD (Estudo Preliminar IA + LGPD 2024), PL 2338/2023 (Câmara/Senado — Marco Regulatório de IA), CNJ (Resoluções 332/2020 + 615/2025 — IA no Judiciário), EU AI Act (EUR-Lex), OECD AI Observatory, JOTA, IAPP.

**Time de mercado de capitais:**
CVM, B3, ANBIMA, BCB, STJ (Temas Repetitivos em valores mobiliários), DOU filtros fazendários, JOTA, Valor.

**Time setor bancário/financeiro/fintech:**
BCB (Open Finance Resolução Conjunta 1/2020, PIX, Marco Cripto Lei 14.478), CVM, CADE, COAF (PLD/FT — Lei 9.613 + 12.683; Circular BCB 3.978), DOU, JOTA.

---

## Adicionando uma fonte

Para acrescentar fonte que não está neste catálogo:
1. Ache URL do feed (tente `/rss`, `/feed`, `/atom.xml`, ou veja o source da página por `<link rel="alternate" type="application/rss+xml">`).
2. Valide que retorna XML/JSON em browser ou com `curl`.
3. Adicione ao CLAUDE.md regulatorio do usuário em **Feed configuration → Direct regulator feeds**, com: nome da fonte, URL, formato, o que cobre.
4. Se sem feed, adicione em **Sources without feeds** e decida: manual, email, ou change detection.
