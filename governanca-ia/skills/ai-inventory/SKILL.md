---
name: ai-inventory
description: >
  Inventário de sistemas de IA por sistema sob o PL 2338/2023 (Marco Legal da
  IA — em tramitação) e, quando houver nexo UE, sob o EU AI Act como
  benchmark — registre papel de cada sistema (desenvolvedor/provedor,
  aplicador (operador), importador, distribuidor, representante autorizado,
  fabricante de produto) e tier de risco (proibido, alto risco, risco
  limitado, risco mínimo, IA de propósito geral, IA de propósito geral
  sistêmica). Papel e tier são avaliados por sistema, não por empresa. Use
  quando o(a) usuário(a) disser "inventário de IA", "adicionar sistema de
  IA", "que sistemas temos", "classifique este sistema de IA", "registro
  PL 2338", "registro EU AI Act", ou "registro de sistemas de IA".
argument-hint: "[list | add | edit <id> | classify <id> | show <id>]"
---

# /ai-inventory

## Quando isto roda

O(A) usuário(a) quer gerenciar o inventário de sistemas de IA sob o PL
2338/2023 (em tramitação na Câmara dos Deputados, aprovado no Senado em
2024 `[verificar]`) e, quando aplicável, sob o EU AI Act como benchmark
global. A ideia central que esta skill existe para reforçar: **papel e
tier são por sistema, não por empresa.** Uma mesma organização pode ser
*desenvolvedora/provedora* do Sistema A, *aplicadora (operadora)* do
Sistema B e *importadora* do Sistema C. Cada combinação aciona um conjunto
diferente de obrigações sob o PL 2338 e, se houver nexo UE, sob o EU AI
Act. O inventário existe para que essas avaliações fiquem rastreadas onde
você consegue encontrar — as obrigações são derivadas em conversa, não a
partir de tabela.

## O que fazer

1. **Leia a config.** Leia
   `~/.claude/plugins/config/claude-for-legal/governanca-ia/CLAUDE.md`.
   Se não existir ou ainda tiver marcadores `[PLACEHOLDER]`, direcione
   o(a) usuário(a) para `/governanca-ia:cold-start-interview`
   primeiro.

2. **Leia o inventário.** O inventário fica em
   `~/.claude/plugins/config/claude-for-legal/governanca-ia/ai-systems.yaml`.
   Se não existir, crie com uma lista `systems:` vazia quando o primeiro
   `add` rodar.

3. **Despache pelo argumento:**

   - Sem argumento, ou `list` → mostre a tabela do inventário (ver **List** abaixo).
   - `add` → rode o fluxo **Add**.
   - `edit <id>` → mostre o registro atual, pergunte o que mudar,
     atualize um campo, confirme, grave.
   - `classify <id>` → rode o **passo a passo de classificação** em um
     registro existente, atualizando role, tier, role_basis e tier_basis.
   - `show <id>` → mostre o registro completo.

4. **Em list, ofereça o dashboard:**
   "Quer o dashboard completo? Filtra por status / tier / nexo UE / dono(a).
   É só falar."

5. **Feche toda ação com gancho para o trabalho do(a) advogado(a).**
   Depois de qualquer gravação, diga:
   > Registrado. Quando estiver pronto(a) para caminhar pelas obrigações
   > deste sistema, é só pedir — vou fazer em conversa e sinalizar onde
   > o mapeamento contra o PL 2338 (e EU AI Act, se aplicável) precisa
   > da sua verificação. Não derivo obrigações de tabela porque o
   > mapeamento é complexo e o texto está em movimento.

## Formato de list

Renderize como tabela compacta:

| ID | Nome | Dono(a) | Status | Nexo UE | Dados pessoais (BR) | Papel | Tier | Próx. revisão |
|----|------|---------|--------|---------|---------------------|-------|------|----------------|
| sys-001 | Triagem de currículos | RH / Jamie | em_producao | sim | sim | aplicador | alto_risco | 2026-08-01 |
| sys-002 | Assistente de redação de e-mails | TI / Priya | em_producao | nao | sim | aplicador | risco_limitado | 2026-12-01 |

