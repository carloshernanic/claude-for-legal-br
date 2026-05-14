---
name: escalation-flagger
description: >
  Roteia um problema contratual para o(a) aprovador(a) correto(a) conforme a
  matriz de escalonamento em
  `~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md`, e
  minuta o pedido. Use quando o usuário disser "quem precisa aprovar isto",
  "escala isto", "isto precisa de sign-off do GC", "rotear para aprovação", ou
  quando outra skill encontra um problema que ultrapassa a alçada do(a)
  revisor(a).
argument-hint: "[descreva o problema, ou referencie um memo de revisão]"
---

# /escalation-flagger

**Adaptação BR.** Plugin adaptado ao Brasil (CC, CDC, LGPD, Lei 9.307/1996, LINDB).

Nomeia o(a) aprovador(a) para um problema contratual conforme a matriz em
`~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md` e
minuta a mensagem para que você não escreva "oi, tem um minuto?" às 17h.

## Instruções

1. **Carregue `~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md`** → seção Escalonamento. Se faltar, diga — o perfil de prática precisa ser editado.

2. **Caracterize o problema:** faixa financeira / desvio de termo / gatilho automático / decisão de negócio.

3. **Cruze com a matriz, nomeie o(a) aprovador(a).** Seja específico — uma pessoa ou papel, não "liderança jurídica".

4. **Minute o pedido** conforme o template abaixo: o que o contrato diz, o que o playbook diz, opções com recomendação, prazo de decisão.

5. **Não envie.** Minute, mostre, deixe o(a) advogado(a) enviar.

## Exemplos

```
/comercial:escalation-flagger
O MSA da Acme tem responsabilidade ilimitada — quem aprova e o que digo?
```

```
/comercial:escalation-flagger
Referência: acme-review-memo.md
Issue: §8.2 ressalvas da indenização
```

---

## Contexto de matéria

**Contexto de matéria.** Cheque `## Workspaces de matéria` no CLAUDE.md de nível de prática. Se `Habilitado` é `✗` (padrão para usuários in-house), pule o resto deste parágrafo — skills usam contexto de nível de prática e a maquinaria de matéria fica invisível. Se habilitado e não houver matéria ativa, pergunte: "Para qual matéria? Rode `/comercial:matter-workspace switch <slug>` ou diga `nível de prática`." Carregue o `matter.md` da matéria ativa para contexto e overrides. Escreva saídas na pasta de matéria em `~/.claude/plugins/config/claude-for-legal/comercial/matters/<slug>/`. Nunca leia arquivos de outra matéria salvo se `Contexto cross-matter` estiver `ligado`.

---

## Propósito

Todo time de contratos tem matriz de escalonamento, escrita ou não. Esta skill lê a escrita (em `~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md`), cruza um problema contratual com ela, nomeia o(a) aprovador(a) e minuta o pedido para o(a) advogado(a) não escrever "oi, tem um minuto?" às 17h.

## Carregue a matriz

**Qual lado?** Antes de cruzar com a matriz, determine de qual lado a empresa está no contrato cujo problema está sendo escalado. Geralmente óbvio: se a contraparte é fornecedor (presta bens/serviços), você é comprador. Se a contraparte é cliente (compra seu produto/serviço), você é vendedor. Se não for óbvio, pergunte. Leia a seção correspondente do playbook (`### Playbook vendedor` ou `### Playbook comprador`) para avaliar se o termo está dentro dos fallbacks ou aciona escalonamento automático — um termo aceitável de um lado pode ser hard-no do outro. **E sempre antes: é B2B (CC) ou consumo (CDC)? Cláusula nula sob CDC art. 51 não precisa de escalonamento — precisa de rejeição.** Anote o lado no pedido minutado para que o(a) aprovador(a) saiba qual playbook foi aplicado.

Leia `~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md` → `## Escalonamento`. Se faltar ou estiver vago, diga — a entrevista de cold-start deveria ter capturado isto e, se não capturou, o perfil de prática precisa ser editado.

Estrutura esperada:

| Quem aprova | Faixa | Escala para | Via |
|---|---|---|---|
| Paralegal | Termos padrão, <R$ 50K | Advogado(a) | Slack |
| Advogado(a) | Não-padrão mas dentro dos fallbacks, <R$ 500K | GC | Slack ou e-mail |
| GC | Tudo o mais | CFO/Conselho | Reunião |

Mais **gatilhos automáticos de escalonamento** — coisas que escalam independentemente do valor. Tipicamente: responsabilidade ilimitada, cessão de PI, qualquer item das listas "Nunca aceitar", qualquer cláusula nula sob CDC art. 51.

## Workflow

### Passo 1: Caracterize o problema

O que está sendo escalado?

