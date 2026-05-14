# Framework de Launch Review em Oito Categorias

Framework default quando a equipe não tem o seu próprio. Adaptado de prática brasileira de produto. Cada categoria tem uma pergunta-chave e uma condição de auto-skip.

As categorias são conceitos de enquadramento estáveis. O que conta como "Precisa trabalho" vs. "Bloqueante" *dentro* de uma categoria depende das jurisdições aplicáveis, regimes setoriais e da calibração da empresa em `~/.claude/plugins/config/claude-for-legal/produto/CLAUDE.md`. Pesquise os regimes regulatórios aplicáveis ao setor, público e jurisdições do produto antes de concluir que um padrão fático específico é ou não problema.

## 1. Compromissos contratuais

**Pergunta-chave:** Isso conflita com alguma promessa voltada ao cliente?

Cheque: Termos de Uso, SLAs, materiais de marketing, contratos com clientes (especialmente B2B / enterprise com termos customizados), documentação publicada. Em consumo (B2C), CDC art. 51 lista cláusulas abusivas nulas de pleno direito; CDC art. 30 vincula publicidade ao contrato.

**Auto-skip se:** Nenhuma mudança voltada ao cliente — ferramenta interna, infra, ou mudança invisível para usuários.

**Findings comuns:**
- Novo feature contradiz restrição dos Termos de Uso
- Implicações no SLA por nova dependência
- Feature anunciado como "incluído" migrando para tier pago — CDC art. 51 XIII (modificação unilateral)
- Mudança unilateral em Termos de Uso (CC art. 122 — vedada cláusula puramente potestativa; CDC art. 51 X-XI)

## 2. Privacidade (LGPD)

**Pergunta-chave:** Nova coleta de dado, nova finalidade ou novo compartilhamento?

Cheque: que dado é tocado, se é novo ou existente, se a finalidade está coberta pela Política de Privacidade atual, se algum novo terceiro vê o dado. Pesquise os regimes aplicáveis às jurisdições dos titulares afetados antes de concluir se nova informação, consentimento ou avaliação (RIPD — LGPD art. 38) é exigida.

Base legal aplicável (LGPD art. 7º para comuns; art. 11 para sensíveis): consentimento, execução de contrato, obrigação legal, legítimo interesse (com RIPD e balanceamento), proteção da vida, exercício regular de direitos, etc.

**Auto-skip se:** Sem mudança de dado — mudança pura de UI, infra pura sem novo logging.

**Findings comuns:** Ver skill PIA do plugin privacidade.

## 3. Segurança

**Pergunta-chave:** Nova superfície de ataque?

Cheque: novos endpoints, novo dado em repouso, novos caminhos de acesso, novos requisitos de autenticação. LGPD arts. 46–49 — medidas técnicas e administrativas; Resolução CD/ANPD 15/2024 — comunicação de incidentes.

**Auto-skip se:** Mudança só de UI, sem backend. (Mas cheque que é mesmo só UI — "mudança de UI" que adiciona nova chamada de API não é.)

**Não é decisão só do jurídico** — chame o time de segurança. O papel do jurídico é garantir que a revisão de segurança aconteceu e que findings foram endereçados.

## 4. Propriedade Intelectual

**Pergunta-chave:** Algum código, conteúdo de terceiro ou output potencialmente infringente?

Cheque: novas dependências open-source (compatibilidade de licença — algumas licenças copyleft criam obrigações inconsistentes com produto proprietário; pesquise a licença específica), conteúdo de terceiros (imagens de estoque, fontes, datasets), features que geram conteúdo que poderia infringir (geração por IA, uploads de usuário exibidos publicamente).

LPI (Lei 9.279/96) — marca, patente, conjunto-imagem; LDA (Lei 9.610/98) — direitos autorais; Lei do Software (Lei 9.609/98).

**Auto-skip se:** Sem novas dependências, sem geração de conteúdo, sem uploads de usuário.

