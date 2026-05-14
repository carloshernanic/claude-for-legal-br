---
name: use-case-triage
description: >
  Determine rapidamente se uma atividade de tratamento precisa de RIPD,
  se há gatilho mandatório para RIPD sob LGPD art. 38, ou se pode seguir —
  surfacia conflitos com a política de privacidade e roteia para o próximo
  passo. Use quando o(a) usuário(a) perguntar "isto precisa de RIPD",
  "triagem desta funcionalidade", "checagem de privacidade em X",
  "isto está ok pelo ângulo de privacidade", ou descrever atividade nova
  de tratamento, funcionalidade de produto ou relação com fornecedor.
argument-hint: "[descreva a atividade de tratamento ou a funcionalidade]"
---

# /use-case-triage

1. Leia `~/.claude/plugins/config/claude-for-legal/privacidade/CLAUDE.md`. Confirme que a prática de privacidade está configurada — se não, pare e direcione ao setup.
2. Rode o workflow abaixo. Clarifique a atividade se estiver vaga.
3. Checagem de gatilho da casa → checagem de gatilho mandatório de RIPD (LGPD art. 38; orientação ANPD) → checagem de conflito com política de privacidade.
4. Saída: classificação (SEGUIR / RIPD NECESSÁRIO / RIPD MANDATÓRIO / PARAR), fundamentação, tabela de condicionantes se necessário, handoffs entre plugins.
5. Ofereça continuar para geração de RIPD se a avaliação for necessária.

```
/privacidade:use-case-triage "Nova funcionalidade que usa dados comportamentais para personalizar recomendações de conteúdo"
```

---

# Triagem de Caso de Uso em Privacidade

## Contexto de caso

**Contexto de caso.** Verifique `## Workspaces de caso` no CLAUDE.md de nível de prática. Se `Habilitado` for `✗` (padrão para in-house), pule o resto deste parágrafo — skills usam contexto de prática e a maquinaria de caso fica invisível. Se habilitado e não houver caso ativo, pergunte: "De qual caso é? Rode `/privacidade:matter-workspace switch <slug>` ou diga `nível de prática`." Carregue o `matter.md` do caso ativo para contexto e overrides específicos. Escreva saídas na pasta do caso em `~/.claude/plugins/config/claude-for-legal/privacidade/matters/<matter-slug>/`. Nunca leia arquivos de outro caso a menos que `Contexto cruzado entre casos` esteja `on`.

---

## Checagem de destino

Antes de produzir saída, cheque para onde vai. Se o(a) usuário(a) nomeou destino (canal, lista de distribuição, contraparte, "todo mundo"), pergunte se está dentro do círculo de sigilo profissional do(a) advogado(a). Canais públicos, listas corporativas amplas, contraparte/advocacia adversa, fornecedores e clientes (para material de trabalho) podem comprometer o tratamento confidencial. Quando o destino parecer fora do círculo, sinalize e ofereça (a) versão sigilosa só para o jurídico, (b) versão sanitizada para o canal amplo, ou (c) ambas — não aplique silenciosamente cabeçalho sigiloso para depois ajudar a colar onde o cabeçalho não alcança. Veja a `## Guardrails compartilhados → Checagem de destino` canônica no CLAUDE.md deste plugin.

## Finalidade

Responder a pergunta que aparece antes de alguém rodar um RIPD: "isto precisa mesmo de um?" E, se precisa, de que tipo, e o que está no caminho?

Triagem de privacidade é mais rápida que geração de RIPD, mas é a montante dela. Não escreve a avaliação — determina se uma é necessária e em que termos. A skill de geração de RIPD faz o trabalho profundo.

A saída é uma de quatro classificações:
- **SEGUIR** — Sem necessidade de RIPD. Salvaguardas padrão se aplicam.
- **RIPD NECESSÁRIO** — Avaliação necessária antes ou em paralelo ao deployment.
- **RIPD MANDATÓRIO** — Relatório de Impacto à Proteção de Dados mandatório sob LGPD art. 38 (alto risco; tratamento de dados sensíveis em larga escala; perfilamento; tecnologia inovadora; orientação específica da ANPD em vigor). Barra mais alta, envolvimento do(a) Encarregado(a)/jurídico provável.
- **PARAR** — Atividade de tratamento conflita com a política de privacidade ou não tem base legal (LGPD arts. 7º ou 11) como descrita. Precisa redesenho antes de prosseguir.

