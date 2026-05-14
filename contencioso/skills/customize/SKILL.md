---
name: customize
description: >
  Customização guiada do seu perfil prático de contencioso — mude uma coisa
  sem rerodar o cold-start inteiro. Ajuste papel na prática, polo (autor /
  réu / misto), calibração de risco, panorama, estilo da casa, contatos de
  escalonamento, vocabulário de severidade, ou paths de workspace de
  matéria. Use quando o(a) usuário(a) disser "muda meu [x]", "atualiza meu
  perfil", "edita minha config", ou "customizar".
argument-hint: "[nome da seção, ou descreva o que quer mudar]"
---

# /customize

## Quando isso roda

O(a) usuário(a) digitou `/contencioso:customize`. Quer mudar algo no
perfil de contencioso — calibração de risco, regra de estilo da casa,
contato de escalonamento, nota de panorama — sem rerodar o cold-start
inteiro e sem editar YAML na mão.

## O que fazer

1. **Ler a config.** Ler
   `~/.claude/plugins/config/claude-for-legal/contencioso/CLAUDE.md`
   (e `~/.claude/plugins/config/claude-for-legal/company-profile.md` um
   nível acima). Se a config do plugin não existe ou ainda contém valores
   `[PLACEHOLDER]`, diga:

   > Você ainda não rodou o setup. Rode
   > `/contencioso:cold-start-interview` primeiro — customize é para
   > ajustar um perfil que você já tem.

2. **Mostrar o mapa customizável.** Liste o que está no perfil, agrupado,
   com resumo de uma linha do valor atual:

   - **Empresa / quem você é** — razão social, setor, jurisdições, estágio,
     setting da prática *(compartilhado entre os 12 plugins — mudanças
     fluem por `company-profile.md`)*
   - **Papel na prática** — in-house / advogado(a) de escritório / solo /
     clínica
   - **Polo** — autor / réu / misto, e nuances de postura (defesa em ação
     coletiva, defesa em regulatório, autor em comercial, etc.)
   - **Calibração de risco** — o que conta como alto / médio / baixo risco
     em notificação extrajudicial recebida, ofício, ou nova matéria;
     gatilhos de escalonamento
   - **Panorama** — adversários frequentes, foros favoráveis e
     desfavoráveis, magistrados(as) a conhecer, relações com escritórios
     externos
   - **Estilo da casa** — formato de peça processual, de declaração, de
     notificação extrajudicial, estrutura de roteiro de depoimento
     pessoal (CPC art. 385), template de preservação documental
   - **Mapa de vocabulário de severidade** — como você traduz rótulos de
     severidade entre saídas a cliente / internas / processuais
   - **Pessoas** — responsáveis por matéria, time interno, escritórios
     externos por tipo de matéria, cadeia de escalonamento
   - **Workflow** — workspaces de matéria, registro de portfólio,
     cadência de oc-status, cadência de renovação de preservação
   - **Integrações** — armazenamento documental / peticionamento
     eletrônico (PJe / e-SAJ / e-Proc / Projudi) / calendário / Slack,
     fallbacks

3. **Perguntar o que querem mudar.**

   > O que você gostaria de ajustar? Escolha uma seção, ou descreva a
   > mudança com suas palavras.

4. **Fazer a mudança.** Mostre o valor atual, peça o novo, explique o que
   muda downstream, confirme, escreva na config.

   Exemplos:
   - *Polo misto → só réu:* "`/new-matter` intake vai parar de fazer as
     perguntas de polo autor. `/demand-draft` continua funcionando para
     notificações extrajudiciais defensivas mas o frame inicial será
     diferente."
   - *Calibração de risco apertando o limite de alto risco:* "Mais
     notificações recebidas e ofícios vão rotear para `/matter-briefing`
     e `/oc-status`."
   - *Novo escritório externo padrão para PI:* "`/oc-status` vai incluir
     essa banca na varredura semanal para matérias tag PI."

5. **Para mudanças no perfil compartilhado** (razão social, setor,
   jurisdições, setting da prática, estágio): escreva em
   `~/.claude/plugins/config/claude-for-legal/company-profile.md` e
   anote:

   > Essa mudança afeta todos os 12 plugins — qualquer plugin que leia
   > seu footprint de jurisdição agora vê [novo valor].

6. **Fechar.**

   > Pronto. Sua próxima saída vai refletir a mudança. Mais alguma coisa?
   > Pode rodar `/contencioso:customize` a qualquer tempo.

## Guardrails

- **Nunca apague uma seção.** Se o(a) usuário(a) quiser "remover" um tipo
  de matéria do escopo, ofereça marcar como `[Não tratado atualmente]` e
  explique o que muda no roteamento de intake.
- **Flag inconsistência interna.** Se a mudança tornar o perfil
  inconsistente (ex. polo só autor + roster de escritórios externos só
  defesa; ou "alto volume" de portfólio + nenhum workspace de matéria
  configurado), flage a tensão.
- **Flag degradação de guardrail.** O alerta de sigilo de tratativas
  (CPC art. 166 §3º; Lei 13.140/2015 art. 30) em `/demand-draft`, o
  cabeçalho de material de trabalho / sigilo profissional (EOAB art. 7º
  XIX; CPC art. 388) nas saídas de matéria, tags de atribuição de fonte,
  e tags `[verify]` em autoridades citadas são load-bearing — não
  remova. A flag `[review]` e o framing "não protocolize sem revisão de
  advogado(a)" são load-bearing.
- **Uma mudança por vez.** Não re-pergunte a entrevista inteira.