**Findings comuns:**
- Licença copyleft em nova dependência
- Procedência de dado de treinamento incerta
- Conteúdo gerado por usuário sem processo de notificação — pesquise o regime aplicável (Marco Civil arts. 19 e 21; STJ Súmula 633 para marketplace)

## 5. Interações com terceiros

**Pergunta-chave:** Novo fornecedor, parceiro ou integração?

Cheque: existe contrato, existe acordo controlador-operador (LGPD art. 39) se há fluxo de dado, falha do terceiro vira nosso problema (uptime, segurança).

**Auto-skip se:** Sem novas partes externas.

**Findings comuns:**
- Novo fornecedor sem cláusula controlador-operador (LGPD art. 39)
- Parceiro de integração com práticas de dado diferentes
- Dependência de API sem SLA

## 6. Regulatório / setorial

**Pergunta-chave:** Isso toca setor, público ou jurisdição regulada?

Pesquise os regimes regulatórios aplicáveis ao setor do produto (por exemplo, saúde — ANVISA; financeiro — BCB/CVM; crianças e adolescentes — ECA + ANPD; seguros — SUSEP; telecom — ANATEL; trabalho — MTE; publicidade — CONAR + CDC), público e jurisdições (Brasil federal e estadual; também outras regiões se o produto alcança). Considere também regimes de acessibilidade (LBI Lei 13.146/2015 + Decreto 9.296/2018 + eMAG/WCAG) e controle de exportação onde relevante. Cite fontes primárias e verifique atualidade — setores e jurisdições regulados mudam frequentemente.

**Auto-skip se:** Mesmos usuários, mesmos setores, mesmas jurisdições que o produto existente — nada novo em escopo regulatório.

**Findings comuns:**
- Expansão para setor regulado sem infraestrutura de suporte (contratos, controles, divulgações) que o regime exige
- Feature que pode ser usado por público regulado (ex.: crianças) sem as proteções do regime aplicável (LGPD art. 14 + ECA + CONANDA Res. 163/2014)
- Expansão internacional para jurisdição com requisitos de localização, licenciamento ou notificação (transferência internacional Res. CD/ANPD 19/2024)

## 7. Claims de marketing

**Pergunta-chave:** Algum claim que precisa de substanciação?

Ver skill marketing-claims-review. CDC art. 36 §único — ônus da prova de quem patrocina; CONAR — Código + Anexos (publicidade infantil, apostas, criptoativos, IA generativa); Lei 9.294/96 — tabaco, álcool, medicamentos; CONANDA Res. 163/2014 — publicidade infantil é abusiva.

**Auto-skip se:** Sem componente de marketing — lançamento silencioso, feature interno, flag flip.

## 8. Governança de IA

**Pergunta-chave:** Usa IA em qualquer forma? O caso de uso está no registro? Avaliação de Impacto Algorítmico feita? Termos do fornecedor de IA revisados?

Cheque: modelos de terceiros, modelos internos, features de IA via fornecedor, scoring ou classificação automatizada, conteúdo generativo, recomendações, predições. Pesquise os regimes de governança de IA aplicáveis às jurisdições dos titulares afetados e ao tipo de caso de uso — regras específicas de IA estão evoluindo e variam substancialmente.

Referências BR: **PL 2338/2023** (Marco da IA — em tramitação; aprovado no Senado em 12/2024); **LGPD art. 20** (revisão de decisões automatizadas); **TSE Res. 23.610/2019** (uso em propaganda eleitoral; deepfake proibido); **Resoluções CNJ 332/2020 e 615/2025** (uso pelo Judiciário); **Provimento CFOAB 218/2023** (uso por advogados).

**Auto-skip se:** Sem componente de IA detectado.

**Findings comuns:**
- Caso de uso fora do registro de IA
- Termos do fornecedor de IA permitem treinamento sobre inputs
- Decisão automatizada sem desenho de human-in-the-loop onde pode ser exigido (LGPD art. 20)
