# Watch List — Privacidade e Proteção de Dados (Brasil)

**Última verificação: 2026-05-13.**

> **Checagem de obsolescência.** Se a data de última verificação acima tem mais de 90 dias, trate este arquivo como obsoleto e confirme cada item antes de confiar. Watch list desatualizada é pior do que nenhuma — parece atual mas está errada. Quando uma skill ler este arquivo, cheque primeiro a data. Se obsoleta, diga: "A watch list foi verificada pela última vez em [data] — [N] meses atrás. Estou usando-a como checklist de áreas a pesquisar, não como fonte de status atual." Quando atualizar qualquer item, atualize também a data no topo.

A regulação de privacidade no Brasil se movimenta rápido. Antes de confiar em data de vigência, threshold ou obrigação, verifique. Estas são as áreas com maior probabilidade de terem mudado desde o treinamento do modelo:

## LGPD (Lei 13.709/2018)

- **Lei principal.** Em vigor desde setembro/2020; sanções administrativas desde 01/08/2021 (Lei 14.010/2020 prorrogou). Em geral estável, mas alterações pontuais ocorrem por MP e lei ordinária — verificar texto consolidado no Planalto antes de citar dispositivo.
- **PLs em tramitação que podem alterar a LGPD:** acompanhar PL 2.338/2023 (Marco da IA) — toca o art. 20 (decisões automatizadas); outros PLs sobre dados de crianças, dados financeiros, neurodireitos. Confirme status atual no Congresso `[verificar]`.

## Resoluções CD/ANPD (regulamentação infralegal)

A ANPD publica regulamentos sob a forma de Resoluções do Conselho Diretor. As mais relevantes (em 2026-05):

- **Resolução CD/ANPD nº 2/2022** — aplicação da LGPD a agentes de tratamento de pequeno porte.
- **Resolução CD/ANPD nº 4/2023** — sanções administrativas (cálculo de multas; circunstâncias atenuantes/agravantes).
- **Resolução CD/ANPD nº 15/2024** — **comunicação de incidente de segurança** à ANPD e aos titulares (LGPD art. 48). Prazo: até 3 dias úteis da ciência do incidente para a ANPD. Substitui o entendimento anterior (que era prazo "razoável") — antes desta Resolução, o que valia era diferente. **Sempre verifique o texto atual.**
- **Resolução CD/ANPD nº 18/2024** — comunicação prévia de operações de tratamento e estudo de impacto (consulta pública sobre RIPD).
- **Resolução CD/ANPD nº 19/2024** — **transferência internacional de dados pessoais** (LGPD arts. 33-36). Define cláusulas-padrão da ANPD, regras para BCR e adequação. Substitui o regime de transição.

Antes de citar Resolução, verifique no site da ANPD (`gov.br/anpd`) se há texto atualizado ou nova Resolução. A ANPD revoga e republica com frequência.

## Sanções aplicadas pela ANPD

- A ANPD vem aplicando sanções de advertência, multa e publicização da infração desde 2023. Cheque o histórico de Processos Administrativos Sancionatórios no site da ANPD para tendência atual (PADs publicados, valores médios, principais infrações).
- **Tendências 2025-2026 a monitorar:** vazamentos de dados sem comunicação tempestiva; tratamento sem base legal de dados sensíveis; ausência de Encarregado(a); compartilhamento com operadores sem contrato de tratamento adequado; uso de dados para treinamento de IA sem base legal compatível.

## Jurisprudência LGPD — STJ e STF

- **STJ:** consolidação sobre dano moral por vazamento de dados (necessidade ou não de comprovação de dano efetivo). Acompanhar Temas Repetitivos e Recursos Especiais Repetitivos sobre LGPD.
- **STF:** ADI 6.387 (2020) — reconheceu proteção de dados como direito fundamental (depois inserido no art. 5º LXXIX CF pela EC 115/2022). Acompanhar ações sobre LGPD vs. competência federativa, LGPD vs. CDC, base legal de tratamento por entes públicos.