Sob a tabela, mostre contagens por tier e uma linha: "N sistemas sinalizados para revisão dentro de 30 dias."

## Fluxo de add (entrevista)

Pergunte, um campo por vez (ou aceite colagem). Campos obrigatórios são
`name`, `owner`, `description`, `status`, `eu_nexus`, `br_dados_pessoais`.
O resto pode ser deferido — diga explicitamente: "pode voltar para
classificação com `/governanca-ia:ai-inventory classify <id>`."

1. **Nome.** Rótulo curto do sistema.
2. **Dono(a).** Pessoa ou time responsável dia a dia.
3. **Descrição.** Uma ou duas frases. O que faz, e contra que dados?
4. **Status.** `planejado | em_desenvolvimento | em_producao | descontinuado`.
5. **Nexo UE.** O sistema é implantado na UE/EEE, ofertado a usuários na UE/EEE, ou usado para produzir outputs que afetam pessoas na UE/EEE? Se algum desses for verdadeiro, análise sob EU AI Act se aplica (como benchmark e, com nexo, como direito aplicável).
6. **Dados pessoais (BR).** O sistema trata dados pessoais sob LGPD (Lei 13.709/2018)? Se sim, LGPD se aplica integralmente; pode disparar RIPD (art. 38), bases legais (arts. 7º/11) e direitos do(a) titular (art. 18), incluindo direito à revisão de decisão automatizada (art. 20).
7. **Avançar para classificação?** Ofereça rodar o passo a passo agora, ou pular e voltar depois.

Atribua um ID: `sys-NNN` onde NNN é o próximo inteiro no arquivo.

## Passo a passo de classificação

O passo a passo produz `role`, `role_basis`, `tier`, `tier_basis`. Ambas
as bases recebem tag `[verificar contra texto atual do PL 2338 / EU AI
Act, conforme aplicável]` — não porque a skill esteja hedging, mas porque
o mapeamento normativo é complexo, o PL 2338 ainda tramita, e o EU AI Act
ainda está em fase de aplicação. O(A) advogado(a) é dono(a) da
verificação.

### Etapa 1: Papel

> **Quem faz o quê com este sistema?**

Opções, com o teste distintivo (alinhado ao PL 2338/2023 e ao EU AI Act):

- **Desenvolvedor / Provedor** — você desenvolve (ou faz desenvolver) e disponibiliza no mercado ou coloca em serviço sob nome ou marca própria.
- **Aplicador (Operador)** — você usa o sistema sob sua autoridade, não para uso pessoal não-profissional. (Mais comum dentro de empresas.) No vocabulário do PL 2338, equivalente funcional ao "operador"/"aplicador".
- **Importador** — você traz um sistema de IA para o Brasil/UE a partir de um(a) provedor(a) estabelecido(a) fora.
- **Distribuidor** — você disponibiliza um sistema no mercado sem ser provedor ou importador.
- **Representante autorizado** — atua em nome de um(a) provedor(a) estrangeiro(a) e está estabelecido(a) no território.
- **Fabricante de produto** — você coloca um sistema de IA de propósito geral (ou outro sistema de IA) em um produto sob seu nome/marca. Tratado como provedor pelo produto.

**Flag de papel dual.** Se o(a) usuário(a) modifica substancialmente um sistema de fornecedor (fine-tuning em dados próprios, muda finalidade, redenominação), pode se tornar **provedor(a)** do sistema modificado mesmo tendo começado como aplicador(a). Sinalize sempre que descreverem modificação além de configuração. `[verificar contra texto atual do PL 2338 / EU AI Act — obrigações de provedor e modificação substancial]`

Escreva o papel. Escreva `role_basis` em uma frase.

### Etapa 2: Tier

> **O que o sistema faz, e o caso de uso cai em categoria regulada?**

Cheque em ordem:

**A. Práticas vedadas.** `[verificar contra texto atual do PL 2338 — capítulo de riscos excessivos; e EU AI Act art. 5, se houver nexo UE]`