## Premissa de jurisdição

Esta triagem assume o escopo jurisdicional especificado em sua configuração. Regras de privacidade, gatilhos de avaliação e bases legais variam materialmente por jurisdição (LGPD vs. GDPR vs. regimes setoriais). Se a atividade de tratamento, controlador ou titulares afetados estiverem em jurisdição diferente da configurada, esta classificação pode não se aplicar como redigida.

## Leia a configuração primeiro

Antes de triagem, sempre leia `~/.claude/plugins/config/claude-for-legal/privacidade/CLAUDE.md`. Os critérios de gatilho de RIPD, footprint regulatório e compromissos da política de privacidade ali são autoritativos. Raciocínio genérico de lei de privacidade não substitui o que esta empresa de fato comprometeu.

Se o arquivo está faltando ou contém `[PLACEHOLDER]`, traga este bounce à tona:

> Notei que você ainda não configurou seu perfil de prática — é como customizo critérios de gatilho de RIPD, footprint regulatório e compromissos da política de privacidade para sua prática.
>
> **Duas opções:**
> - Rode `/privacidade:cold-start-interview` (2 minutos) para configurar seu perfil, e eu trio com base na SUA prática.
> - Diga **"provisório"** e eu trio contra padrões genéricos — jurisdição BR/LGPD, postura mediana de risco, papel de advogado(a), sem playbook — e marco toda saída com `[PROVISÓRIO — configure seu perfil para saída customizada]` para você ver o que eu faço antes de se comprometer.

### Modo provisório

Se o(a) usuário(a) disser "provisório", rode a triagem normalmente usando estes padrões genéricos: postura mediana de risco, papel de advogado(a), jurisdição BR (LGPD + linhas-base setoriais comuns), sem playbook (classifique por princípios gerais de LGPD em vez de casar com compromissos configurados). Marque a nota de revisão e todo bloco de achado com `[PROVISÓRIO]`. Ao final da saída, anexe:

> "Essa foi uma rodada genérica contra premissas padrão. Rode `/privacidade:cold-start-interview` para obter saída calibrada à SUA prática — seu footprint regulatório, seus compromissos de política de privacidade, sua postura de risco. 2 minutos."

---

## Processo de triagem

### Passo 1: Entender a atividade

Se a descrição estiver vaga, pergunte antes de classificar. Seja específico em:

- Que dados estão sendo coletados ou tratados? Quais categorias? Há **dados pessoais sensíveis** (LGPD art. 5º II — origem racial ou étnica, convicção religiosa, opinião política, filiação a sindicato, dado referente à saúde ou à vida sexual, dado genético ou biométrico)?
- Quem são os titulares — clientes, empregados, terceiros? **Crianças ou adolescentes** (LGPD art. 14; ECA)?
- Qual a finalidade? Que problema isso resolve?
- Esta é coleta nova de dados, ou reúso de dados que você já tem? (LGPD art. 6º II — adequação ao tratamento original)
- Há operador (fornecedor) envolvido? Novo ou existente? (LGPD arts. 39-40 — contrato de tratamento)
- Há decisão automatizada envolvida — o output afeta alguém? (LGPD art. 20 — direito à revisão)
- Qual o contexto de deployment — interno apenas, voltado a cliente, público?

"Funcionalidade nova" e "atividade de tratamento de dados" não bastam para triar com precisão.

---

### Passo 2: Checar gatilhos da casa

Leia `~/.claude/plugins/config/claude-for-legal/privacidade/CLAUDE.md` → `## Estilo da casa para RIPD` → Critérios de gatilho. Aplique.

Se o gatilho da casa for atingido → no mínimo **RIPD NECESSÁRIO**.

Se gatilho da casa não atingido, continue para Passo 3 antes de concluir SEGUIR. Algumas atividades demandam RIPD independentemente da política interna.

---

### Passo 3: Checagem de avaliação mandatória

**Antes de pesquisar gatilhos específicos por regime, faça a pergunta de overlay setorial primeiro.** Se o tratamento toca categoria de dados regulada por regime setorial brasileiro (financeiro, saúde, crianças/adolescentes, telecom, dados de empregados), o overlay setorial costuma ser o framework controlador, e a triagem precisa trazer isso à tona cedo em vez de depois.

