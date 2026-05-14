# Mapeamento de Terminologia Jurídica: EUA → Brasil

Guia de equivalências usado para portar os plugins `claude-for-legal` (jurisdição EUA) para o contexto brasileiro. Não é uma tradução 1:1 — em muitos casos o instituto americano não tem equivalente perfeito e a adaptação faz uma escolha funcional.

Quando estiver adaptando uma skill, use este documento como referência canônica. Onde a equivalência for imperfeita, **sinalize na própria skill** ("equivalente funcional", "não há análogo direto no Brasil — abordagem adotada: …").

> **Aviso permanente.** Todo o conteúdo gerado pelos plugins é **rascunho para revisão por advogado(a) habilitado(a) na OAB** — não é parecer jurídico, não é conclusão legal, não substitui profissional. Cabe ao advogado revisar, validar e assumir responsabilidade profissional pela peça final.

---

## Privacidade e Proteção de Dados

| EUA | Brasil |
|---|---|
| GDPR / CCPA / CPRA | **LGPD** — Lei nº 13.709/2018 |
| Data Subject Access Request (DSAR) | Requisição de Titular / Pedido de exercício de direitos (LGPD art. 18) |
| Privacy Impact Assessment (PIA) | **RIPD** — Relatório de Impacto à Proteção de Dados Pessoais (LGPD art. 38) |
| Data Protection Impact Assessment (DPIA) | RIPD (mesmo instrumento; obrigatório quando ANPD requerer) |
| Data Processing Agreement (DPA) | Contrato de tratamento de dados / acordo controlador-operador |
| Controller / Processor | **Controlador / Operador** (LGPD art. 5º, VI e VII) |
| Data Protection Officer (DPO) | **Encarregado(a) pelo Tratamento de Dados** (LGPD art. 41) |
| Lawful basis | **Base legal** (LGPD art. 7º para dados comuns; art. 11 para sensíveis) |
| Data breach notification | Comunicação de incidente à ANPD e aos titulares (LGPD art. 48; Resolução CD/ANPD nº 15/2024) |
| Supervisory authority | **ANPD** — Autoridade Nacional de Proteção de Dados |
| SCC / Standard Contractual Clauses | Cláusulas-padrão da ANPD para transferência internacional (Resolução CD/ANPD nº 19/2024) |
| Adequacy decision | Decisão de adequação da ANPD |
| Right to be forgotten | Direito de eliminação (LGPD art. 18, VI) |

---

## Trabalhista e Emprego

| EUA | Brasil |
|---|---|
| At-will employment | **Não existe.** Contrato por prazo indeterminado é a regra; rescisão sem justa causa exige aviso prévio + verbas + FGTS + multa 40% |
| FLSA (wage & hour) | **CLT** Decreto-Lei 5.452/1943 (arts. 58–75 jornada; arts. 457–467 remuneração) + Reforma 13.467/2017 |
| FMLA (family medical leave) | Licença-maternidade (CLT art. 392; 120 dias / 180 Empresa Cidadã); licença-paternidade (CF art. 7º XIX; 5 dias / 20 Empresa Cidadã); auxílio-doença INSS após 15º dia (Lei 8.213/91) |
| CFRA / state PFL | Não há análogo estadual; benefícios são federais via INSS |
| ADA (disability) | Lei 13.146/2015 (Estatuto da PCD); cotas Lei 8.213/91 art. 93 |
| ADEA (age discrimination) | CF art. 7º XXX; Lei 9.029/1995 (proibição de práticas discriminatórias) |
| Title VII | CF arts. 5º e 7º; Lei 9.029/1995; Lei 7.716/1989 (racismo); CLT art. 461 (isonomia salarial) |
| OSHA | **NRs** (Normas Regulamentadoras MTE); CLT arts. 154–201; CIPA (NR-5) |
| Worker classification (1099/W-2) | **PJ vs CLT**; Lei 13.429/2017 (terceirização) e Lei 13.467/2017; pejotização — riscos do art. 9º CLT e Súmula 331 TST |
| Non-compete / non-solicit | Cláusula de não-concorrência (CC art. 422; jurisprudência TST exige limite temporal, geográfico e indenização) |
| Severance | Verbas rescisórias (aviso prévio, 13º proporcional, férias + 1/3, FGTS + multa 40%, multa art. 477 §8º se atrasar) |
| Background check | Consulta a antecedentes — limitada pela Súmula 568 TST (vínculo + função) |
| EEOC | **MPT** — Ministério Público do Trabalho; **TRT/TST** para contencioso |
| EEO-1 report | RAIS / e-Social; relatório de transparência salarial Lei 14.611/2023 |
| Restrictive covenants | Cláusulas restritivas (sigilo, não-concorrência, não-aliciamento) — exigem proporcionalidade e contrapartida |

