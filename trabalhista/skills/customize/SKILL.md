---
name: customize
description: >
  Customização guiada do seu perfil de prática trabalhista — mude uma coisa
  sem rodar a cold-start interview inteira. Ajuste footprint jurisdicional
  (UFs/categorias/CCTs), postura de risco, contatos de escalação, regras de
  revisão de admissão, regras de revisão de rescisão, posições do manual
  interno, preferências de apuração ou caminhos de workspace de matéria.
  Use quando o(a) usuário(a) disser "mudar minha(meu) [coisa]", "adicionar
  uma UF", "atualizar meu perfil", "editar minha config" ou "customizar".
argument-hint: "[nome da seção, ou descreva o que quer mudar]"
---

# /customize

## Quando isto roda

O(a) usuário(a) digitou `/trabalhista:customize`. Quer mudar algo
no perfil de prática — uma UF, postura de risco, contato de escalação,
posição do manual — sem rodar a cold-start interview inteira nem editar
YAML à mão.

## O que fazer

1. **Leia a config.** Leia
   `~/.claude/plugins/config/claude-for-legal/trabalhista/CLAUDE.md`
   (e `~/.claude/plugins/config/claude-for-legal/company-profile.md` um
   nível acima). Se a config do plugin não existe ou ainda contém
   valores `[PLACEHOLDER]`, diga:

   > Você ainda não rodou o setup. Rode `/trabalhista:cold-start-interview`
   > primeiro — customize é para ajustar um perfil que já existe.

2. **Mostre o mapa customizável.** Liste o que está no perfil, agrupado, com
   um sumário de uma linha do valor atual:

   - **Empresa / quem você é** — nome, indústria, configuração da prática, UFs
     *(compartilhado entre todos os 12 plugins — mudanças fluem por
     `company-profile.md`)*
   - **Footprint jurisdicional** — UFs (e países) com empregados, monoestadual
     vs. multiestadual, categorias sindicais predominantes, CCT/ACT vigentes,
     e qualquer expansão futura. Direciona lógica de suplementos por categoria.
   - **Postura de risco** — conservadora / mediana / agressiva, o que cada uma
     significa para sinalizar risco de rescisão, exigibilidade de cláusulas
     restritivas e adaptação razoável (LBI / Lei 8.213/91 art. 93)
   - **Pessoas** — parceiros de RH/DP, líder de pessoas, advocacia externa,
     cadeia de escalação, sponsor de apuração
   - **Revisão de admissão** — template de carta-proposta/contrato, postura
     sobre cláusulas restritivas (não-concorrência: TST exige prazo + limite
     geográfico + contrapartida), antecedentes (Súmula 568 TST), exame
     admissional (NR-7), eventos eSocial (S-2200)
   - **Revisão de rescisão** — verbas rescisórias padrão (aviso prévio Lei 12.506/2011,
     13º proporcional, férias+1/3, FGTS+40%), prazo do art. 477 §6º (10 dias),
     homologação (CLT 477 / acordo extrajudicial 855-B), CCT/ACT aplicável,
     bandeiras de alto risco (estabilidades, retorno recente, dispensa coletiva
     STF Tema 638)
   - **Manual / regulamento interno** — caminho do arquivo, abordagem de
     suplementos por categoria/UF/CCT, cadência de revisão; atenção a alteração
     unilateral lesiva (CLT art. 468)
   - **Preferências de apuração** — rotulagem privilegiada (EOAB art. 7º XIX),
     protocolo de oitivas (Upjohn / advertência de função), templates de
     resumo por audiência
   - **Workflow** — workspaces de matéria, cadência do leave-tracker,
     caminhos de projeto de expansão
   - **Integrações** — HRIS/folha / Slack / armazenamento de documentos,
     fallbacks; eSocial

3. **Pergunte o que querem mudar.**

   > O que gostaria de ajustar? Escolha uma seção, ou descreva a mudança
   > com suas próprias palavras.

4. **Faça a mudança.** Mostre o valor atual, peça o novo valor, explique
   o que muda downstream, confirme, grave na config.

   Exemplos:
   - *Adicionando bancários ao footprint:* "`/wage-hour-qa` e
     `/termination-review` passarão a aplicar CLT art. 224 (jornada 6h),
     CCT FENABAN e específicos do segmento. `/handbook-updates` passará a
     pedir suplemento para bancários. `/hiring-review` passará a sinalizar
     cláusulas restritivas no contexto do segmento."
   - *Padrão de verbas rescisórias 2 salários → 4 salários (mais favorável que CLT):*
     "`/termination-review` usará o novo baseline no cálculo. Atenção: se o
     padrão acima da CLT está em uso há mais de 10 anos, vira cláusula
     contratual (Súmula 51 TST) — não dá para reduzir depois."
   - *Postura de risco mediana → conservadora:* "Vou sinalizar mais rescisões
     para escalação, recomendar mais cláusulas protetivas em quitação
     (sempre lembrando que a quitação no Brasil tem eficácia liberatória
     limitada — OJ 270 SDI-I TST), e ser mais rígido em cláusulas restritivas."

5. **Para mudanças compartilhadas** (nome da empresa, indústria, jurisdições,
   configuração da prática, estágio): grave em
   `~/.claude/plugins/config/claude-for-legal/company-profile.md` e note:

   > Esta mudança afeta todos os 12 plugins — qualquer plugin que leia seu
   > footprint jurisdicional agora vê [novo valor].

6. **Encerre.**

   > Pronto. Sua próxima saída refletirá a mudança. Mais alguma coisa?
   > Pode rodar `/trabalhista:customize` a qualquer tempo.

## Guardrails

- **Nunca delete uma seção.** Se o(a) usuário(a) quer "remover" uma UF/categoria,
  ofereça marcar como `[Sem empregados no momento — manter regras para reentrada]`
  e explique que ir para `[Não configurado]` derruba a sinalização específica.
- **Sinalize inconsistência interna.** Se a mudança tornaria o perfil
  inconsistente (ex.: bancários no footprint + postura agressiva sobre cargo
  de confiança; ou postura agressiva + "toda rescisão vai para advocacia
  externa"), sinalize a tensão.
- **Sinalize degradação de guardrail.** A checagem pré-voo de citação, tags
  de atribuição de fonte e tags `[verificar]` em leis citadas são load-bearing —
  não remova. A flag `[revisar]` é load-bearing — explique o trade-off antes
  de ajustar.
- **Uma mudança de cada vez.** Não re-rode a entrevista inteira.
