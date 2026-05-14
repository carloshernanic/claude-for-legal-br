---
name: pia-generation
description: >
  Gera um Relatório de Impacto à Proteção de Dados Pessoais (RIPD — LGPD art. 38)
  no estilo da casa para nova funcionalidade, produto ou atividade de tratamento,
  usando a estrutura aprendida do RIPD-semente. Use quando o(a) usuário(a) diz
  "escrever um RIPD", "relatório de impacto", "precisamos de RIPD para isto",
  "revisar privacidade desta funcionalidade", ou descreve nova atividade de
  tratamento.
argument-hint: "[nome da funcionalidade ou descrição]"
---

# /pia-generation

> **Adaptação BR.** Esta skill foi adaptada para o regime da **LGPD (Lei 13.709/2018) art. 38**: PIA/DPIA → **RIPD** (Relatório de Impacto à Proteção de Dados Pessoais). Supervisory authority → **ANPD**. "Attorney work product" → sigilo profissional (**EOAB art. 7º XIX**). Bases legais → LGPD art. 7º (dados comuns) e art. 11 (dados sensíveis). Sempre cite primárias BR.

1. Carregue `~/.claude/plugins/config/claude-for-legal/privacidade/CLAUDE.md` → estilo da casa para RIPD (gatilho, estrutura, profundidade, aprovação).
2. Rode o workflow abaixo.
3. Cheque: o RIPD é de fato necessário? (Gatilho da casa + pesquisar quando a ANPD pode requerer (LGPD art. 38) e quando a prática recomenda — citar primárias, verificar atualidade.)
4. Intake: faça as perguntas à equipe de produto. Pode puxar de PRD se houver.
5. Escreva RIPD no formato da casa. Inclua checagem de consistência com a política de privacidade.
6. Saída com lista de condições e responsáveis nomeados. Encaminhe para aprovação (Encarregado(a) — LGPD art. 41 §2º III).

```
/privacidade:pia-generation "Funcionalidade de geolocalização"
```

```
/privacidade:pia-generation
PRD: [link Drive]
```

---

# Geração de RIPD (LGPD art. 38)

## Contexto de caso

**Contexto de caso.** Cheque `## Workspaces de caso` no CLAUDE.md de prática. Se `Enabled` é `✗` (padrão para in-house), pule o resto deste parágrafo. Se habilitado e sem caso ativo, pergunte: "Para qual caso? Rode `/privacidade:matter-workspace switch <slug>` ou diga `prática`." Carregue o `matter.md` do caso ativo para contexto e overrides. Escreva saídas em `~/.claude/plugins/config/claude-for-legal/privacidade/matters/<matter-slug>/`. Nunca leia arquivos de outro caso salvo se `Cross-matter context` for `on`.

---

## Checagem de destino

Antes de produzir saída, cheque para onde vai. Se o(a) usuário(a) nomeou destino (canal, lista, contraparte, "todo mundo"), pergunte se está dentro do círculo de sigilo profissional. Canais públicos, listas amplas, contraparte/advocacia adversa, fornecedores e clientes (quando recebem análise interna de risco) renunciam a proteção. Quando o destino parece fora do círculo, sinalize e ofereça (a) versão sigilosa só para o jurídico, (b) versão sanitizada para canal amplo, ou (c) ambas — não aplique cabeçalho de sigilo silenciosamente e ajude a colar onde o cabeçalho não alcança. Veja a versão canônica em `## Guardrails compartilhados → Checagem de destino` no CLAUDE.md.

## Propósito

Um RIPD é a conversa com a equipe de produto, capturada. Pergunta: que dados, por quê, por quanto tempo, quem vê, o que pode dar errado. Esta skill estrutura a conversa e escreve a saída no formato deste time — o que foi aprendido do RIPD-semente no cold-start.

## Hipótese jurisdicional

Esta avaliação assume o escopo jurisdicional especificado na configuração — **primariamente LGPD para tratamento no Brasil ou de titulares no Brasil (art. 3º)**. Regras, gatilhos de avaliação e bases legais variam materialmente entre regimes (LGPD vs. GDPR vs. CCPA vs. setoriais). Se a atividade, o(a) controlador(a) ou os(as) titulares afetados(as) estiverem em jurisdição diferente, esta análise pode não se aplicar literalmente.