---

## Societário e M&A

| EUA | Brasil |
|---|---|
| Corporation / Inc. / LLC | **S.A.** (Lei 6.404/1976) / **Ltda.** (CC arts. 1.052–1.087) / EIRELI extinta pela Lei 14.195/2021; agora SLU (sociedade limitada unipessoal) |
| Delaware General Corporation Law | Lei 6.404/1976 (S.A.) e Código Civil (Ltda.) |
| Bylaws / Articles of Incorporation | **Estatuto Social** (S.A.) / **Contrato Social** (Ltda.) |
| Board of Directors | **Conselho de Administração** (obrigatório em S.A. de capital aberto e de capital autorizado; facultativo nas demais — Lei 6.404 art. 138) |
| Officers | **Diretoria** (Lei 6.404 art. 143) |
| Shareholder meeting | **Assembleia Geral** (AGO/AGE) — Lei 6.404 arts. 121–137 |
| Unanimous Written Consent | **Deliberação por escrito** (Ltda. — CC art. 1.072 §3º; S.A. fechada — Lei 6.404 art. 121 par. único, hipóteses limitadas) |
| Form D / blue sky filings | Registro CVM; Resolução CVM 160 (ofertas públicas); ICVM 476 substituída pela RCVM 160 |
| SEC | **CVM** — Comissão de Valores Mobiliários |
| 10-K / 10-Q / 8-K | DFP / ITR / Fato Relevante (Resoluções CVM 80 e 44) |
| Material adverse change (MAC) | Cláusula de efeito adverso relevante (uso contratual; jurisprudência STJ ainda escassa) |
| Disclosure schedule | Anexos de declarações e garantias / quadro de exceções |
| Closing / Closing checklist | Fechamento; checklist de condições precedentes |
| Reps & warranties / W&I insurance | Declarações e garantias; seguro de R&W disponível no mercado BR |
| Secretary of State filing | **Junta Comercial** estadual (registro e arquivamento de atos) |
| EIN | **CNPJ** (Receita Federal) |
| Stock option plan | Plano de outorga de opções — discussão tributária e trabalhista relevante (Lei 14.789/2023; ainda há litígio sobre natureza) |
| Foreign investment | Registro RDE-IED no SISBACEN (Lei 4.131/1962 e Resoluções BCB) |

---

## Propriedade Intelectual

| EUA | Brasil |
|---|---|
| USPTO | **INPI** — Instituto Nacional da Propriedade Industrial |
| Lanham Act | **Lei 9.279/1996** (Lei da Propriedade Industrial) |
| US Copyright Act | **Lei 9.610/1998** (Lei de Direitos Autorais) |
| Software copyright (Computer Software Copyright Act) | **Lei 9.609/1998** (Lei do Software) |
| DMCA Section 512 takedown | **Marco Civil da Internet** (Lei 12.965/2014, arts. 19 e 21) — Brasil exige **ordem judicial** para remoção, salvo nudez não consentida (notice-and-takedown extrajudicial) |
| DMCA counter-notice | Não há análogo formal; pedido de reconsideração + ação judicial |
| Patent | Patente de invenção / modelo de utilidade (LPI arts. 6º–93º) |
| Trademark / Service mark | Marca (LPI arts. 122–175) — classificação NCL/Nice |
| Trade dress | Conjunto-imagem (jurisprudência STJ; LPI art. 195, III) |
| Copyright registration (US) | Registro de obra na Biblioteca Nacional, INPI (programas de computador) ou EBA — facultativo, prova de anterioridade |
| Cease & desist | Notificação extrajudicial (cartório de títulos e documentos para data certa) |
| FTO (freedom to operate) | Análise de liberdade de operação (sem nome consagrado; usa-se "FTO" mesmo) |
| Trade secret | **Segredo de empresa** (LPI art. 195, XI e XII) |
| Madrid Protocol | Brasil aderiu em 2019 — Decreto 10.033/2019 |

---

## Litígio / Processo

