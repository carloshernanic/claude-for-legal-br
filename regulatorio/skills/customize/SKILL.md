---
name: customize
description: >
  Customização guiada do perfil da sua prática regulatória — mudar uma coisa
  sem refazer toda a cold-start interview. Ajustar reguladores monitorados,
  índice da biblioteca de políticas, limiar de materialidade, processo de
  resposta a lacunas, configuração de feeds, ou paths de matter workspace.
  Use quando o usuário disser "mudar meu [item]", "adicionar regulador",
  "atualizar minha watchlist", "editar meu threshold", ou "customize".
argument-hint: "[nome da seção, ou descreva o que quer mudar]"
---

> **Adaptação BR não oficial.** Calibrado para reguladores brasileiros (ANPD, CVM, BCB, ANVISA, ANATEL, ANS, ANEEL, ANP, ANTT, ANAC, IBAMA, CADE, SENACON). Lei 9.784/1999, LINDB, Dec. 10.411/2020 (AIR).

# /customize

## When this runs

O usuário digitou `/regulatorio:customize`. Quer mudar algo no perfil
regulatório — um regulador monitorado, um limiar de materialidade, uma fonte
de feed — sem refazer toda a cold-start interview e sem editar YAML à mão.

## What to do

1. **Leia o config.** Leia
   `~/.claude/plugins/config/claude-for-legal/regulatorio/CLAUDE.md`
   (e `~/.claude/plugins/config/claude-for-legal/company-profile.md` um
   nível acima). Se o config do plugin não existir ou ainda contiver
   valores `[PLACEHOLDER]`, diga:

   > Você ainda não rodou o setup. Rode `/regulatorio:cold-start-interview`
   > primeiro — customize serve para ajustar um perfil já existente.

2. **Mostre o mapa customizável.** Liste o que está no perfil, agrupado, com
   resumo de uma linha do valor atual:

   - **Empresa / quem é você** — nome, setor, jurisdições, estágio, ambiente
     de prática *(compartilhado entre os 12 plugins — mudanças fluem via
     `company-profile.md`)*
   - **Reguladores monitorados** — agências / autarquias / SROs (CVM como
     valores mobiliários; ANBIMA como autorregulação) / reguladores estaduais
     no escopo, e quais são "leading" (mais provável de afetar políticas)
     vs. "monitor"
   - **Biblioteca de políticas** — políticas internas indexadas, caminho
     para cada uma, owner por política
   - **Limiar de materialidade** — quando uma mudança regulatória sobe a
     "notable" vs. "report" vs. "digest only"; como esse limiar filtra
     a saída de `/watch`
   - **Processo de resposta a lacunas** — quem triagem, SLA por severidade,
     downstream owners (política, produto, treinamento)
   - **Configuração de feeds** — feeds de reguladores (DOU / sites das agências),
     conectores Thomson Reuters, cadência do sweep `/watch`, canal de digest
   - **Pessoas** — advogado(a) regulatório responsável, owners de políticas,
     redator(a) de manifestações, cadeia de escalada
   - **Workflow** — matter workspaces, tracker de lacunas abertas, tracker
     de prazos de consulta pública, cadência de publicação do digest
   - **Integrações** — Thomson Reuters / Slack / armazenamento de documentos,
     status e fallbacks

3. **Pergunte o que querem mudar.**

   > O que você quer ajustar? Escolha uma seção, ou descreva a mudança em
   > suas próprias palavras.

4. **Faça a mudança.** Mostre o valor atual, peça o novo valor, explique o
   que muda downstream, confirme, escreva no config.

   Exemplos:
   - *Adicionar regulador à watchlist:* "`/watch` vai varrer este regulador
     na próxima execução. `/diff` aceitará inputs do feed de normatização
     deste regulador."
   - *Apertar o limiar de materialidade:* "O digest de `/watch` ficará mais
     curto — itens abaixo do novo limiar saem do digest semanal mas
     continuam pesquisáveis."
   - *Nova política adicionada à biblioteca:* "`/diff` incluirá esta política
     ao cruzar novas normas contra a biblioteca. O comment tracker marcará
     manifestações que afetem esta política."

5. **Para mudanças de perfil compartilhado** (nome da empresa, setor,
   jurisdições, ambiente de prática, estágio): escreva em
   `~/.claude/plugins/config/claude-for-legal/company-profile.md` e note:

   > Esta mudança afeta todos os 12 plugins — qualquer plugin que leia sua
   > pegada jurisdicional agora vê [novo valor].

6. **Encerre.**

   > Pronto. Sua próxima saída refletirá a mudança. Mais alguma coisa? Você
   > pode rodar `/regulatorio:customize` a qualquer momento.

## Guardrails

- **Nunca delete uma seção.** Se o usuário quiser "tirar" um regulador,
  ofereça marcá-lo `[Monitor only]` e explique que monitoring mantém o
  feed em arquivo mas o retira do digest ativo.
- **Sinalize inconsistência interna.** Se a mudança tornar o perfil
  inconsistente (ex.: regulador no escopo + nenhuma jurisdição na pegada
  que o regulador cubra; ou "digest semanal" + limiar de materialidade
  que renda menos de um item por trimestre), sinalize a tensão.
- **Sinalize degradação de guardrails.** Tags `[verify]` em normas citadas,
  atribuição de fonte em pulls de feed, e a flag `[review]` em triagem de
  lacunas são load-bearing — não remova. Limiar de materialidade pode ser
  ajustado, mas abaixar abaixo do ponto em que o digest vira ruído é o
  ponto — alerte se essa for a direção.
- **Uma mudança por vez.** Não refaça a entrevista toda.