## Carregue contexto anterior sobre esta funcionalidade / atividade

Antes de escrever um RIPD novo, cheque a pasta de saídas para trabalho prévio sobre a mesma funcionalidade, atividade ou contraparte. Leia `~/.claude/plugins/config/claude-for-legal/privacidade/CLAUDE.md` → `## Saídas` para o caminho. Procure:

- **Triagens prévias (`use-case-triage`)** cobrindo esta atividade — risk rating, condições obrigatórias e preocupações da triagem são porta de entrada do RIPD.
- **RIPDs prévios (`pia-generation`)** para a mesma atividade ou que se sobreponham — um RIPD superveniente deve reconciliar (o que mudou, o que ficou). Um RIPD que silenciosamente chega a conclusão diferente é contradição que o(a) revisor(a) não vê.
- **Revisões de contrato (`dpa-review`)** para fornecedores em escopo — informam análise de suboperador / transferência internacional / retenção.

Se houver saída prévia, cite no RIPD:

> "Triagem prévia ([data]) classificou como [nível de risco] e exigiu [condições]. Este RIPD constrói sobre — [condições satisfeitas, remanescentes, redimensionadas]."

Se houver RIPD prévio:
> "Este RIPD supera o RIPD de [data] porque [motivo — mudança de escopo, nova categoria de dado, mudança de fornecedor, mudança regulatória]. Conclusões mantidas: [X]. Conclusões revisadas: [Y, porque Z]."

**Carregue severidade upstream como PISO** por regra de severidade no `~/.claude/plugins/config/claude-for-legal/privacidade/CLAUDE.md` → `## Guardrails compartilhados`. Triagem que classificou alto risco não pode virar RIPD baixo risco sem explicar por quê e o que mudou.

Se não houver saída prévia, diga: "Nenhuma triagem ou RIPD anterior sobre esta atividade na pasta de saídas; é início a frio" — para o(a) revisor(a) saber que a checagem rodou e não achou nada para reconciliar.

## Carregue estilo da casa

Leia `~/.claude/plugins/config/claude-for-legal/privacidade/CLAUDE.md` → `## Estilo da casa para RIPD`. Tem:
- O que dispara RIPD aqui (pode não bater com gatilhos legais — alguns times fazem RIPD para tudo, outros só para alto risco)
- Estrutura-template extraída do RIPD-semente
- Profundidade típica
- Quem aprova (Encarregado(a) — LGPD art. 41 §2º III)

Se a estrutura do semente está na config, **use**. O ponto é que este RIPD pareça com os outros do time, não com um genérico.

## Passo 0: Precisa de RIPD?

Cheque os gatilhos em `~/.claude/plugins/config/claude-for-legal/privacidade/CLAUDE.md`. É a resposta da casa.

Além disso, **pesquise os gatilhos vigentes de avaliação obrigatória** para cada regime no footprint:

- **LGPD art. 38:** a ANPD **pode determinar** ao(à) controlador(a) que elabore RIPD, especialmente quando o tratamento envolve dados sensíveis (art. 5º II), perfilamento, decisão automatizada (art. 20), dados de crianças/adolescentes (art. 14), ou risco a direitos e liberdades. Cite a Resolução CD/ANPD aplicável (à data, **Resolução CD/ANPD 65/2024** sobre programa de governança em privacidade reforça RIPD como instrumento; verifique atualidade).
- **Setoriais BR:** BCB (Open Finance), ANS, CFM podem ter exigências paralelas.
- **GDPR/UK GDPR:** Art. 35 — DPIA mandatório para alto risco; lista regulatória de "must list".
- **CCPA/CPRA e outros estaduais EUA:** risk assessment quando aplicável.

Cite o dispositivo controlador (lei, resolução, guia) com pinpoint. Verifique atualidade — thresholds e definições mudam por novas resoluções da ANPD. Sinalize incerteza em vez de chutar.