> **Overlays setoriais brasileiros — pergunte primeiro:**
>
> O tratamento toca:
> - **Dados financeiros ou de operações financeiras** (BCB / CMN / Lei Complementar 105/2001 — sigilo bancário; Resolução BCB 4.658/2018 — segurança cibernética; Open Finance — Resoluções Conjuntas)? Sobrepõe à LGPD com obrigações específicas de retenção, comunicação e arquitetura de segurança.
> - **Dados de saúde** (LGPD art. 11; ANS para planos de saúde; Resolução CFM 1.821/2007 — prontuário; sigilo médico CP art. 154)? Dados de saúde são sensíveis (art. 5º II) — base legal restrita do art. 11.
> - **Dados de crianças e adolescentes** (LGPD art. 14 + ECA — Lei 8.069/1990)? Base legal específica (consentimento de pelo menos um dos pais, salvo nas hipóteses do §3º); melhor interesse; vedações de publicidade direcionada a crianças (CDC + Resolução CONANDA 163/2014).
> - **Dados de telecomunicações** (Lei Geral de Telecomunicações 9.472/1997; Marco Civil da Internet — Lei 12.965/2014, arts. 10-15 — guarda de registros)?
> - **Dados de empregados ou candidatos** (CLT + LGPD + Súmula 568 TST sobre antecedentes; Lei 14.611/2023 — transparência salarial)?
> - **Dados de consumidor enquanto consumidor** (CDC — Lei 8.078/1990 + LGPD)?
>
> Se sim a qualquer: o overlay setorial costuma fornecer a restrição substantiva controladora, não apenas uma isenção de regra geral. Pesquise e cite o dispositivo específico antes de continuar. Uma atividade pode satisfazer a LGPD mas violar o sigilo bancário; pode ter base legal do art. 7º mas conflitar com regra da ANVISA — o overlay setorial não desaparece porque a LGPD foi observada.

Para cada regime em `~/.claude/plugins/config/claude-for-legal/privacidade/CLAUDE.md` → `## Footprint regulatório`, **pesquise os gatilhos mandatórios vigentes de avaliação de privacidade/proteção de dados**. Cite Resolução, lei, ou orientação do regulador com referências de pinpoint. Note datas de vigência — a ANPD publica e atualiza orientações periodicamente; não confie em checklist estático. Sinalize incerteza para verificação por advogado(a) em vez de chutar.

**Gatilhos do art. 38 LGPD (RIPD mandatório quando a ANPD solicitar ou quando o tratamento for de alto risco):**
- Tratamento baseado em **legítimo interesse** (LGPD art. 10 §3º — ANPD pode solicitar RIPD)
- **Decisões automatizadas** que afetam interesses do titular (LGPD art. 20)
- Tratamento de **dados sensíveis** em larga escala
- Tratamento de dados de **crianças e adolescentes**
- Uso de **tecnologias inovadoras** (reconhecimento facial, IA, biometria, perfilamento)
- Atividades de **monitoramento sistemático em larga escala** (ex.: CCTV em espaço público; tracking comportamental cross-context)

Se **qualquer** gatilho mandatório aplicável for atingido → **RIPD MANDATÓRIO**, independentemente do gatilho da casa.

**Indicadores fortes (não necessariamente mandatórios mas vale fazer um):**
- Tecnologia nova ou uso novo de tecnologia existente
- Dados de crianças e adolescentes
- Combinação de datasets que não foram coletados juntos (finalidade originária diversa — LGPD art. 6º I)
- Dados que poderiam viabilizar discriminação (LGPD art. 6º IX — não-discriminação)
- Tratamento que titulares não esperariam (vetor da expectativa razoável)
- Publicidade comportamental cross-context, lookalike audiences, ou outra atividade de ad-tech baseada em tracking (sobressai conflitos com compromissos de política e overlays setoriais de forma confiável)

Um ou mais indicadores fortes sem gatilho mandatório pesquisado → escale para **RIPD NECESSÁRIO** (não RIPD mandatório, mas sinalize na saída).

---

### Passo 4: Checagem de conflito com política de privacidade