| EUA | Brasil |
|---|---|
| FRCP (Federal Rules of Civil Procedure) | **CPC** — Lei 13.105/2015 (Código de Processo Civil) |
| FRE (Federal Rules of Evidence) | CPC arts. 369–484 (provas); não há código autônomo de evidências |
| FRCrP | **CPP** — Decreto-Lei 3.689/1941 |
| Complaint / Answer | Petição inicial / Contestação |
| Summary judgment | Julgamento antecipado (CPC art. 355) |
| Motion to dismiss | Preliminares na contestação (CPC art. 337); indeferimento liminar (CPC art. 332) |
| Deposition | Depoimento pessoal / oitiva de testemunha em audiência; produção antecipada de provas (CPC art. 381) |
| Discovery | Não há "discovery" amplo; provas se requerem na inicial/contestação; produção antecipada (CPC art. 381); exibição (CPC arts. 396–404) |
| Subpoena | Intimação / requisição judicial; mandado de notificação |
| Privilege log | Não há instituto análogo; sigilo profissional do advogado (EOAB art. 7º XIX; CPC art. 388) |
| Legal hold | Dever de preservação documental (CC arts. 186 e 927; risco probatório CPC art. 400) |
| Motion in limine | Questão de ordem; requerimento prévio em audiência |
| Demand letter (FRE 408 protection) | Notificação extrajudicial — sigilo de tratativas conciliatórias (CPC art. 166 §3º; protege apenas mediação/conciliação) |
| Settlement | Acordo / transação (CC arts. 840–850; CPC art. 487, III, b) |
| Class action | **Ação Civil Pública** (Lei 7.347/1985) e **ações coletivas** (CDC arts. 81–104) — legitimação restrita (MP, Defensoria, associações) |
| Docket | Andamento processual (PJe, e-SAJ, e-Proc, Projudi — varia por tribunal) |
| Court of Appeals / Supreme Court | TJ (estadual) / TRF (federal) / **STJ** (uniformização de lei federal) / **STF** (constitucional) |
| Bench trial vs jury trial | Júri restrito a crimes dolosos contra a vida (CF art. 5º XXXVIII); cível sempre togado |

---

## Contratos / Comercial

| EUA | Brasil |
|---|---|
| MSA + SOW | Contrato-quadro + Ordem de Serviço / Anexo |
| Choice of law (NY/DE law) | **Lei aplicável** — em contratos internos, regra de não-derrogabilidade (LINDB art. 9º); contratos internacionais admitem eleição (jurisprudência STJ + Convenção do México não ratificada) |
| Forum selection | **Eleição de foro** (CPC art. 63) — restrita em contratos de adesão e consumo |
| Arbitration clause | **Cláusula compromissória** (Lei 9.307/1996 — Lei de Arbitragem) |
| AAA / JAMS | CAM-CCBC, CAM B3, CBMA, CMA, FGV (câmaras brasileiras) |
| UCC (Uniform Commercial Code) | Não existe; **Código Civil** Livro II (Obrigações) e Livro III (Empresa) — Lei 10.406/2002 |
| Indemnification | Indenização / cláusula de indenidade — limite art. 944 CC; cap admitido salvo dolo |
| Limitation of liability | Cláusula limitativa de responsabilidade — nula em consumo (CDC art. 51, I) e em transporte de pessoas (Súmula 161 STF) |
| Force majeure | **Caso fortuito e força maior** (CC art. 393) |
| Liquidated damages | **Cláusula penal** (CC arts. 408–416) — limite: valor da obrigação principal |
| Hardship / MAC | Onerosidade excessiva / imprevisão (CC arts. 478–480) |
| Assignment | Cessão de posição contratual (CC arts. 286–298 cessão de crédito; assunção de dívida arts. 299–303) |
| Governing language | Português obrigatório se assinado no Brasil para registro/eficácia perante terceiros (Decreto 13.609/1943 arts. 14 e 18 para tradução juramentada) |
| Electronic signature | **MP 2.200-2/2001** (ICP-Brasil) + Lei 14.063/2020; aceita-se assinatura eletrônica simples/avançada/qualificada |
| Notarization | **Cartório de Notas** — reconhecimento de firma; e-Notariado (Provimento CNJ 100/2020) |

---

## Consumidor / Produto

| EUA | Brasil |
|---|---|
| FTC Act (deceptive/unfair) | **CDC** — Lei 8.078/1990 (arts. 30–38 publicidade; arts. 12–27 responsabilidade) |
| State consumer protection acts | CDC é federal e único |
| Class action waiver in arbitration | Inadmissível em consumo (CDC art. 51, VII; STJ REsp 1.169.841) |
| FDA / CPSC | **ANVISA** (saúde) / **INMETRO** (produtos) / **Senacon** (consumidor) / **Procons** estaduais |
| Recall | Recall regulado pela Portaria SENACON 618/2019; comunicação obrigatória (CDC art. 10 §1º) |
| Advertising standards self-reg | **CONAR** (Conselho Nacional de Autorregulamentação Publicitária) |
| False advertising | Publicidade enganosa ou abusiva (CDC arts. 36–38) |

---

## Regulatório

