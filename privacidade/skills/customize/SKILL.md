---
name: customize
description: >
  Customização guiada do seu perfil de prática em privacidade — altere uma coisa
  sem rerodar a entrevista cold-start inteira. Ajuste postura de risco, contatos
  de escalonamento, playbook de contrato de tratamento, compromissos da política
  de privacidade, estilo do RIPD, processo de requisição de titular, ou caminhos
  de workspace de caso. Use quando o(a) usuário(a) disser "altere [X]",
  "atualize meu perfil", "edite meu playbook", ou "customize".
argument-hint: "[nome da seção, ou descreva o que quer alterar]"
---

# /customize

## Quando isto roda

O(a) usuário(a) digitou `/privacidade:customize`. Quer alterar algo no
perfil de privacidade — postura de risco, contato de escalonamento, posição
de contrato de tratamento, seção de RIPD, prazo de requisição de titular —
sem rerodar a entrevista cold-start inteira e sem editar YAML na mão.

## O que fazer

1. **Leia a configuração.** Leia
   `~/.claude/plugins/config/claude-for-legal/privacidade/CLAUDE.md`
   (e `~/.claude/plugins/config/claude-for-legal/company-profile.md` um
   nível acima). Se a configuração do plugin não existir ou ainda contiver
   valores `[PLACEHOLDER]`, diga:

   > Você ainda não rodou o setup. Rode primeiro
   > `/privacidade:cold-start-interview` — o customize é para ajustar
   > um perfil que você já tem.

2. **Mostre o mapa do que se pode customizar.** Liste o que está no perfil,
   agrupado, com resumo de uma linha do valor atual:

   - **Empresa / quem você é** — nome, setor, jurisdições, estágio, configuração
     de prática, orientação controlador vs. operador *(compartilhado entre
     todos os 12 plugins — alterações fluem via `company-profile.md`)*
   - **Postura de risco** — conservadora / mediana / agressiva, o que cada
     uma significa para obrigações de operador, transferências internacionais
     e retenção
   - **Pessoas** — Encarregado(a) (LGPD art. 41), time de privacidade,
     contato com engenharia, advocacia externa, cadeia de escalonamento
   - **Playbook de contrato de tratamento** — posições sobre notificação de
     suboperador, eliminação, auditoria, responsabilidade (solidária — LGPD
     art. 42), transferência internacional, cláusulas-padrão da ANPD
     (Resolução CD/ANPD 19/2024) — como operador e como controlador
   - **Compromissos da política de privacidade** — promessas feitas no aviso
     de privacidade que o `/policy-monitor` confronta com a prática
   - **Estilo do RIPD** — ordem de seções, scoring de risco, enquadramento
     de stakeholders, quando os gatilhos do art. 38 LGPD se aplicam
   - **Processo de requisição de titular (LGPD art. 18)** — verificação de
     identidade, prazos do art. 19 (imediato para confirmação e acesso;
     15 dias para resposta completa), aplicação de hipóteses de não
     atendimento, estrutura do template de resposta
   - **Workflow** — caminho de entrada, workspaces de caso, cadência da
     varredura do policy-monitor
   - **Integrações** — status de armazenamento de documentos / ferramenta
     de privacidade / Slack, fallbacks

3. **Pergunte o que querem alterar.**

   > O que você gostaria de ajustar? Escolha uma seção, ou descreva a
   > mudança com suas próprias palavras.

4. **Faça a alteração.** Mostre o valor atual, peça o novo valor, explique
   o que muda a jusante, confirme, escreva no arquivo.

   Exemplos:
   - *Aviso de troca de suboperador 30 dias → 14 dias:* "O `/dpa-review`
     vai sinalizar qualquer prazo inferior a 14 dias como desvio. Contratos
     já assinados permanecem como registrados."
   - *Nova hipótese de não atendimento no playbook:* "O `/dsar-response`
     vai trazer essa hipótese à tona na etapa de análise quando os fatos
     se encaixarem."
   - *Postura de risco mediana → conservadora:* "Vou sinalizar mais
     atividades para escalonamento de RIPD, recomendar cláusulas mais
     restritivas para transferência internacional (Resolução CD/ANPD
     19/2024) e ser mais conservador em retenção (LGPD art. 16)."

5. **Para alterações de perfil compartilhado** (nome da empresa, setor,
   jurisdições, configuração de prática, estágio): escreva em
   `~/.claude/plugins/config/claude-for-legal/company-profile.md` e note:

   > Essa alteração afeta todos os 12 plugins — qualquer plugin que leia
   > sua jurisdição agora enxerga [novo valor].

6. **Encerre.**

   > Pronto. Sua próxima saída refletirá a alteração. Mais alguma coisa?
   > Você pode rodar `/privacidade:customize` a qualquer momento.

## Guardrails

- **Nunca remova uma seção.** Se o(a) usuário(a) quiser "remover" um
  regime do escopo, ofereça marcá-lo como `[Fora do escopo atual]` e
  explique o que a marcação desabilita.
- **Sinalize inconsistência interna.** Se a alteração tornar o perfil
  inconsistente (ex.: "somente operador" + posições de controlador
  ativas; ou "sem nexo na UE" + cláusulas-padrão da Comissão Europeia
  no template padrão; ou "sem tratamento internacional" + Resolução
  CD/ANPD 19/2024 ativa no playbook), sinalize a tensão.
- **Sinalize degradação de guardrail.** A flag `[review]`, as tags de
  atribuição de fonte, as tags `[verificar]` em normas citadas, e a
  checagem obrigatória de gatilhos de RIPD no `/triage` são portantes —
  não remova. Se os prazos legais de resposta a requisição de titular
  forem ajustados para abaixo do mínimo legal (LGPD art. 19), recuse
  e explique por quê.
- **Uma alteração por vez.** Não rerode a entrevista inteira.