Leia `~/.claude/plugins/config/claude-for-legal/privacidade/CLAUDE.md` → `## Compromissos da política de privacidade`. Cheque a atividade proposta contra cada compromisso declarado.

**Conflitos comuns a capturar:**
- Política diz "coletamos X, Y, Z" — esta atividade coleta W. Atualização de política necessária antes de lançar, ou parar de coletar W.
- Política diz "não compartilhamos com terceiros" — esta atividade passa dados para fornecedor para finalidade dele. Pesquise se o fluxo se enquadra como compartilhamento que exige base legal específica (LGPD art. 7º + adequação ao art. 6º II) e se requer aviso ao titular (art. 9º).
- Política declara prazos de retenção — esta atividade retém por mais tempo. Atenção ao LGPD art. 15 (término do tratamento) e art. 16 (hipóteses limitadas de conservação).
- Política diz "usamos dados apenas para [finalidade]" — esta atividade usa para finalidade nova sem novo consentimento ou avaliação de legítimo interesse. Quebra de adequação (art. 6º II).
- Política especifica direitos do titular oferecidos — esta atividade cria nova categoria de dados sem que o processo de atendimento a requisição esteja preparado (art. 18).

Se houver conflito direto → **PARAR**. Não "siga com cautela" — o conflito com a política tem que ser resolvido (atualização de política ou redesenho da atividade) antes de prosseguir.

---

### Passo 5: Classificação e saída

---

### Bottom line
[RIPD necessário / RIPD mandatório / Seguir — uma frase do porquê]

---

**ATIVIDADE:** [Declare a atividade de tratamento como você a entende]

**CLASSIFICAÇÃO:** [SEGUIR / RIPD NECESSÁRIO / RIPD MANDATÓRIO / PARAR]

**Gatilho da casa atingido?** [Sim / Não]
**Gatilho mandatório de RIPD LGPD art. 38?** [Sim — [gatilho] / Não]
**Conflito com política de privacidade?** [Nenhum / Sim — [conflito específico]]

**Fundamentação:**
[1-3 frases. Para SEGUIR: o que torna seguro sob a política atual. Para RIPD: o que cria a obrigação. Para PARAR: qual compromisso de política específico ou princípio está em conflito.]

---

*Se RIPD NECESSÁRIO ou RIPD MANDATÓRIO — condicionantes antes de prosseguir:*

| Requisito | Responsável | Feito? |
|---|---|---|
| [ex.: Relatório de Impacto à Proteção de Dados — formato RIPD completo] | [Encarregado(a) / jurídico] | ☐ |
| [ex.: Avaliação de legítimo interesse (se LI for a base — LGPD art. 10)] | [Encarregado(a) / jurídico] | ☐ |
| [ex.: Consulta ao(à) Encarregado(a) (LGPD art. 41 §2º III)] | [Encarregado(a)] | ☐ |
| [ex.: Contrato de tratamento com fornecedor (LGPD arts. 39-40)] | [Privacidade / Jurídico] | ☐ |
| [ex.: Atualização da política de privacidade antes do lançamento (art. 9º — informação ao titular)] | [Encarregado(a)] | ☐ |
| [ex.: Mecanismo de consentimento construído e testado (art. 8º — consentimento livre, informado e inequívoco)] | [Produto] | ☐ |
| [ex.: Processo de direitos do titular cobre a nova categoria (art. 18)] | [Privacidade / Produto] | ☐ |

**Base legal (LGPD art. 7º para dados comuns; art. 11 para sensíveis):** [Consentimento / Cumprimento de obrigação legal / Execução de políticas públicas / Realização de estudos por órgão de pesquisa / Execução de contrato / Exercício regular de direitos / Proteção da vida / Tutela da saúde / Legítimo interesse / Proteção do crédito — ou "indefinida — precisa de determinação no RIPD"]

**Próximo passo — oferta de continuar:**

Após apresentar resultado de RIPD NECESSÁRIO ou RIPD MANDATÓRIO, sempre encerre com:

> "Quer que eu inicie o RIPD agora? Posso rodar as perguntas de intake e produzir o documento de avaliação sem você precisar rodar comando separado."

Se sim, carregue a skill `pia-generation` e continue na mesma conversa — passe a descrição da atividade e os gatilhos já identificados.