> **Sem complementação silenciosa.** Se a consulta retornar pouco ou nada para os gatilhos de RIPD / regras de base legal, reporte e pare. NÃO preencha sem perguntar. Diga: "A busca retornou [N] resultados de [ferramenta]. Cobertura rasa para [regime / questão]. Opções: (1) ampliar query, (2) tentar outra ferramenta, (3) busca web — resultados serão tagueados `[busca web — verificar]` e devem ser conferidos contra fonte primária, ou (4) sinalizar não verificado e parar. Qual prefere?" Quem decide aceitar fonte de menor confiança é o(a) advogado(a).
>
> **Atribuição de fonte.** Etiquete cada citação no RIPD com origem: `[ANPD]`, `[JusBrasil]`, `[Diário Oficial]`, `[site do regulador]`, ou nome da MCP para citações de conector; `[busca web — verificar]` para busca; `[conhecimento do modelo — verificar]` para conhecimento de treino; `[fornecido pelo usuário]` quando o(a) usuário(a) entregou. Citações com `verificar` têm risco maior de fabricação e devem ser conferidas primeiro. Nunca remova ou colapse.

Além dos mandatos estatutários, trate como **indicadores fortes** de que vale fazer RIPD ainda que não estritamente obrigatório:

- Tecnologia nova ou uso inédito de tech existente
- Dados de crianças/adolescentes (LGPD art. 14)
- Dados sensíveis em larga escala (LGPD art. 11 c/c art. 5º II)
- Combinação de bases que não foram coletadas juntas
- Dados que podem habilitar discriminação
- Tratamento que titulares não esperariam
- Decisão automatizada com efeito significativo (LGPD art. 20)
- Transferência internacional (Res. CD/ANPD 19/2024)

Se nenhum gatilho legal aplica e o da casa também não → "Não parece precisar de RIPD. Eis nota de um parágrafo para o arquivo explicando por quê, caso alguém pergunte."

## O intake

Antes de escrever, obtenha respostas da equipe de produto. Conversa serve — não é form para enviar.

### O quê e por quê

- Qual é a funcionalidade/produto/mudança?
- Qual problema do(a) usuário(a) resolve?
- Que dados pessoais toca? Específico — "dados de usuário" não é resposta. Quais campos?
- Algo é nova coleta, ou são dados que já tem?
- Qual o tratamento — armazenamento, análise, compartilhamento, decisão automatizada?

### Base legal / checagens por regime

Para cada regime aplicável, **pesquise o framework vigente** e cite primárias:

- **LGPD:** identifique a **base legal** (art. 7º para dados comuns; art. 11 para sensíveis) por finalidade. As 10 hipóteses do art. 7º: consentimento, cumprimento de obrigação legal/regulatória, execução de políticas públicas, estudos por órgão de pesquisa, execução de contrato/procedimentos preliminares, exercício regular de direitos em processo, proteção da vida/incolumidade física, tutela da saúde, legítimo interesse, proteção do crédito. Para sensíveis: art. 11 exige consentimento específico ou hipóteses específicas (cumprimento de obrigação legal, políticas públicas, estudos, exercício de direitos, tutela da saúde por profissionais, segurança e prevenção a fraude). Pesquise requisitos e expectativas de teste de balanceamento (legítimo interesse art. 10) ou padrão de consentimento; cite controlador.
- **GDPR/UK GDPR** (se aplicável): art. 6 (lawful basis); art. 9 (categorias especiais).
- **CCPA/CPRA**: verifique se algum fluxo é "venda" ou "compartilhamento" no sentido do regime.
- **Setoriais BR:** BCB, ANS, CFM podem ter requisitos específicos para base legal e consentimento.

Verifique atualidade; definições e bases mudam. Sinalize incerteza para revisão por advogado(a).

### Quem e onde

- Quem dentro da empresa vê? Engenharia? Suporte? Analistas?
- Terceiros? Operadores, parceiros, analytics?
- Onde armazenado? Que região? Infra nova ou existente?
- Há **transferência internacional** (LGPD arts. 33-36; Res. CD/ANPD 19/2024)?
- Quanto tempo é mantido? Há plano de eliminação (LGPD art. 16) ou vive para sempre?

### O que pode dar errado

- Se vazasse, qual o dano ao(à) titular?
- Esses dados podem ser usados para discriminar, mesmo sem querer?
- Titulares ficariam surpresos com isso? (O "teste do creepy" — não é padrão jurídico mas é útil.)
- Há opt-out? Deveria ter?
- Se há decisão automatizada: há revisão humana ou possibilidade do art. 20?