| EUA | Brasil |
|---|---|
| Federal Register | **DOU** — Diário Oficial da União |
| NPRM (Notice of Proposed Rulemaking) | **Consulta pública** / **Tomada de Subsídios** (Lei 9.784/99; Decreto 10.411/2020 AIR) |
| Agency comment period | Período de consulta pública (varia por agência) |
| FCC | **ANATEL** |
| FDA | **ANVISA** |
| FERC | **ANEEL** |
| Federal Reserve / OCC | **BCB** (Banco Central do Brasil) |
| SEC | **CVM** |
| FTC | **CADE** (concorrência) / **SENACON** (consumo) |
| ITC | **CAMEX** / SECEX (defesa comercial) |
| EPA | **IBAMA** / **ICMBio** / órgãos estaduais (CETESB/INEA/etc.) |
| OFAC sanctions | Não há sistema BR equivalente; aplica-se OFAC indireto + sanções ONU internalizadas; **COAF** (PLD/CFT) |

---

## IA / Governança

| EUA | Brasil |
|---|---|
| AI Bill of Rights (White House) | **PL 2338/2023** — Marco Regulatório da IA (em tramitação, aprovado no Senado em 2024) |
| NIST AI RMF | Sem framework BR oficial; ANPD publicou "Estudo Preliminar IA + LGPD" (2024) |
| EU AI Act (referência usada nos EUA) | Vide PL 2338/2023; também serve de referência subsidiária |
| Algorithmic accountability | LGPD art. 20 (decisões automatizadas + direito à revisão) |

---

## Acadêmico / Clínicas

| EUA | Brasil |
|---|---|
| ABA Formal Opinion 512 (AI use by lawyers) | **Provimento CFOAB nº 205/2021** (publicidade) + Provimento 218/2023 (uso responsável de IA); CED-OAB; **Resolução CNJ 332/2020** + 615/2025 (uso de IA pelo Judiciário) |
| Bar admission | Exame da Ordem (OAB); inscrição na seccional |
| MPRE / MBE / MEE | Exame de Ordem (1ª e 2ª fase) |
| Clinic / Pro bono | **NPJ** (Núcleo de Prática Jurídica) — Resolução CNE/CES 5/2018; Provimento CFOAB 166/2015 (advocacia voluntária); EOAB art. 30 (estagiários) |
| Casebook / Socratic | Manual / Tratado; método varia |
| IRAC | Mesmo (Issue/Rule/Application/Conclusion); peça-padrão brasileira: relatório + fundamentos + dispositivo (CF art. 93 IX) |

---

## Anti-corrupção / Compliance

| EUA | Brasil |
|---|---|
| FCPA | **Lei Anticorrupção** — Lei 12.846/2013 + Decreto 11.129/2022; **Lei de Improbidade** 8.429/1992 (alterada pela 14.230/2021) |
| DOJ FCPA Resource Guide | Manual CGU/AGU; Decreto 11.129/2022 (programa de integridade) |
| Whistleblower | Lei 13.608/2018; Decreto 10.153/2019 (proteção ao denunciante) |
| OFAC / sanctions screening | COAF (PLD/FT); Resolução COAF 36/2021; due diligence de terceiros |
| Sarbanes-Oxley | Lei 6.404 + ICVM 480/RCVM 80 (controles internos); Lei 12.683/2012 (PLD) |

---

## Tributário (referencial — não há plugin tax dedicado)

| EUA | Brasil |
|---|---|
| IRS | **Receita Federal do Brasil (RFB)** |
| 1099 / W-2 / W-9 | Não há equivalente direto; informações via DIRF, e-Social, EFD-Reinf |
| State sales tax | **ICMS** (estadual) / **ISS** (municipal) / **PIS/COFINS** (federal) — reforma EC 132/2023 cria IBS+CBS+IS |

---

## Notas de aplicação

1. **Nunca traduza o nome de uma lei brasileira**: cite no formato oficial — "Lei 13.709/2018 (LGPD)", "CLT (Decreto-Lei 5.452/1943)", "CC (Lei 10.406/2002)".
2. **Jurisprudência**: prefira citar **STF**, **STJ**, **TST** (uniformização) sobre TJ/TRT/TRF (não-vinculante salvo IRDR/IAC).
3. **Súmulas vinculantes** (STF) e **Temas de Repercussão Geral** (STF) / **Temas Repetitivos** (STJ) têm efeito processual diferenciado — sinalize quando aplicável.
4. **Recurso a precedentes**: o sistema brasileiro mistura civil law + precedentes qualificados (CPC arts. 926–928); não trate jurisprudência ordinária como binding precedent.
5. **Federalismo limitado**: matéria civil, processual civil, penal, processual penal, trabalho, comercial é **competência federal** (CF art. 22). Estados legislam sobre tributário-estadual, ambiental concorrente, etc. Não invente "lei estadual" onde a competência é da União.
6. **OAB**: toda peça/parecer é responsabilidade do(a) advogado(a) — preserve a guardrail "rascunho para revisão por advogado(a) inscrito(a) na OAB".