Se não, o resultado da triagem permanece. O RIPD pode ser rodado a qualquer momento com:
`/privacidade:pia-generation [atividade]`

---

*Se PARAR:*

**Conflito:** [Compromisso específico da política de privacidade ou princípio em conflito]

**Para prosseguir, uma destas tem que mudar:**
- [Opção A — redesenhar a atividade para não criar o conflito]
- [Opção B — atualizar a política de privacidade para cobrir este tratamento (requer revisão se a atualização é em si consistente com base legal)]

Não ofereça caminho à frente se não houver. Se o tratamento simplesmente não pode ser reconciliado com compromissos declarados ou base legal, diga isso.

---

### Passo 6: Handoffs entre plugins

**Handoff para governança de IA:** Se a atividade envolve sistema de IA tomando ou influenciando decisões sobre indivíduos:

> "Esta atividade envolve decisão por IA. Uma avaliação de impacto de IA provavelmente é necessária além do RIPD (LGPD art. 20 + PL 2.338/2023 `[verificar]`). Use `/governanca-ia:aia-generation [atividade]` para rodar em paralelo — não são substitutos."

**Handoff para product counsel:** Se isto é nova funcionalidade ou lançamento de produto:

> "Se isto é parte de lançamento de produto, traga o product counsel. Use `/produto:launch-review` — ele detecta o componente de privacidade e roteia para este plugin."

Sinalize apenas handoffs de fato relevantes. Não anexe ambos como boilerplate.

---

## Triagem em lote

Se o(a) usuário(a) apresentar lista de funcionalidades, roadmap, ou backlog — tabela-resumo primeiro, depois expanda cada entrada que não seja SEGUIR:

| # | Atividade | Classificação | Condição-chave / bloqueio |
|---|---|---|---|
| 1 | [atividade] | 🟢 Seguir | — |
| 2 | [atividade] | 🟡 RIPD necessário | Avaliação de base legal necessária; contrato de tratamento com fornecedor não está em vigor |
| 3 | [atividade] | 🟠 RIPD mandatório | Tratamento de dados sensíveis em larga escala (LGPD art. 38; art. 11) |
| 4 | [atividade] | 🔴 Parar | Conflito com política de privacidade — adequação à finalidade |

---

## Casos limítrofes e modos de falha

**"Está anonimizado" não significa automaticamente SEGUIR.**
Pergunte como foi anonimizado e se a reidentificação é realisticamente possível dado o conjunto de dados. **Dado pseudonimizado ainda é dado pessoal sob LGPD** (art. 13 §4º — dado pseudonimizado pode voltar a ser pessoal se reidentificado). Anonimização efetiva (art. 12) exige meios técnicos razoáveis e disponíveis no momento.

**"A gente já faz coisa parecida" não é triagem.**
Tratamento existente que nunca foi avaliado não dá guarida a tratamento novo. Se a atividade nova é materialmente diferente em escala, finalidade ou categoria de dados, trie do zero.

**"É só um piloto" não pula triagem.**
Piloto que toca dados reais de usuário ou empregado se sujeita aos mesmos gatilhos. Aplique a mesma classificação; se RIPD necessário, o piloto deveria ter um.

**"O fornecedor cuida de toda a privacidade."**
Operador cuida da infraestrutura. Você ainda é o controlador determinando as finalidades (LGPD art. 5º VI). Se dados pessoais fluem para o operador, contrato de tratamento é necessário (LGPD arts. 39-40) e a triagem ainda se aplica à finalidade. **Responsabilidade é solidária** (LGPD art. 42 — controlador e operador podem responder solidariamente por danos).

**Dado inferido e atributos derivados contam.**
Se a atividade gera dado inferido sobre indivíduos (ex.: score comportamental, preferência prevista), trate o atributo inferido como dado pessoal para fins de triagem. Não deixe "estamos apenas computando um score" obscurecer o que o score representa.

## Encerre com a árvore de decisão de próximos passos

Encerre com a árvore de decisão de próximos passos conforme `CLAUDE.md` `## Saídas`. Customize as opções ao que esta skill produziu — as cinco ramificações padrão (minutar X, escalar, buscar mais fatos, aguardar, outra coisa) são ponto de partida, não trava. A árvore é a saída; o(a) advogado(a) escolhe.
