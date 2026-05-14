---
name: is-this-a-problem
description: >
  Resposta rápida "isso é problema?" para a pergunta rápida no Slack —
  pattern-matches contra sua calibração. Use quando o usuário diz "isso é
  problema", "pergunta rápida", "podemos fazer X", "preciso de revisão
  jurídica para", "sanity check", ou cola pergunta de PM que precisa de
  chamada same-minute fine / precisa olhar / hold.
argument-hint: "[a pergunta]"
---

# /is-this-a-problem

1. Carregue `~/.claude/plugins/config/claude-for-legal/produto/CLAUDE.md` → Calibração de risco.
2. Aplique o workflow de triagem abaixo.
3. Pattern-match. Cheque traps comuns.
4. Responda em um minuto: ✅ Tranquilo / ⚠️ Precisa olhar / 🛑 Hold. Uma frase de porquê.
5. Se ⚠️ ou 🛑: nomeie o próximo passo.

```
/produto:is-this-a-problem "Podemos usar logos de clientes na página de preços?"
```

---

## Matter context

**Matter context.** Cheque `## Matter workspaces` no CLAUDE.md de practice-level. Se `Enabled` é `✗` (default para in-house), pule o resto deste parágrafo — skills usam contexto practice-level e a maquinaria de matéria é invisível. Se habilitado e não há matéria ativa, pergunte: "Qual matéria é esta? Rode `/produto:matter-workspace switch <slug>` ou diga `practice-level`." Carregue o `matter.md` da matéria ativa para contexto e overrides específicos. Escreva outputs na pasta da matéria em `~/.claude/plugins/config/claude-for-legal/produto/matters/<matter-slug>/`. Nunca leia arquivos de outra matéria a menos que `Cross-matter context` esteja `on`.

---

## Checagem de destinatário

Antes de produzir output, cheque para onde vai. Se o usuário nomeou destino (canal, lista de distribuição, contraparte, "todo mundo"), pergunte se está dentro do círculo do sigilo. Canais públicos, listas company-wide, contraparte/advogado da outra parte, fornecedores e clientes (para material de trabalho) quebram a proteção. Quando o destino parece fora do círculo, sinalize e ofereça (a) versão privilegiada só para o jurídico, (b) versão sanitizada para o canal mais amplo, ou (c) ambas — não aplique silenciosamente cabeçalho privilegiado e depois ajude a colar onde o cabeçalho não protege. Ver `## Guardrails compartilhadas → Checagem de destinatário` no CLAUDE.md deste plugin.

## Propósito

A maior parte das perguntas rápidas no Slack é uma de três coisas: (a) não é problema, fale rápido, (b) coisa real que precisa de olhar de verdade, encaminhe, (c) algo que parece OK mas tem trap, pegue o trap. Esta skill triagem em menos de um minuto usando a tabela de calibração.

O objetivo é velocidade. O PM perguntou às 16:47. Quer resposta, não memorando.

## Carregue calibração

Leia `~/.claude/plugins/config/claude-for-legal/produto/CLAUDE.md` → `## Calibração de risco`. O ponto inteiro desta skill é pattern-matching contra aquela tabela.

## A triagem

### Match contra calibração

A pergunta bate com algum padrão na tabela de calibração?

**Bate com "Costuma ser FYI":**
→ Diga. Uma linha. "Tranquilo — [padrão]. Pode mandar."

**Bate com "Costuma exigir trabalho":**
→ Nomeie o trabalho. "Precisa de [RIPD / revisão de fornecedor / check de claim]. Demora [timeline da tabela]. Quer que eu comece?"

**Bate com "Costuma bloquear":**
→ Pare-os. "Espera — [padrão]. Isso precisa de olhar de verdade antes de qualquer um se comprometer com data. Vamos conversar."

**Não bate com nada:**
→ Diga também. "Isso não bate com nada que eu já vi aqui. Precisa de olhar humano — [seu nome] ou eu amanhã?"

