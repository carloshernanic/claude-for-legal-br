---
name: customize
description: >
  Customização guiada do perfil de prática de contratos comerciais — mude uma
  coisa sem refazer toda a entrevista de cold-start. Ajuste postura de risco,
  contatos de escalonamento, posições do playbook, preferências de triagem de
  NDA, estilo da casa, preferências de revisão ou caminhos de workspace de
  matéria. Use quando o usuário disser "mude minha [coisa]", "atualize meu
  perfil", "edite meu playbook", "ajuste minha config" ou "customize".
argument-hint: "[nome da seção, ou descreva o que quer mudar]"
---

# /customize

**Adaptação BR.** Plugin adaptado ao Brasil (CC, CDC, LGPD, Lei 9.307/1996, LINDB, EOAB). Substitui referências a Delaware/UCC/FRE 408 quando aplicável.

## Quando isto roda

O usuário digitou `/comercial:customize`. Quer mudar algo no perfil
de prática — postura de risco, contato de escalonamento, posição de playbook,
jurisdição, formato de saída — sem refazer toda a entrevista de cold-start e
sem editar YAML à mão.

## O que fazer

1. **Leia a configuração.** Leia
   `~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md`
   (e `~/.claude/plugins/config/claude-for-legal/company-profile.md` um nível
   acima). Se a config do plugin não existir ou ainda contiver valores
   `[PLACEHOLDER]`, diga:

   > Você ainda não rodou o setup. Rode `/comercial:cold-start-interview`
   > primeiro — customize serve para ajustar um perfil que já existe.

2. **Mostre o mapa do que dá para customizar.** Liste o que está no perfil,
   agrupado, com resumo de uma linha do valor atual:

   - **Empresa / quem você é** — nome, setor, jurisdições (UF/Brasil),
     estágio, modalidade de prática, orientação vendedor vs. comprador
     *(compartilhado entre todos os plugins — mudanças fluem pelo
     `company-profile.md`)*
   - **Postura de risco** — conservadora / mediana / agressiva, o que cada uma
     significa para fallbacks e gatilhos de escalonamento
   - **Pessoas** — cadeia de escalonamento, aprovadores por faixa financeira
     e por tipo de cláusula
   - **Posições de playbook** — posições contratuais substantivas: limites de
     responsabilidade (CC 944), escopo de indenização (CC 389/944), cláusula
     penal (CC 408–416), titularidade de PI, LGPD, rescisão, renovação
     automática, reajuste, e fallbacks de cada
   - **Preferências de triagem de NDA** — o que é verde / amarelo / vermelho
     para NDA recebido
   - **Preferências de revisão** — estilo de redline, profundidade de
     explicação, se gera resumo para área de negócio por padrão
   - **Estilo da casa** — formato do documento, bloco de assinatura
     (Lei 14.063/2020 + MP 2.200-2/2001), canal de alerta de renovação,
     formato do registro de desvios
   - **Workflow** — caminhos de workspace de matéria, intake, cadência do
     renewal watcher
   - **Integrações** — CLM (Ironclad/DocuSign/Lawnix/Linte) / e-sig
     (DocuSign/Clicksign/D4Sign/ZapSign/Autentique) / Slack / armazenamento,
     status e fallbacks

3. **Pergunte o que quer mudar.**

   > O que você quer ajustar? Escolha uma seção, ou descreva a mudança com
   > suas palavras.

4. **Faça a mudança.** Mostre o valor atual, peça o novo, explique o que muda
   downstream, confirme, escreva na config.

   Exemplos:
   - *Fallback de limite de responsabilidade 12 meses → 6 meses:* "`/review`
     agora flageia qualquer coisa acima de 6 meses como desvio; entradas
     existentes em deal-debrief ficam como registradas."
   - *Novo aprovador de escalonamento:* "Qualquer redline acima da sua
     alçada vai rotear para este aprovador — `/escalate` vai incluí-lo por
     padrão para a faixa de risco correspondente."
   - *Postura de risco mediana → agressiva:* "Vou aceitar mais posições
     vendedor-amigáveis sem flagear e subir a barra do `[revisar]`."

5. **Para mudanças de perfil compartilhado** (nome da empresa, setor,
   jurisdições, modalidade de prática, estágio): escreva em
   `~/.claude/plugins/config/claude-for-legal/company-profile.md` e anote:

   > Esta mudança afeta todos os plugins — qualquer plugin que leia sua
   > pegada jurisdicional agora vê [novo valor].

6. **Encerre.**

   > Pronto. Sua próxima saída vai refletir a mudança. Mais alguma coisa?
   > Pode rodar `/comercial:customize` quando quiser.

## Guardrails

- **Nunca delete uma seção.** Se o usuário quiser "remover" algo, defina como
  `[Não configurado]` e explique o que isso significa para o comportamento do
  plugin.
- **Flague inconsistência interna.** Se a mudança deixar o perfil inconsistente
  (ex.: postura agressiva + "todo redline precisa de aprovação do GC"; ou
  "vendedor" + uma posição de playbook comprador), flague a tensão e pergunte
  qual quer.
- **Flague degradação de guardrail.** Se o usuário pedir para desligar
  guardrail (tirar a flag `[revisar]`, pular o cabeçalho de sigilo profissional,
  remover tags `[verificar]`), explique do que a guardrail protege e confirme
  o trade-off. A flag `[revisar]`, tags de atribuição de fonte e tags
  `[verificar]` em leis citadas são load-bearing e não devem ser removidas.
- **Uma mudança por vez.** Não refaça a entrevista inteira.
