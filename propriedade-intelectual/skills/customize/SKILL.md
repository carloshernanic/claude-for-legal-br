---
name: customize
description: >
  Customização guiada do seu perfil de prática de PI — mude uma coisa sem
  re-rodar a entrevista inicial inteira. Ajuste postura de risco, contatos de
  escalada, escopo de portfólio, estratégia de proteção de marca, postura de
  enforcement, thresholds de clearance (busca de anterioridade marcária),
  regras de revisão OSS ou caminhos de workspace de matéria. Use quando o
  usuário disser "mude meu [item]", "atualize meu perfil", "edite minha
  configuração" ou "customize".
argument-hint: "[nome da seção, ou descreva o que quer mudar]"
---

# /customize

## Quando isto roda

O usuário digitou `/propriedade-intelectual:customize`. Quer mudar algo no perfil de prática
— postura de risco, contato de escalada, posição de portfólio, tática de
enforcement — sem re-rodar a entrevista inicial inteira e sem editar YAML à
mão.

## O que fazer

1. **Ler o config.** Leia
   `~/.claude/plugins/config/claude-for-legal/propriedade-intelectual/CLAUDE.md`
   (e `~/.claude/plugins/config/claude-for-legal/company-profile.md` um nível
   acima). Se o config do plugin não existir ou ainda contiver valores
   `[PLACEHOLDER]`, diga:

   > Você ainda não rodou o setup. Rode `/propriedade-intelectual:cold-start-interview`
   > primeiro — customize é para ajustar um perfil que você já tem.

2. **Mostre o mapa customizável.** Liste o que está no perfil, agrupado, com
   resumo de uma linha do valor atual:

   - **Empresa / quem você é** — denominação, setor, jurisdições, estágio,
     cenário de prática *(compartilhado entre todos os 12 plugins — mudanças
     fluem por `company-profile.md`)*
   - **Perfil de prática de PI** — quais tipos de PI estão em escopo (patente,
     marca, direito autoral, segredo de empresa, desenho industrial),
     orientação de prática (prossecução INPI / transacional / enforcement /
     portfólio in-house)
   - **Postura de risco** — conservadora / equilibrada / agressiva, o que cada
     uma significa para thresholds de clearance, opiniões de FTO e escalada
     de notificação extrajudicial
   - **Pessoas** — advogado(a) de PI, escritórios externos por tipo de PI,
     cadeia de escalada de enforcement, comitê de invenções
   - **Portfólio** — famílias de patente, classes NCL de marca, marcas-chave,
     países de registro (BR via INPI + Protocolo de Madri + nacionais),
     serviços de watch
   - **Proteção de marca** — postura de enforcement em remoção em
     marketplaces, cybersquatting/domain, parodia / limitações dos arts. 46-48
     da Lei 9.610
   - **Postura de enforcement** — quando enviar notificação extrajudicial vs
     carta amena vs ajuizar; gatilhos de escalada por tipo de infração
   - **Clearance e FTO** — fornecedores de busca (busca.inpi.gov.br,
     Espacenet, WIPO Madrid Monitor), thresholds de confiança em clearance,
     formato de opinião de FTO
   - **Revisão OSS** — políticas de tier de licença, licenças bloqueadoras de
     produto, cadência de revisão de novas dependências
   - **Workflow** — workspaces de matéria (IDs de matéria, IDs de família),
     feed do sistema de gestão de PI, formulário de captura de invenção
   - **Integrações** — sistema de docket de patente / conectores INPI /
     Slack / armazenamento de documentos status, fallbacks

3. **Pergunte o que querem mudar.**

   > O que você gostaria de ajustar? Escolha uma seção ou descreva a mudança
   > com suas palavras.

4. **Faça a mudança.** Mostre o valor atual, peça o novo, explique o que
   muda downstream, confirme, grave no config.

   Exemplos:
   - *Adicionando nova classe de marca à watch:* "`/portfolio` incluirá a
     classe NCL XX nos relatórios de watch e `/infringement-triage` roteará
     achados da classe XX consequentemente."
   - *Postura de enforcement agressiva → equilibrada:* "`/cease-desist`
     oferecerá minutas de carta amena como primeira opção em casos ambíguos
     em vez de ir direto para notificação extrajudicial."
   - *Nova licença OSS bloqueadora de produto:* "`/oss-review` reprovará
     revisões que incluam esta licença em vez de apenas avisar."

5. **Para mudanças no perfil compartilhado** (denominação, setor,
   jurisdições, cenário de prática, estágio): grave em
   `~/.claude/plugins/config/claude-for-legal/company-profile.md` e anote:

   > Esta mudança afeta todos os 12 plugins — qualquer plugin que leia seu
   > footprint de jurisdição agora vê [novo valor].

6. **Encerre.**

   > Pronto. Sua próxima saída refletirá a mudança. Mais alguma coisa? Você
   > pode rodar `/propriedade-intelectual:customize` a qualquer momento.

## Guardrails

- **Nunca delete uma seção.** Se o usuário quer "remover" um tipo de PI do
  escopo, marque como `[Atualmente fora de escopo]` e explique o que sai
  junto.
- **Sinalize inconsistência interna.** Se a mudança tornaria o perfil
  inconsistente (ex.: marca fora de escopo + serviço de watch marcária
  configurado; ou postura de enforcement agressiva + "toda notificação
  extrajudicial vai para escritório externo"), sinalize a tensão.
- **Sinalize degradação de guardrail.** A flag `[review]`, tags de atribuição
  de fonte e tags `[verificar]` em autoridades citadas são load-bearing — não
  remova. Confiança de clearance é load-bearing na saída de `/clearance` —
  não suprima.
- **Uma mudança por vez.** Não re-faça a entrevista inteira.