### O trap check

Algumas perguntas são OK na superfície mas têm twist. Reconheça o padrão fático, faça a catch question, depois pesquise a doutrina aplicável para o fato específico antes de concluir se é problema ou não.

| Pergunta soa como | Por que pode não ser simples | Pegue perguntando |
|---|---|---|
| "Podemos adicionar [fornecedor] à integração?" | Fornecedor toca nova categoria de dado — sinalize como potencialmente implicando LGPD e regime de fornecedor; encaminhe para pesquisa (cláusula controlador-operador LGPD art. 39) | "Que dado flui para eles?" |
| "Podemos rodar A/B test na página de preço?" | Preço diferencial por segmento pode implicar CDC (prática abusiva — art. 39) e LGPD (decisão automatizada — art. 20) — sinalize e encaminhe | "Os dois braços veem o mesmo preço pela mesma coisa? Como usuários são alocados?" |
| "Podemos opt-in automático no novo feature?" | Default-on para usuários que tinham feito opt-out pode implicar CDC art. 39 IV (prevalecer-se de fraqueza/ignorância) e LGPD (consentimento livre — art. 8º) — sinalize e encaminhe | "Isso respeita preferências existentes?" |
| "Podemos usar logos de clientes no site?" | Uso de logo é permissão separada da relação contratual — sinalize como potencialmente implicando direito de imagem (CC arts. 11 e 20) e os próprios termos do contrato do cliente | "O que o contrato diz sobre publicidade? Temos permissão por escrito?" |
| "Podemos treinar nesse dado?" | Direitos de uso para a finalidade original da coleta podem não se estender a treinamento — sinalize e pesquise a finalidade informada ao titular na coleta (LGPD arts. 6º I — finalidade; 8º — consentimento específico) | "O que dissemos aos titulares quando coletamos? Em quais jurisdições estão?" |
| "É só ferramenta interna" | Ferramentas internas ainda processam dado pessoal — sinalize como potencialmente implicando LGPD (mesmo se titular for empregado) e encaminhe para pesquisa | "De quem é o dado que toca? Empregados, clientes, terceiros?" |
| "Já fazemos coisa parecida" | "Parecida" está fazendo muito trabalho — o delta é onde o problema costuma estar | "Parecida como? O que efetivamente é diferente?" |
| "Podemos usar [fornecedor IA / LLM] para isso?" | Termos de IA do fornecedor podem permitir treino em inputs; caso de uso pode precisar de Avaliação de Impacto Algorítmico — sinalize e encaminhe para `/governanca-ia:use-case-triage` | "Tem aditivo de IA? Que dado entra no modelo?" |
| "Podemos adicionar IA a esse feature?" | Pode ser novo caso de uso fora do registro; pode disparar exigência de avaliação de impacto — sinalize e encaminhe para `/governanca-ia:use-case-triage` | "O que a IA faz — assistiva ou automatizada? Sobre quem age?" |
| "O modelo decide automaticamente" | Decisão automatizada sem revisão humana é regulada (LGPD art. 20 — direito à revisão) — sinalize e pesquise | "Quem é afetado? Tem humano no loop? Onde estão os afetados?" |
| "É conteúdo gerado por IA" | IP do output e deveres de divulgação variam por jurisdição e termos do fornecedor; em propaganda eleitoral, deepfake é proibido (TSE Res. 23.610/2019) — sinalize e encaminhe | "Que tipo de conteúdo? O ToS do fornecedor trata de propriedade do output? Quem é o público?" |
| "Estamos só fazendo fine-tuning no nosso dado" | Direitos sobre dados de treinamento, IP do output e obrigações do fornecedor mudam — sinalize e encaminhe para `/governanca-ia:vendor-ai-review` | "O que há no dado de treino? Tem dado de cliente ou de empregado?" |
| "É promoção/cashback" | Pode disparar Lei 5.768/1971 (distribuição de prêmios) + autorização SECAP/MF; CDC art. 39 (prática abusiva); CONAR — sinalize e pesquise | "É concurso, sorteio, vale-brinde? Qual a mecânica?" |
| "Vamos cobrar mensal com auto-renovação" | CDC art. 49 (arrependimento 7 dias em e-commerce) + Decreto 7.962/2013 + CDC art. 39 — opt-out tem que ser tão fácil quanto opt-in | "Como cliente cancela? Em quantos cliques?" |
| "Vamos exibir comparativo com concorrente" | CDC art. 36 §único (ônus do anunciante) + Código CONAR (comparativa admitida com lealdade, sem denegrir, embasada) | "Que dado sustenta? É verificável? Critério explícito?" |

