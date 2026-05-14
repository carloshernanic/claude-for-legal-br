---
name: customize
description: >
  Customização guiada do seu perfil do Legal Builder Hub — mude uma coisa sem
  refazer toda a entrevista de cold-start. Ajuste perfil de prática, starter
  pack instalado, registries monitorados, preferências de update ou rigor de
  QA. Use quando o(a) usuário(a) diz "mude meu [item]", "adicione um
  registry", "atualize meu perfil", "edite minha config" ou "customizar".
argument-hint: "[nome de seção, ou descreva o que quer mudar]"
---

# /customize

## Quando isto roda

O(a) usuário(a) digitou `/builder-hub:customize`. Quer mudar algo no
perfil do Builder Hub — um registry monitorado, preferências de notificação
de update, uma área de prática para recomendações — sem refazer toda a
entrevista de cold-start e sem editar YAML na unha.

## O que fazer

1. **Leia a config.** Leia
   `~/.claude/plugins/config/claude-for-legal/builder-hub/CLAUDE.md`
   (e `~/.claude/plugins/config/claude-for-legal/company-profile.md` um
   nível acima). Se a config do plugin não existir ou ainda tiver valores
   `[PLACEHOLDER]`, diga:

   > Você ainda não rodou o setup. Rode primeiro
   > `/builder-hub:cold-start-interview` — o customize é para ajustar
   > um perfil que você já tem.

2. **Mostre o mapa do customizável.** Liste o que está no perfil, agrupado,
   com uma linha resumindo o valor atual:

   - **Empresa / quem você é** — nome, setor, jurisdições, estágio,
     ambiente de prática *(compartilhado entre os 12 plugins — mudanças
     fluem por `company-profile.md`)*
   - **Seu perfil de prática** — áreas de prática em escopo, usadas para
     recomendar skills comunitárias
   - **Starter pack instalado** — quais plugins e skills estão instalados
     via o hub, com fonte de instalação
   - **Registries monitorados** — repositórios GitHub / URLs de onde o hub
     puxa skills comunitárias
   - **Preferências de update** — cadência de checagem (diária / semanal /
     sob demanda), canal de notificação (Slack / na sessão), auto-update
     vs. prompt
   - **Rigor de QA** — quão agressivamente o `/qa` sinaliza problemas
     numa skill candidata antes da instalação (leniente / médio /
     estrito), e quais checks de failure mode estão ligados
   - **Padrões de instalação de skill** — escopo de instalação (usuário(a)
     / projeto), rodar `/qa` automaticamente antes do install ou não
   - **Integrações** — status Slack / armazenamento documental, fallbacks

3. **Pergunte o que quer mudar.**

   > O que você gostaria de ajustar? Escolha uma seção, ou descreva a
   > mudança com suas palavras.

4. **Faça a mudança.** Mostre o valor atual, peça o novo, explique o que muda
   downstream, confirme, escreva na config.

   Exemplos:
   - *Adicionando novo registry monitorado:* "O `/browse` vai buscar neste
     registry junto com os existentes. O `/update` o checa na próxima
     rodada."
   - *Rigor de QA estrito → médio:* "O `/qa` vai reportar os mesmos
     achados mas não bloqueia a instalação na faixa média a menos que
     você confirme."
   - *Auto-update on → off:* "O hub vai te pedir confirmação antes de
     aplicar updates em vez de aplicar automaticamente."

5. **Para mudanças no perfil compartilhado** (nome da empresa, setor,
   jurisdições, ambiente de prática, estágio): grave em
   `~/.claude/plugins/config/claude-for-legal/company-profile.md` e
   anote:

   > Esta mudança afeta os 12 plugins — qualquer plugin que lê sua pegada
   > jurisdicional agora vê [novo valor].

6. **Encerre.**

   > Pronto. Seu próximo output vai refletir a mudança. Mais alguma coisa?
   > Você pode rodar `/builder-hub:customize` quando quiser.

## Guardrails

- **Nunca delete uma seção.** Se o(a) usuário(a) quer "remover" um registry
  monitorado, ofereça marcar como `[Pausado]` e explique que pausar
  preserva o histórico de instalação mas para os checks de update.
- **Sinalize inconsistência interna.** Se a mudança deixaria o perfil
  inconsistente (p. ex., auto-update on + rigor de QA off; ou perfil de
  prática que não casa com nenhum plugin instalado), sinalize a tensão.
- **Sinalize degradação de guardrail.** Os checks do Legal Skill Design
  Framework (treze parâmetros de design, três modos de falha jurídicos,
  trust-surface) existem para que o `/qa` rode — desligá-los derrota o
  ponto. Se o(a) usuário(a) quer reduzir rigor, recomende a faixa média
  em vez de desligar o check.
- **Uma mudança por vez.** Não re-pergunte a entrevista inteira.