Resumos (não texto definitivo) — o PL 2338 propõe categorias como:
- Técnicas subliminares ou enganosas que distorçam materialmente o comportamento de pessoa ou grupo, gerando prejuízo
- Exploração de vulnerabilidades (idade, deficiência, condição socioeconômica) para distorcer comportamento
- Sistemas de pontuação social pelo poder público para tratamento discriminatório/prejudicial generalizado
- Identificação biométrica remota em tempo real em espaços de acesso público com finalidade de segurança pública (exceções restritas — autorização judicial específica `[verificar]`)
- Categorização biométrica para inferir características sensíveis (origem racial, opinião política, filiação sindical, convicção religiosa/filosófica, vida sexual)
- Reconhecimento de emoções no trabalho ou educação (exceções médica e de segurança)
- Coleta indiscriminada de imagens faciais de internet/CCTV para banco de dados de reconhecimento
- Predição criminal individual baseada apenas em traços de personalidade
- Conteúdo gerado por IA representando abuso/exploração sexual infantil (interface com Lei 14.811/2024)
- Deepfake eleitoral (interface com Resoluções TSE 2024)

Se casar → tier é `proibido`. Sinalize o caso de uso como parada e roteie para o fluxo de prática vedada do time de governança. Atenção a interface com CDC (prática abusiva, art. 39), CF (igualdade, art. 5º), Lei 7.716/89 (racismo) e Lei 12.288/2010 (igualdade racial).

**B. Áreas de alto risco.** `[verificar contra texto atual do PL 2338 — anexo / capítulo de alto risco; e EU AI Act Anexo III, se houver nexo UE]`

Categorias propostas (PL 2338 cobre, com texto sujeito a alteração `[verificar]`):
1. Identificação e categorização biométrica
2. Infraestrutura crítica (energia, água, transporte, telecomunicações)
3. Educação e formação profissional (acesso, avaliação, proctoring)
4. Trabalho, gestão de trabalhador e acesso ao autoemprego — recrutamento, seleção, promoção, demissão, alocação de tarefa, monitoramento, performance (interface CLT + Lei 9.029/95)
5. Acesso a serviços essenciais públicos e privados (benefícios, scoring de crédito, precificação de seguros, despacho de emergência) — interface CDC + Lei 4.595/64 + regulações BCB/CVM/SUSEP/ANS
6. Segurança pública / persecução penal (avaliação de risco, polígrafo, deepfake forense, confiabilidade probatória, profiling)
7. Migração, asilo, controle de fronteira (avaliação de risco, verificação documental)
8. Administração da Justiça e processos democráticos (interface Resolução CNJ 332/2020 + 615/2025; interface TSE em eleições)
9. Saúde (interface Provimento CFM 2.314/2022; ANS; ANVISA quando se enquadrar como dispositivo médico) `[verificar]`

Se casar → tier é `alto_risco`. Anote a categoria. Avalie se cabe RIPD (LGPD art. 38) e AIR setorial (Decreto 10.411/2020) em paralelo.

**C. IA de propósito geral / Large Generative Model.** `[verificar contra texto atual do PL 2338 — disposições sobre LGM/GPAI; EU AI Act art. 51 e ss., se aplicável]`

- **IA de propósito geral / LGM:** modelo treinado em grande volume de dados, projetado para generalidade, capaz de executar competentemente ampla gama de tarefas distintas.
- **IA de propósito geral com risco sistêmico:** critério de compute e/ou designação por autoridade `[verificar]`.

**D. Risco limitado.** Chatbots interagindo com pessoas naturais, deepfakes, sistemas de reconhecimento de emoções e categorização biométrica fora do escopo de práticas vedadas — obrigações de transparência (informar que se trata de IA; rotular conteúdo sintético). Interface com CDC art. 6º III (informação) e LGPD art. 9º.

**E. Risco mínimo.** Todo o resto.

Escreva o tier. Escreva `tier_basis` em uma frase, citando o dispositivo (PL 2338, anexo do PL, ou EU AI Act) que casou, com tag `[verificar contra texto atual]`.