Se um trap pode estar presente, faça a pergunta antes de responder. Uma pergunta, não checklist. Quando a resposta sugere problema real, sinalize para pesquisa e encaminhe — não conclua doutrina a partir só da pergunta.

## Formato de output

**Para Slack (caso comum):**

Respostas de triagem no Slack são orientação jurídica interna. Se a resposta vai para ticket, documento ou canal amplamente compartilhado com não-jurídico, prepende o cabeçalho de material de trabalho do `~/.claude/plugins/config/claude-for-legal/produto/CLAUDE.md` `## Outputs` (varia por papel — ver `## Quem está usando isto`):

```
[CABEÇALHO DE MATERIAL DE TRABALHO — conforme config ## Outputs]
```

Para DM no fluxo do Slack ao PM, forma curta:

```
[✅ Tranquilo | ⚠️ Precisa olhar | 🛑 Hold]

[Uma frase: a chamada e o porquê.]

[Se ⚠️: o que o olhar envolve, quanto tempo]
[Se 🛑: com quem falar, quando]
```

**Exemplos:**

```
✅ Tranquilo — adicionar evento de analytics é FYI aqui desde que coberto pelas
categorias da Política de Privacidade atual. Este está.
```

```
⚠️ Precisa de RIPD — nova coleta de dado para [categoria]. Costuma levar um dia.
Quer que eu comece? (LGPD art. 38)
```

```
🛑 Hold — "treinar em dado de cliente" dispara várias coisas. O que o contrato
de cliente disse sobre uso de dado? Qual a finalidade informada no momento da
coleta (LGPD art. 6º I)? Vamos puxar antes de qualquer um prometer isso ao cliente.
```

```
⚠️ Precisa de triagem de governança de IA — adicionar LLM nesse fluxo significa
checar o caso de uso contra o registro e confirmar avaliação de impacto antes do
ship. Leva um dia. Quer que eu rode `/governanca-ia:use-case-triage` agora?
(Ref.: PL 2338/2023 + LGPD art. 20)
```

## Quando NÃO usar esta skill

- A pergunta é mesmo complexa (vários issues, área nova) → encaminhe para launch-review ou feature-risk-assessment
- A pergunta é "pode revisar este PRD?" → isso é launch-review, não triagem
- Você não tem certeza → diga "não tenho certeza, deixa eu olhar direito" — resposta rápida errada é pior que resposta certa devagar

## Tom

Rápido, direto, prestativo. O PM não está pedindo aula. Se está tranquilo, diga "tranquilo" — não liste as sete coisas que você checou. Se não está, diga o que não está e o que fazer.

Você é o(a) advogado(a) que as pessoas querem perguntar, não o(a) que elas contornam.

## Encerre com a árvore de decisão de próximos passos

Termine com a árvore de decisão de próximos passos conforme CLAUDE.md `## Outputs`. Customize as opções para o que esta skill acabou de produzir — as cinco branches default (redigir o X, escalar, buscar mais fatos, aguardar, outra coisa) são ponto de partida, não trava. A árvore É o output; o(a) advogado(a) escolhe.
