---
name: customize
description: >
  Customização guiada do seu perfil de prática de governança de IA — mude uma
  coisa sem refazer toda a entrevista cold-start. Ajuste postura de risco,
  contatos de escalonamento, entradas do registro de casos de uso, posições de
  fornecedor de IA, compromissos de política de IA, house style de avaliação
  de impacto, ou caminhos de workspace de matéria. Use quando o(a) usuário(a)
  disser "mudar meu(minha) [coisa]", "atualizar meu perfil", "editar minha
  config", "ajustar meu playbook", ou "customizar".
argument-hint: "[nome da seção, ou descreva o que quer mudar]"
---

# /customize

## Quando isto roda

O(A) usuário(a) digitou `/governanca-ia:customize`. Quer mudar algo no
perfil de prática — postura de risco, contato de escalonamento, posição de
playbook, jurisdição, formato de output — sem refazer toda a entrevista
cold-start e sem editar YAML na mão.

## O que fazer

1. **Leia a config.** Leia
   `~/.claude/plugins/config/claude-for-legal/governanca-ia/CLAUDE.md`
   (e `~/.claude/plugins/config/claude-for-legal/company-profile.md` um
   nível acima). Se a config do plugin não existir ou ainda tiver valores
   `[PLACEHOLDER]`, diga:

   > Você ainda não rodou o setup. Rode `/governanca-ia:cold-start-interview`
   > primeiro — o customize é para ajustar um perfil que já existe.

2. **Mostre o mapa customizável.** Liste o que está no perfil, agrupado, com
   uma linha de resumo do valor atual:

   - **Empresa / quem você é** — nome, setor, jurisdições, estágio, setting
     de prática *(compartilhado entre todos os 12 plugins — mudanças fluem
     por `company-profile.md`)*
   - **Footprint regulatório** — PL 2338/2023, Resoluções CD/ANPD, Resolução
     CNJ 332/2020 + 615/2025, Marco Civil, CDC, deliberações setoriais
     (BCB/CVM/ANS/CFM), Resoluções TSE, Lei 14.811/2024, PL 2630, EU AI Act
     (se houver nexo UE) em escopo
   - **Postura de risco** — conservadora / intermediária / agressiva, o que
     cada uma significa para triagem e output de RIPD/AIA
   - **Pessoas** — time de governança, dono(a) de risco de IA, cadeia de
     escalonamento, aprovadores
   - **Registro de casos de uso** — entradas aprovado / condicional / nunca,
     e condições anexadas a cada uma
   - **Inventário de sistemas de IA** — papel por sistema (provedor /
     aplicador (operador) / etc.) e tier sob o PL 2338/2023 (e EU AI Act se
     aplicável). Rode `/governanca-ia:ai-inventory` para o editor
     dedicado.
   - **Governança de fornecedores de IA** — treinamento sobre dados,
     responsabilidade, aviso de mudança de modelo e outras posições no seu
     playbook de fornecedor de IA
   - **Compromissos de política de IA** — os compromissos públicos ou
     internos que sua política de IA assumiu e que o plugin cruza contra
   - **House style de avaliação de impacto** — ordem das seções, formato de
     pontuação de risco, enquadramento de stakeholders (RIPD/AIA)
   - **Workflow** — caminho de intake, formato de output, caminhos de
     workspace de matéria, cadência de revisão do policy monitor
   - **Integrações** — o que está conectado (Slack, armazenamento de
     documentos, scheduled-tasks), o que faz fallback

3. **Pergunte o que quer mudar.**

   > O que você gostaria de ajustar? Escolha uma seção, ou descreva a
   > mudança com suas próprias palavras.

4. **Faça a mudança.** Mostre o valor atual, peça o novo, explique o que
   muda a jusante, confirme, grave na config.

   Exemplos de explicação a jusante:
   - *Postura de risco intermediária → conservadora:* "Vou sinalizar mais
     casos de uso como condicionais em vez de aprovados, surgir mais
     follow-ups de RIPD/AIA, e recomendar redlines mais conservadores em
     contratos de fornecedor de IA."
   - *Adicionando um(a) contato de escalonamento:* "Toda skill que roteia
     escalonamentos (`/use-case-triage`, `/vendor-ai-review`,
     `/reg-gap-analysis`) agora incluirá esse contato nas faixas de risco
     relevantes."
   - *Nova entrada no registro de casos de uso:* "`/use-case-triage` vai
     casar contra essa entrada na próxima rodada. RIPDs/AIAs existentes
     não são reescritos — refaça se quiser refletir a nova postura."

5. **Para mudanças no perfil compartilhado** (nome da empresa, setor,
   jurisdições, setting de prática, estágio): grave em
   `~/.claude/plugins/config/claude-for-legal/company-profile.md` e anote:

   > Esta mudança afeta todos os 12 plugins — qualquer plugin que leia seu
   > footprint de jurisdição agora vê [novo valor].

6. **Feche.**

   > Pronto. Seu próximo output refletirá a mudança. Mais alguma coisa?
   > Você pode rodar `/governanca-ia:customize` a qualquer tempo.

## Guardrails

- **Nunca apague uma seção.** Se o(a) usuário(a) quiser "remover" algo,
  setar para `[Não configurado]` e explicar o que isso significa para o
  comportamento do plugin. ("Remover sua cadeia de escalonamento significa
  que `/use-case-triage` vai sinalizar itens dignos de escalonamento mas
  não vai rotear para uma pessoa específica.")
- **Sinalize inconsistência interna.** Se a mudança tornar o perfil
  inconsistente (p.ex., postura agressiva + escalonamento "tudo vai para o
  Jurídico"; ou "EU AI Act em escopo" + "nenhum sistema sinalizado para UE";
  ou "in-house em saúde" + "Provimento CFM 2.314/2022 fora de escopo"),
  sinalize a tensão e pergunte qual deles eles querem.
- **Sinalize degradação de guardrail.** Se o(a) usuário(a) pedir para
  desligar um guardrail ("pare de adicionar a tag `[revisar]`", "deixe o
  aviso de citações", "pule o cabeçalho de sigilo"), explique o que o
  guardrail protege e confirme se entendem o trade-off. A maioria dos
  guardrails é ajustável — alguns são estruturais:
  - O mecanismo de flag `[revisar]` (diz ao(à) usuário(a) quando precisa
    de julgamento jurídico em vez de resposta confiante errada) —
    load-bearing, não remover.
  - Tags de atribuição de fonte em conteúdo recuperado — load-bearing,
    não remover.
  - Tags `[verificar]` em leis/resoluções citadas — load-bearing, não
    remover.
  - Cabeçalho de sigilo profissional (EOAB art. 7º, XIX) — load-bearing.
- **Uma mudança por vez.** Não refaça toda a entrevista. Se o(a)
  usuário(a) quiser várias mudanças, trate sequencialmente e confirme cada
  uma antes de avançar.
