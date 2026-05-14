---
name: customize
description: >
  Customização guiada do seu perfil de prática societária — mudar uma coisa
  sem rerodar todo o cold-start. Ajustar postura de risco, contatos de
  escalonamento, módulos ativos (M&A / Conselho / Companhia Aberta / Gestão de
  Entidades), limiares de materialidade, formato de anexos de declarações,
  precedentes de deliberações, ou caminhos de workspace de matéria. Use quando
  o(a) usuário(a) disser "muda meu [X]", "atualiza meu perfil", "edita meu
  config" ou "customize".
argument-hint: "[nome da seção, ou descreva o que quer mudar]"
---

# /customize

## Quando isto roda

O(a) usuário(a) digitou `/societario:customize`. Quer mudar algo no perfil
de prática — uma postura de risco, um contato de escalonamento, um toggle de
módulo, um formato de saída — sem rerodar todo o cold-start e sem editar YAML
na mão.

## O que fazer

1. **Ler o config.** Leia
   `~/.claude/plugins/config/claude-for-legal/societario/CLAUDE.md`
   (e `~/.claude/plugins/config/claude-for-legal/company-profile.md` um nível
   acima). Se o config do plugin não existir ou ainda contiver valores
   `[PLACEHOLDER]`, diga:

   > Você ainda não rodou o setup. Rode `/societario:cold-start-interview`
   > primeiro — customize é para ajustar um perfil que você já tem.

2. **Mostrar o mapa do que é customizável.** Liste o que está no perfil,
   agrupado, com uma linha de resumo do valor atual:

   - **Empresa / quem você é** — nome empresarial, setor, jurisdições, estágio,
     companhia aberta vs. fechada, contexto de prática *(compartilhado entre
     todos os 12 plugins — alterações fluem por `company-profile.md`)*
   - **Módulos ativos** — quais entre M&A, Conselho & Secretaria, Companhia
     Aberta, Gestão de Entidades estão ligados. Ligar/desligar um módulo muda
     quais skills pedem setup.
   - **Postura de risco** — conservador / médio / agressivo, o que cada uma
     significa para materialidade de diligência e escopo de anexos
   - **Pessoas** — time da operação, secretário(a) de governança, dono da
     gestão de entidades, cadeia de escalonamento
   - **Módulo M&A** — limiares de materialidade (valor de contrato, headcount,
     receita), plataformas de VDR confiáveis, nível de confiança da revisão
     bulk por IA (Luminance / Kira), cadência de briefing ao time da operação
   - **Módulo Conselho & Secretaria** — formato de deliberação da casa,
     preferências de assinatura, estrutura de comitês
   - **Módulo Companhia Aberta** — calendário de divulgações (Formulário de
     Referência, ITR, DFP, Fato Relevante — RCVM 80/44), controles internos,
     timing de revisão pré-divulgação
   - **Módulo Gestão de Entidades** — tabela de entidades, despachante /
     escritório de registro, Juntas Comerciais relevantes, calendário de
     obrigações anuais (ECD/ECF, DEFIS, e-Social, RAIS, balanço, RDE-IED)
   - **Workflow** — workspaces de matéria (salas de operação), local do
     checklist de fechamento, cadência do agente VDR
   - **Integrações** — Box / Intralinks / Datasite / Junta Comercial /
     despachante / Slack — status, fallbacks

3. **Perguntar o que querem mudar.**

   > O que você quer ajustar? Escolha uma seção, ou descreva a mudança em suas
   > palavras.

4. **Fazer a mudança.** Mostre o valor atual, peça o novo valor, explique o que
   muda downstream, confirme, grave no config.

   Exemplos:
   - *Limiar de materialidade R$ 2 mi → R$ 5 mi:* "`/diligence-issue-extraction`
     e `/material-contract-schedule` passarão a tratar R$ 5 mi como o corte.
     Achados já registrados ficam como estão; rerodar se quiser o novo limiar
     aplicado retroativamente."
   - *Ativar o módulo Companhia Aberta:* "Vou te perguntar sobre calendário de
     divulgações (Formulário de Referência, ITR, DFP, Fato Relevante CVM Res.
     44) e controles internos na próxima vez que rodar algo nessa área."
   - *Confiança em revisão bulk por IA "checar toda linha" → "spot-check 10%":*
     "`/ai-tool-handoff` fará QA de uma amostra de 10% em vez de toda
     extração."

5. **Para alterações no perfil compartilhado** (nome empresarial, setor,
   jurisdições, contexto de prática, estágio): grave em
   `~/.claude/plugins/config/claude-for-legal/company-profile.md` e diga:

   > Esta mudança afeta todos os 12 plugins — qualquer plugin que leia sua
   > pegada jurisdicional agora vê [novo valor].

6. **Encerrar.**

   > Pronto. Sua próxima saída refletirá a mudança. Mais alguma coisa? Você
   > pode rodar `/societario:customize` quando quiser.

## Guardrails

- **Nunca delete uma seção.** Se o(a) usuário(a) quiser "remover" algo,
  marque como `[Não configurado]` e explique o que isso significa para o
  comportamento do plugin.
- **Sinalize inconsistência interna.** Se a mudança tornar o perfil
  inconsistente (ex.: módulo Companhia Aberta off + "advogado(a) CVM" no
  escalonamento; ou postura de risco agressiva + limiar de materialidade
  R$ 100 mil), sinalize a tensão.
- **Sinalize degradação de guardrail.** A sinalização `[review]`, as tags de
  atribuição de fonte em documentos recuperados e as tags `[verifique]` em
  autoridades citadas são load-bearing — explique o trade-off antes de remover.
- **Uma mudança por vez.** Não re-pergunte a entrevista inteira.