## Escrevendo o RIPD

**Use a estrutura do RIPD-semente da config CLAUDE.md.** Se não capturado, use este default. Acrescente cabeçalho de material de trabalho do `## Saídas` (varia por papel — ver `## Quem está usando`).

```markdown
[CABEÇALHO DE MATERIAL DE TRABALHO — por config ## Saídas]

# Relatório de Impacto à Proteção de Dados Pessoais (RIPD): [Nome da Funcionalidade/Produto]

**LGPD art. 38**

**Elaborado por:** [nome] | **Data:** [data] | **Status:** MINUTA / APROVADO
**Responsável do produto:** [nome] | **Encarregado(a) revisor(a):** [nome]

---

## Sumário executivo

[Duas frases: o que é, se está ok. Ex.: "A Funcionalidade X coleta dados de localização para entregar Y. O tratamento é consistente com os compromissos da política de privacidade e usa consentimento como base legal (LGPD art. 7º I). Duas mitigações recomendadas abaixo; nenhum bloqueador identificado."]

**Risco geral:** [🟢 Baixo / 🟡 Médio / 🟠 Alto / 🔴 Muito alto — definido pelo(a) revisor(a)]

---

## 1. Descrição do tratamento

**O quê:** [a funcionalidade em português claro]
**Categorias de dados:** [campos específicos — não "dados de usuário". Indique se há dados sensíveis (LGPD art. 5º II) ou dados de crianças/adolescentes (art. 14)]
**Titulares afetados:** [clientes / usuários finais / colaboradores / etc.]
**Finalidade:** [por quê — atrelar a benefício do(a) titular]
**Nova coleta?** [sim — campos novos / não — reusando dado existente]

---

## 2. Base legal (LGPD art. 7º / art. 11)

| Finalidade | Base legal | Notas |
|---|---|---|
| [finalidade 1] | [Consentimento art. 7º I / Execução de contrato art. 7º V / Legítimo interesse art. 7º IX / Obrigação legal art. 7º II / etc.] | [se LI: resumo do balanceamento art. 10; se consentimento: como foi obtido — granular, livre, informado e inequívoco art. 5º XII; se dados sensíveis: art. 11 inciso correspondente] |

---

## 3. Fluxo de dados

**Coleta:** [como/onde entra]
**Armazenamento:** [sistema, região, criptografia]
**Acesso:** [quem, via quais controles]
**Compartilhamento:** [terceiros, finalidade, regido por qual contrato de tratamento]
**Transferência internacional:** [destino, mecanismo conforme Res. CD/ANPD 19/2024 — cláusulas-padrão, normas corporativas globais, decisão de adequação, ou outro / N/A]
**Retenção:** [por quanto tempo, mecanismo de eliminação — LGPD arts. 15 e 16]

---

## 4. Consistência com a política de privacidade

| Compromisso da política | Consistente? | Notas |
|---|---|---|
| [compromisso da config CLAUDE.md] | 🟢 / 🟡 | |

[Se algum 🟡: atualização de política necessária antes do lançamento, ou tratamento precisa mudar]

---

## 5. Riscos e mitigações

| # | Risco | Probabilidade | Impacto | Mitigação | Status | Responsável |
|---|---|---|---|---|---|---|
| 1 | [risco específico, atrelado ao design — não "vazamento" genericamente] | B/M/A | B/M/A | [controle específico] | Feito / Planejado / Lacuna | [nome] |

**Risco residual após mitigações:** [avaliação]

---

## 6. Direitos do(a) titular (LGPD art. 18)

| Direito | Exercitável? | Como |
|---|---|---|
| Confirmação da existência (art. 18, I) | | |
| Acesso (art. 18, II) | | |
| Correção (art. 18, III) | | |
| Anonimização/bloqueio/eliminação (art. 18, IV) | | |
| Portabilidade (art. 18, V) | | |
| Eliminação de dados consentidos (art. 18, VI) | | |
| Informação sobre compartilhamento (art. 18, VII) | | |
| Revogação do consentimento (art. 18, IX) | | |
| Revisão de decisão automatizada (art. 20) | | |

---

## 7. Recomendação

[APROVADO / APROVADO COM CONDIÇÕES / EXIGE MUDANÇAS / NÃO APROVADO]

**Condições (se houver):**
- [ ] [coisa específica que tem que acontecer antes do lançamento]

**Aprovação:** [nome, data — Encarregado(a) por LGPD art. 41 §2º III]
```

