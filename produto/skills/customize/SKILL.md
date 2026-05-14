---
name: customize
description: >
  Customização guiada do seu practice profile de product counsel — mude uma
  coisa sem rodar a entrevista de cold-start inteira. Ajuste calibração de
  risco, contatos de escalação, framework de revisão de lançamento, postura
  de claims de marketing ou paths de matter workspace. Use quando o usuário
  disser "mudar meu [thing]", "atualizar meu profile", "editar meu framework",
  "retunar minha calibração" ou "customize".
argument-hint: "[nome da seção, ou descreva o que quer mudar]"
---

# /customize

> **Adaptação BR.** Configurado para regimes brasileiros: LGPD, Marco Civil, CDC, ECA, LBI, CONAR, etc. Veja `references/terminology-us-to-br.md`.

## Quando isto roda

O usuário digitou `/produto:customize`. Ele quer mudar algo no profile de
product counsel — um threshold de calibração de risco, um contato de escalação,
uma seção de framework — sem rodar a entrevista de cold-start inteira e sem
editar YAML à mão.

## O que fazer

1. **Leia o config.** Leia
   `~/.claude/plugins/config/claude-for-legal/produto/CLAUDE.md`
   (e `~/.claude/plugins/config/claude-for-legal/company-profile.md` um
   nível acima). Se o config do plugin não existe ou ainda contém valores
   `[PLACEHOLDER]`, diga:

   > Você ainda não rodou o setup. Rode `/produto:cold-start-interview`
   > primeiro — customize serve para ajustar um profile que você já tem.

2. **Mostre o mapa customizável.** Liste o que há no profile, agrupado, com
   uma linha de resumo do valor atual:

   - **Empresa / quem você é** — nome, indústria, jurisdições, estágio,
     cenário de prática, área de produto *(compartilhado entre os 12 plugins —
     mudanças fluem por `company-profile.md`)*
   - **Processo de revisão de lançamento** — intake (Jira / Linear / Asana /
     doc), SLA de revisão, tiering de lançamento, local dos PRDs
   - **Framework de revisão** — as categorias contra as quais você revisa
     lançamentos (privacidade/LGPD, PI/INPI, segurança, claims/CDC+CONAR,
     regulatório setorial, acessibilidade/LBI, infanto-juvenil/ECA+CONANDA,
     etc.) e a profundidade em cada
   - **Calibração de risco** — o que é P0 bloqueante / precisa de uma olhada
     de verdade / OK na sua empresa, com exemplos que ancoram os rótulos
   - **Claims de marketing** — postura sobre puffery vs. substanciado (CDC
     art. 36 §único), framing de claims comparativos (CONAR), superlativos
     (CDC art. 37 §1º), regras da casa para claims de feature de IA
   - **Pessoas** — parceiros de produto por superfície, cadeia de escalação
     (seu gestor, GC, comitê de risco), contraparte de marketing
   - **Workflow** — matter workspaces, cadência do watcher de launch-radar,
     template de revisão de lançamento
   - **Integrações** — status de Jira / Linear / Asana / Slack / storage
     documental, fallbacks

3. **Pergunte o que querem mudar.**

   > O que você gostaria de ajustar? Escolha uma seção, ou descreva a mudança
   > com suas palavras.

4. **Faça a mudança.** Mostre o valor atual, peça o novo, explique o que muda
   downstream, confirme, escreva no config.

   Exemplos:
   - *Apertando calibração de risco de "OK" → "precisa de uma olhada de
     verdade" para um padrão:* "`/is-this-a-problem` e `/launch-review` vão
     começar a sinalizar esse padrão. Revisões existentes continuam como
     escritas; reexecute se quiser a nova postura aplicada."
   - *Nova categoria de launch-review:* "`/launch-review` vai adicionar seção
     para essa categoria. `/is-this-a-problem` vai pattern-match na triagem."
   - *Apertando postura de claims de marketing:* "`/marketing-claims-review`
     vai sinalizar mais linguagem como necessitando substanciação (CDC art. 36
     §único) ou reframing."

5. **Para mudanças no profile compartilhado** (nome da empresa, indústria,
   jurisdições, cenário de prática, estágio): escreva em
   `~/.claude/plugins/config/claude-for-legal/company-profile.md` e anote:

   > Esta mudança afeta todos os 12 plugins — qualquer plugin que lê seu
   > footprint de jurisdição agora vê [novo valor].

6. **Encerre.**

   > Pronto. Seu próximo output vai refletir a mudança. Mais alguma coisa? Você
   > pode rodar `/produto:customize` a qualquer momento.

## Guardrails

- **Nunca delete uma seção.** Se o usuário quer "remover" uma categoria de
  revisão, ofereça marcá-la `[Fora de escopo — roteie para outro lugar]` e
  nomeie o plugin / equipe que assume.
- **Sinalize inconsistência interna.** Se a mudança torna o profile
  inconsistente (ex.: escrutínio em claims de feature de IA ligado + sem
  compromissos de política de IA definidos em `/governanca-ia`; ou "SLA
  rápido" + "todo lançamento exige sign-off do GC"), sinalize a tensão.
- **Sinalize degradação de guardrail.** A flag `[review]`, tags de atribuição
  de fonte e tags `[verificar]` em regulamentos citados são load-bearing —
  não remova. A exigência de substanciação em claims é a coisa pela qual
  `/marketing-claims-review` existe; enfraquecê-la derrota a skill — e abre
  exposição sob CDC art. 36 §único (ônus da prova de quem patrocina).
- **Uma mudança de cada vez.** Não reapresente a entrevista inteira.