- **Faixa financeira:** Valor do contrato excede a alçada de alguém
- **Desvio de termo:** Um termo está fora dos fallbacks do playbook — alguém mais sênior precisa decidir se aceita
- **Gatilho automático:** Um dos itens always-escalate está presente (inclui cláusula nula sob CDC art. 51 — exoneração/atenuação de responsabilidade do fornecedor, renúncia a direitos, inversão de ônus da prova, arbitragem compulsória em consumo, eleição de foro abusiva)
- **Decisão de negócio:** Não é call jurídico — precisa do dono do negócio, não da liderança jurídica

Não escale coisas que efetivamente estão ok. Se o termo está dentro dos fallbacks de `~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md`, não precisa subir.

### Passo 2: Cruze com a matriz

```
O problema é gatilho automático?
  → SIM: escale para [pessoa nomeada para esse gatilho]
  → NÃO: continue

O valor está acima do limite do(a) revisor(a)?
  → SIM: escale para quem tem alçada nesse nível
  → NÃO: continue

O desvio está fora de todos os fallbacks documentados?
  → SIM: escale para quem pode aprovar termos não-padrão
  → NÃO: revisor(a) pode aprovar — sem escalonamento
```

### Passo 3: Nomeie o(a) aprovador(a)

Seja específico. Não "escale para liderança jurídica" — nomeie a pessoa ou papel de `~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md`. Se a matriz não nomeia ninguém para esta situação, diga: "A matriz não cobre [situação]. Sugiro pedir ao(à) [GC] que decida."

### Passo 4: Minute o pedido

O(A) aprovador(a) deve conseguir decidir pela mensagem sozinha — sem "deixa eu puxar o contrato".

```markdown
**Escalando para:** [nome]
**Via:** [Slack #canal / e-mail / reunião — conforme `~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md`]
**Urgência:** [prazo se houver]

---

Oi [nome] —

Preciso da sua chamada no [tipo de contrato] da [Contraparte]. [Uma frase sobre o contexto do deal.]

**O problema:** [Português claro, um parágrafo. O que querem, por que está fora do nosso padrão, qual é o risco de fato.]

**O que o contrato diz:**
> "[citação exata]"

**O que nosso playbook diz:** [citação de `~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md`]

**Regime:** [B2B (CC) / consumo (CDC) / adesão B2B (CC art. 423)]

**Opções:**
1. **Aceitar** — [uma linha sobre por que pode estar ok]
2. **Reagir com:** "[redação proposta]" — [uma linha sobre reação provável da contraparte]
3. **Sair** — [uma linha sobre se é realista dado o contexto de negócio]

**Minha recomendação:** [qual opção e por quê, brevemente]

**Decisão por:** [data, se houver prazo]

[Link para o memo completo]
```

### Passo 5: Registre o escalonamento

Se este time usa sistema de tickets ou workflows de aprovação em CLM, logue. Senão, anote no memo de revisão que o escalonamento foi enviado, para quem e quando. A próxima pessoa que ler o memo deve ver o status.

## Calibração: na dúvida, escale com nota

O custo de um escalonamento desnecessário é ~30 segundos do(a) aprovador(a) — lê, diz "ok, prossegue", e o registro mostra que viu. O custo de um escalonamento perdido é assinar termo não aprovado, o que é porta de mão única. Os custos não são simétricos. **Na dúvida, escale.**

A calibração do que merece escalonamento vive em `~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md`, não nesta skill. Cheque a posição declarada do playbook, seus fallbacks e a lista "escalonamento automático independentemente de valor":

- **Claramente dentro da faixa de fallback:** sem escalonamento.
- **Claramente fora da faixa, ou na lista de escalonamento automático:** escale.
- **Incerto — o termo é ambíguo, novo, ou argumentavelmente dentro da faixa mas o argumento é forçado:** escale assim mesmo e anote a incerteza explicitamente. O draft sinaliza a pergunta específica que o(a) aprovador(a) precisa decidir e por que a skill não conseguiu confidentemente colocar dentro do fallback. O(a) aprovador(a) afunila; a skill não.

Não suprima escalonamento porque sobre-escalonamento poderia treinar aprovadores a passar batido. Esse é problema de experiência-de-aprovador que o(a) advogado(a) resolve ajustando limites no playbook, não problema que a skill resolve fazendo seu próprio juízo subjetivo sobre termo em que está incerta.

Se um termo aparecer que o playbook não cobre, não chute o limite — pergunte ao(à) advogado(a) revisor(a) se esta classe de problema deve escalar, e ofereça registrar a resposta em `~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md` para que revisões futuras sejam consistentes.

## O que esta skill NÃO faz

- Não aprova nada. Rotea.
- Não decide entre as opções. O draft inclui recomendação, mas quem decide é o(a) aprovador(a).
- Não envia a mensagem — minuta. O(a) advogado(a) envia depois de ler.