## Padrões de qualidade do risco

Riscos no RIPD devem ser **específicos e atrelados ao design**, não genéricos. Risco genérico enche o documento e treina leitor(a) a passar olhos.

| Risco ruim | Por que ruim | Melhor |
|---|---|---|
| "Vazamento de dados" | Aplica a tudo; diz nada | "Histórico de localização acessível por staff de suporte pelo painel admin sem log de auditoria — insider malicioso pode rastrear titular sem ser detectado" |
| "Não conformidade com a LGPD" | Circular — o RIPD é para *avaliar* conformidade | Nomeie o artigo e a lacuna |
| "Usuários(as) podem não gostar" | Vago | "Titulares que pediram opt-out de marketing podem receber porque a flag não é checada neste fluxo — violação do art. 9º §5º" |

Mire em 2-5 riscos reais, não 15 inflados.

## Diff com a política de privacidade

Todo RIPD deve cruzar com os compromissos em `~/.claude/plugins/config/claude-for-legal/privacidade/CLAUDE.md`. Drift comum:

- Política diz "coletamos X, Y, Z" — nova funcionalidade coleta W. Política precisa atualização ou parar de coletar W.
- Política diz "não vendemos / compartilhamos dados" — nova funcionalidade compartilha com parceiro publicitário. Pode ser hipótese que exige consentimento.
- Política diz "retenção enquanto a conta estiver ativa" — nova funcionalidade mantém após exclusão.

Sinalize todo mismatch. Um dos lados tem que mudar antes do lançamento.

## Handoff

- **Para equipe de produto:** Lista de condições com responsáveis e prazos. Não "melhorar segurança" — "adicionar log de auditoria à consulta de localização no painel admin, responsável: [tech lead], antes do lançamento."
- **Para skill reg-gap-analysis:** Se o RIPD descobriu inconsistência com a política, essa skill cuida do update.
- **Para o processo de aprovação:** Conforme CLAUDE.md → quem aprova RIPD (Encarregado(a)).

## Portão: submeter RIPD à ANPD

Produzir RIPD interno é pesquisa e documentação. *Submeter RIPD à ANPD* — voluntária ou em resposta a fiscalização — é o ato consequente.

**Antes de submeter à ANPD (ou a regulador setorial):** Leia `## Quem está usando` no CLAUDE.md. Se Papel for Não advogado(a):

> Submeter à ANPD tem consequências jurídicas — o documento entra no registro de fiscalização e qualquer omissão ou erro material vira exposição sancionatória (LGPD art. 52). Você revisou com advogado(a)? Se sim, prossiga. Se não, eis um brief para levar a ele(a):
>
> [Gerar resumo de 1 página: regime e regulador, por que submeter (gatilho do art. 38 ou voluntário), riscos identificados, residual após mitigações, incertezas sinalizadas, e as três perguntas para o(a) advogado(a) antes de protocolar.]
>
> Se precisa encontrar advogado(a) habilitado(a) na OAB: a Seccional da OAB do seu estado tem serviço de orientação e referência.

Não passe deste portão sem "sim" explícito.

## Encerre com a árvore de decisão de próximos passos

Termine com a árvore de decisão conforme `## Saídas` no CLAUDE.md. Customize as opções ao que esta skill acabou de produzir — os cinco ramos default (minutar o X, escalar, buscar mais fatos, aguardar, outra coisa) são ponto de partida. A árvore É a saída; o(a) advogado(a) escolhe.

## O que esta skill não faz

- Não aprova o tratamento. Humano(a) assina o RIPD.
- Não escreve relatório formal à ANPD — esse é documento mais formal com requisitos regulatórios específicos. Este é a avaliação interna.
- Não desenha a mitigação. Descreve o que precisa mitigar; engenharia desenha o fix.