### Etapa 3: Recomendações

Ofereça três próximos passos:
1. "Quer que eu caminhe pelas obrigações deste sistema? Faço em conversa — não derivo de tabela. Avaliarei interface com LGPD (especialmente arts. 11, 18, 20, 38), Marco Civil (art. 11), CDC (arts. 12, 14), Resoluções CD/ANPD aplicáveis e setoriais (CNJ/TSE/BCB/CVM/CFM/ANS, conforme o caso)."
2. "Quer rodar `/governanca-ia:aia-generation` para produzir uma avaliação de impacto completa? Quando houver dados pessoais, ela complementa o RIPD — não substitui."
3. "Quer setar uma próxima data de revisão? Adiciono ao inventário."

## Formato do registro

```yaml
systems:
  - id: sys-001
    name: "Ferramenta de triagem de currículos"
    owner: "RH / Jamie"
    description: "Filtra CVs inbound contra critérios da vaga"
    status: em_producao              # planejado | em_desenvolvimento | em_producao | descontinuado
    eu_nexus: false                  # implantado, ofertado, ou afeta pessoas na UE/EEE
    br_dados_pessoais: true          # aciona LGPD; pode exigir RIPD (art. 38)
    role: aplicador                  # desenvolvedor | aplicador | importador | distribuidor | representante_autorizado | fabricante_produto
    role_basis: "Licenciamos da FornecedoraX e implantamos internamente [verificar contra texto atual do PL 2338]"
    tier: alto_risco                 # proibido | alto_risco | risco_limitado | risco_minimo | ia_geral | ia_geral_sistemica
    tier_basis: "Categoria de trabalho/RH — recrutamento e seleção [verificar contra texto atual do PL 2338]; interface LGPD art. 20 (decisão automatizada) e Lei 9.029/95"
    obligations_assessed: false
    obligations_note: "A avaliar: como aplicador de sistema de alto risco — supervisão humana, qualidade de dados de entrada, monitoramento, registros, informação a trabalhadores (CF art. 5º X; LGPD art. 9º), RIPD (art. 38), direito a revisão (art. 20). Comunicação a candidatos(as) e mecanismo de contestação. Provimento de evidências de não-discriminação (CF art. 5º; Lei 7.716/89). [verificar contra texto atual]"
    next_review: "2026-08-01"
    review_trigger: "em modificação substancial ou anualmente"
    created: "2026-05-11"
    updated: "2026-05-11"
```

## Por que esta skill NÃO deriva obrigações automaticamente

O inventário armazena papel, tier e base de cada. NÃO contém tabela hardcoded papel × tier → obrigações.

Quando o(a) usuário(a) pergunta "quais são minhas obrigações para o Sistema X?", a skill faz a análise **em conversa**, com tag `[verificar]`, e roteia para `/governanca-ia:aia-generation` para a avaliação formal, se necessário.

Isto é deliberado:
- Mapeamento de dispositivos é complexo; o PL 2338 está em tramitação com texto sujeito a alterações; Resoluções CD/ANPD continuam saindo; jurisprudência do STJ/STF sobre Marco Civil e responsabilidade por IA está em construção.
- Confiante-e-errado sobre obrigação de compliance termina em memo de diretoria.
- O inventário é um registro para o(a) advogado(a). O(A) advogado(a) é dono(a) da análise de obrigações.

## Guardrails

- **Nunca classifique silenciosamente.** O passo a passo de classificação deve ser visível; não auto-classifique a partir de descrição do sistema.
- **Tags `[verificar]` permanecem.** Não são hedging — são o ponto. Não as retire em outputs.
- **Sinalize modificação substancial.** Sempre que um sistema for modificado além de configuração, peça ao(à) usuário(a) para refazer `/ai-inventory classify` — modificação pode mudar o papel.
- **Não declare obrigações a partir de tabela.** Se perguntado, faça a análise em conversa e roteie para `/aia-generation` para qualquer coisa que precise de registro formal.