## Transferência internacional (LGPD arts. 33-36 + Resolução CD/ANPD 19/2024)

- A ANPD ainda não emitiu **decisão de adequação** para nenhum país específico em 2026-05 `[verificar]`. Verifique antes de afirmar que algum país é "adequado" para fins da LGPD.
- Cláusulas-padrão da ANPD: aprovadas pela Resolução 19/2024. Quando usar, confira a versão vigente.
- BCR (regras corporativas globais): mecanismo previsto, com regulamentação pela Resolução 19/2024.

## Tratamento de dados de crianças e adolescentes (LGPD art. 14)

- **Resolução CD/ANPD 18/2024 + Enunciado da ANPD sobre interpretação do art. 14** — interpretação do "melhor interesse" e bases legais admissíveis para crianças. Antes de aplicar art. 14 §1º (consentimento de pelo menos um dos pais ou responsável legal), conferir entendimento atual da ANPD.
- ECA (Lei 8.069/1990) — leitura conjunta obrigatória.
- Há discussão sobre Marco Legal de Inteligência Artificial e proteção reforçada para menores no PL 2338/2023.

## Cookies e CMP

- **Guia Orientativo sobre Cookies da ANPD (outubro/2022)** — primeira versão. Cheque atualizações. Princípios principais: consentimento como base legal apenas para cookies não estritamente necessários; banner não pode usar dark patterns; opt-out tão fácil quanto opt-in.

## Setores regulados

- **Saúde:** Resolução CFM 1.821/2007 (prontuário); LGPD art. 11 §4º (uso de dados sensíveis em saúde); ANS para planos de saúde. Verificar Resolução CFM e Resolução ANS recentes.
- **Financeiro:** Resolução BCB 4.658/2018 (segurança cibernética); Open Finance (Resoluções Conjuntas 1, 2 e 3); LGPD + Lei Complementar 105/2001 (sigilo bancário). Acompanhar SCR (Sistema de Informações de Crédito) e Cadastro Positivo (Lei Complementar 166/2019).
- **Educação:** sem norma setorial específica; LGPD se aplica diretamente. Considerar Lei 9.394/1996 (LDB) para dados em rede pública.
- **Telecomunicações:** Lei Geral de Telecomunicações 9.472/1997, regulamentos ANATEL sobre dados de assinantes; Marco Civil da Internet (Lei 12.965/2014) para provedores.

## Prazo de resposta a requisição de titular (LGPD art. 19)

- LGPD art. 19, I: **imediato em formato simplificado** para confirmação da existência e acesso aos dados.
- LGPD art. 19, II: **15 dias** para resposta clara e completa, indicando informações detalhadas.
- Resolução CD/ANPD 18/2024 e atos posteriores podem detalhar prazos para outros direitos (retificação, eliminação, portabilidade) — verificar.

## Comunicação de incidente — LGPD art. 48 + Resolução CD/ANPD 15/2024

- **Prazo:** até **3 dias úteis** da ciência do incidente para comunicar à ANPD e aos titulares afetados.
- **Conteúdo mínimo:** descrição do incidente, dados afetados, número aproximado de titulares afetados, medidas adotadas, riscos.
- O critério "risco ou dano relevante" da LGPD foi detalhado pela Resolução 15/2024 — confira a redação atual.

## PL 2.338/2023 — Marco Legal de Inteligência Artificial

- Aprovado no Senado Federal em dezembro/2024 (cheque o status atual na Câmara `[verificar]`).
- Toca a LGPD nas decisões automatizadas (art. 20) e em sistemas de IA de "alto risco".
- A ANPD foi designada como autoridade competente para certos aspectos no texto do PL — confira a versão final aprovada quando promulgada.

## Como usar este arquivo

Quando uma skill citar regra de privacidade, data de vigência ou threshold, deve marcar: "A regulação de privacidade está em movimento — pode ter mudado desde meu treinamento. Verifique em [fonte ANPD/Planalto/STJ/STF]. Veja `references/currency-watch.md`."

**Este arquivo envelhece.** Vigente em maio/2026. Atualize quando notar drift.
