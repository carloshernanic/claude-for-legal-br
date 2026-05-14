---
name: dpa-review
description: >
  Revise um contrato/acordo de tratamento de dados (controlador-operador, ou
  controladores conjuntos) contra seu playbook — detecta automaticamente se
  você é controlador ou operador e aplica a metade certa do playbook. Use
  quando o(a) usuário(a) disser "revise este contrato de tratamento",
  "cheque este DPA", "o cliente mandou o acordo de proteção de dados",
  "este contrato de tratamento está ok", ou anexar um contrato.
argument-hint: "[arquivo | link do Drive | colar texto]"
---

# /dpa-review

1. Carregue `~/.claude/plugins/config/claude-for-legal/privacidade/CLAUDE.md` → playbook de contrato de tratamento. Se placeholders, pare e direcione ao setup.
2. Obtenha o contrato. Determine direção: somos operador (contrato do cliente) ou controlador (do fornecedor)? Pergunte se ambíguo.
3. Rode o workflow abaixo — cláusula por cláusula contra a linha apropriada do playbook.
4. Rode checagem de consistência com a política de privacidade.
5. Saída: memorando de revisão com sugestões de redação (redlines). Salve conforme estilo da casa.

```
/privacidade:dpa-review contrato-tratamento-cliente.pdf
```

---

# Revisão de Contrato de Tratamento

## Contexto de caso

**Contexto de caso.** Verifique `## Workspaces de caso` no CLAUDE.md de nível de prática. Se `Habilitado` for `✗` (padrão para in-house), pule o resto deste parágrafo — skills usam contexto de prática e a maquinaria de caso fica invisível. Se habilitado e não houver caso ativo, pergunte: "De qual caso é? Rode `/privacidade:matter-workspace switch <slug>` ou diga `nível de prática`." Carregue o `matter.md` do caso ativo para contexto e overrides específicos. Escreva saídas na pasta do caso em `~/.claude/plugins/config/claude-for-legal/privacidade/matters/<matter-slug>/`. Nunca leia arquivos de outro caso a menos que `Contexto cruzado entre casos` esteja `on`.

---

## Finalidade

Contratos de tratamento vêm em duas direções e a revisão é quase oposta para cada. Quando o cliente envia o contrato dele, defendemos nossa flexibilidade operacional. Quando enviamos para fornecedor, protegemos nossos dados (e os de nossos clientes). Ambas as revisões leem do mesmo playbook em `~/.claude/plugins/config/claude-for-legal/privacidade/CLAUDE.md` mas em linhas opostas.

**Base jurídica:** LGPD arts. 39-43 — relação controlador-operador; art. 39 §1º (operador segue instruções do controlador); art. 41 (Encarregado(a) como ponto focal); art. 42 (responsabilidade solidária por danos). Para transferência internacional embarcada no contrato, ver LGPD arts. 33-36 + Resolução CD/ANPD 19/2024 (cláusulas-padrão da ANPD).

## Primeiro: qual a direção?

Antes de tudo, estabeleça:

- **Somos o operador** → cliente está enviando o contrato dele → leia `~/.claude/plugins/config/claude-for-legal/privacidade/CLAUDE.md` → tabela "Quando somos o operador"
- **Somos o controlador** → estamos enviando contrato para fornecedor (ou revisando o dele) → leia tabela "Quando somos o controlador"

Se não estiver claro, pergunte. Errar isto inverte cada recomendação.

## Premissa de jurisdição

Esta revisão assume o escopo jurisdicional especificado em sua configuração. Regras de privacidade, prazos de resposta e bases legais variam materialmente por jurisdição (LGPD vs. GDPR vs. regimes setoriais brasileiros — BCB, ANS, CFM). Se o controlador, operador ou titulares estiverem em jurisdição diferente da configurada, esta revisão pode não se aplicar como redigida.

**Especial atenção para tratamento envolvendo elemento internacional:** se há **transferência internacional** (dados saem do Brasil ou entram no Brasil vindos do exterior), aplica-se LGPD arts. 33-36 + Resolução CD/ANPD 19/2024. A ANPD ainda não emitiu decisão de adequação para nenhum país específico em 2026-05 `[verificar]` — confira antes de afirmar adequação.

## Carregar contexto anterior sobre esta contraparte / atividade

Antes de revisar, cheque a pasta de saídas por trabalho anterior sobre esta contraparte ou atividade de tratamento. Leia `~/.claude/plugins/config/claude-for-legal/privacidade/CLAUDE.md` → `## Saídas` para o caminho da pasta. Procure:

- **Triagens anteriores (`use-case-triage`)** para a mesma contraparte / atividade — a triagem produz rating de risco e condicionantes que esta revisão de contrato deve honrar ou explicitamente desviar.
- **RIPDs anteriores (`pia-generation`)** cobrindo esta contraparte / atividade — o RIPD pode ter sinalizado mitigações que o contrato precisa implementar.
- **Revisões anteriores de contrato (`dpa-review`)** para a mesma contraparte — revisões prévias firmaram expectativas sobre o que era aceitável, o que foi sinalizado, o que foi resolvido. Revisão nova que silenciosamente contradiz a anterior erode a confiança no material de trabalho.

Se houver saída anterior, cite na revisão:

> "Triagem anterior ([data]) classificou como [nível de risco] e condicionou aprovação a [X]. Esta revisão é consistente com aquele achado." — ou —
> "Triagem anterior ([data]) classificou como [nível de risco]. Esta revisão diverge porque [motivo — fatos novos, escopo diferente, cláusula que mudou o quadro]."

**Carregue severidade upstream como PISO** conforme regra de piso de severidade entre skills no `~/.claude/plugins/config/claude-for-legal/privacidade/CLAUDE.md` → `## Guardrails compartilhados`. Atividade que a triagem classificou 🔴 não pode ser silenciosamente rebaixada para 🟢 na revisão de contrato; qualquer demoção é declarada e explicada.

Se não houver saída anterior (contraparte/atividade nova), diga explicitamente na revisão — "Sem triagem ou RIPD anterior sobre esta contraparte na pasta de saídas" — para que o(a) advogado(a) revisor(a) saiba que a checagem foi feita.

## Carregar o playbook

Leia `~/.claude/plugins/config/claude-for-legal/privacidade/CLAUDE.md` → `## Playbook de contrato de tratamento`. Leia também `## Compromissos da política de privacidade` — o contrato não pode contradizer o que a política promete.

## Overlay setorial (pergunte primeiro, antes da revisão cláusula por cláusula)

Antes da revisão cláusula por cláusula, responda: **os dados que fluem por este contrato incluem alguma categoria regulada por regime setorial brasileiro?** A LGPD é o piso; regimes setoriais costumam fornecer outro piso que não aparece no playbook genérico. Um contrato que é LGPD-completo ainda pode ser BCB-cego, ANS-cego, ou CFM-cego, e contraparte fintech / healthtech / edtech vai notar.

> **Overlays setoriais — pergunte primeiro:**
>
> Este tratamento toca:
> - **Dados financeiros ou de operações financeiras** (Lei Complementar 105/2001 — sigilo bancário; Resolução BCB 4.658/2018 — segurança cibernética; SCR; Open Finance)? Se sim, o contrato precisa: (a) cláusula de sigilo bancário compatível com LC 105, (b) salvaguardas alinhadas à Resolução BCB 4.658/2018, (c) comunicação de incidente que atinja prazos BCB quando aplicável, (d) tratamento conjunto LGPD + LC 105 sem que um regime aparente excluir o outro.
> - **Dados de saúde detidos por prestador, plano ou profissional de saúde** (CFM Res. 1.821/2007; ANS para operadoras de plano; LGPD art. 11 — base legal específica para sensíveis em saúde; sigilo médico — CP art. 154)? Se sim, o contrato precisa: cláusula de sigilo médico/profissional explícita, base legal específica para sensíveis (art. 11), comunicação de incidente, fluxo de retenção alinhado ao prontuário (Res. CFM 1.821/2007 — 20 anos).
> - **Dados de educação detidos por instituição ou seu prestador** (LDB Lei 9.394/1996 + LGPD)? Cláusula de finalidade restrita, consentimento parental para menores (LGPD art. 14).
> - **Dados de crianças e adolescentes** coletados por serviço dirigido a essa audiência (LGPD art. 14 + ECA — Lei 8.069/1990)? Se sim, o contrato precisa: fluxo de consentimento parental ou base legal alternativa do art. 14 §3º (proteção do melhor interesse), limites de retenção, mecânica de eliminação, vedação a tratamento incompatível com o melhor interesse, alinhamento à Resolução CONANDA 163/2014 (publicidade infantil).
> - **Outro regime setorial** (telecom — LGT 9.472/1997 + Marco Civil da Internet 12.965/2014; dados de empregados — CLT + LGPD; defesa do consumidor — CDC 8.078/1990; antilavagem — Lei 9.613/1998 + COAF)?
>
> Se sim a qualquer: o overlay setorial costuma fornecer a restrição substantiva controladora, não apenas isenção da regra geral. Pesquise a redação vigente e cite. Sinalize gaps setoriais na lista de deal-breakers junto a gaps LGPD.

Se nenhum overlay setorial se aplicar, note explicitamente — "sem categorias setorialmente reguladas; overlay setorial n/a" — para que o(a) advogado(a) revisor(a) veja que a checagem foi feita, em vez de imaginar se foi pulada.

## Revisão cláusula por cláusula

### Cláusulas centrais (cheque em todo contrato)

Passe por cada contrato pelas cláusulas abaixo. As posições numéricas e substantivas *específicas* (prazos de aviso, janelas de comunicação de incidente, pisos aceitáveis/inaceitáveis) vêm do `~/.claude/plugins/config/claude-for-legal/privacidade/CLAUDE.md` → `## Playbook de contrato de tratamento`. Os pisos legais que qualquer contrato precisa atender vêm da lei primária — **pesquise a regra vigente** para cada regime aplicável e cite fontes primárias antes de declarar piso.

> **Sem complementação silenciosa.** Se uma consulta à ferramenta de pesquisa jurídica configurada retornar poucos ou nenhum resultado para janela de comunicação de incidente de um regime, requisito de mecanismo de transferência internacional, regra de troca de suboperador ou outro piso, reporte o que encontrou e pare. NÃO complete o gap a partir de busca web ou conhecimento do modelo sem perguntar. Diga: "A busca retornou [N] resultados em [ferramenta]. Cobertura parece rasa para [regime / tema]. Opções: (1) ampliar a consulta, (2) tentar outra ferramenta, (3) busca web — os resultados serão tagueados `[busca web — verificar]` e devem ser conferidos em fonte primária antes de confiar, ou (4) sinalizar como não verificado e parar. Qual prefere?" Quem decide se aceita fontes de menor confiança é o(a) advogado(a).
>
> **Camadas de atribuição de fonte.** Marque toda citação na revisão — pisos legais, versões de cláusulas-padrão, decisões de adequação, orientações da ANPD, jurisprudência — com sua fonte. Para citações de conhecimento do modelo, use uma das três camadas:
>
> - `[estável — última confirmação YYYY-MM-DD]` — referências legais estáveis improváveis de terem mudado (ex.: LGPD art. 39, LGPD art. 48 — comunicação de incidente em prazo razoável; Resolução CD/ANPD 19/2024 por número; Resolução CD/ANPD 15/2024 — 3 dias úteis). Ainda assim, confira antes de protocolar, mas prioridade menor.
> - `[verificar]` — citações de conhecimento do modelo que são reais mas devem ser verificadas: Resoluções de implementação específicas, orientações da ANPD, decisões do STJ/STF, módulos de cláusulas-padrão, decisões de adequação, datas de vigência.
> - `[verificar-pinpoint]` — citações de pinpoint (parágrafos específicos, alíneas, números de cláusula dentro de cláusulas-padrão, números de processo) carregam o maior risco de fabricação e devem SEMPRE ser verificadas contra fonte primária.
>
> Citações recuperadas de ferramenta mantêm sua tag de fonte (`[ANPD]`, `[Planalto]`, `[STJ]`, `[STF]`, `[JusBrasil]`, ou o nome da ferramenta MCP); citações de busca web ficam `[busca web — verificar]`; citações fornecidas pelo(a) usuário(a) ficam `[fornecido pelo usuário]`. As camadas trazem à tona o trabalho real de verificação — quem verifica tudo não verifica nada. Nunca remova nem colapse as tags.

| Cláusula | O que procurar | Campo do playbook | Brigas comuns |
|---|---|---|---|
| **Papéis** | Designação clara controlador/operador (LGPD art. 5º VI/VII); coincide com a realidade | — | Contraparte rotula a relação (ex.: "controladores conjuntos" art. 5º XIX) de forma que não corresponde à realidade |
| **Escopo do tratamento** | Limitado a instruções documentadas; finalidades definidas (LGPD art. 39 §1º) | — | Expansores de escopo abertos ("e finalidades correlatas") |
| **Suboperadores** | Lista atual divulgada, mecanismo de mudança definido | Mudanças de suboperador | Aprovação em bloco vs. veto vs. apenas aviso |
| **Medidas de segurança** | Anexo referencia controles específicos ou normas | Padrões de segurança | "medidas técnicas e administrativas apropriadas" sem anexo = promessa vazia (LGPD art. 46) |
| **Comunicação de incidente** | Gatilho definido ("descoberta" vs "confirmação"), prazo definido | Comunicação de incidente | Aperto do prazo; gatilho do relógio; "em prazo razoável" é vago. **LGPD art. 48 + Resolução CD/ANPD 15/2024: 3 dias úteis** para ANPD; aos titulares "em prazo razoável" definido pela ANPD. |
| **Direito de auditoria** | Método (relatório vs. presencial), frequência, aviso, alocação de custos | Direito de auditoria | Auditoria presencial com aviso curto |
| **Transferência internacional** | Mecanismo de transferência identificado, medidas suplementares, referência à análise de transferência | Transferência internacional | Mecanismos desatualizados ou ausentes. **LGPD arts. 33-36 + Resolução CD/ANPD 19/2024** — verificar se há decisão de adequação (a ANPD ainda não emitiu para nenhum país em 2026-05 `[verificar]`), cláusulas-padrão da ANPD, BCR aprovadas, ou hipóteses do art. 33 (necessidade contratual, autorização do titular, cooperação jurídica, etc.). |
| **Eliminação/devolução** | Prazo pós-rescisão, certificação, ressalva de backup | Eliminação ao término | "Eliminação em prazo comercialmente razoável" = ??? (LGPD art. 16 — hipóteses limitadas de conservação) |
| **Responsabilidade** | Dentro do cap do contrato principal ou separado; ressalvas | Responsabilidade por dados | **Responsabilidade solidária (LGPD art. 42)** — operador responde solidariamente quando descumprir LGPD ou instruções do controlador; cap por dados sem ressalva pode ser ineficaz contra titular. |

### Quando somos o operador: revisão defensiva

Contratos do cliente tentam empurrar carga operacional para nós. Para cada cláusula abaixo, compare o pedido do cliente ao playbook. Onde o pedido está fora do playbook, oponha a posição padrão do time (do CLAUDE.md de configuração) e esteja pronto para recuar à posição aceitável.

| Cláusula | Risco | Pesquisa / consulta ao playbook |
|---|---|---|
| Direito de aprovação (veto) de suboperador | Não conseguimos adicionar infraestrutura sem aprovação cliente-a-cliente | Aplique a posição do playbook sobre mudanças de suboperador |
| Auditoria presencial com aviso curto | Inviável em escala | Aplique a posição do playbook sobre direito de auditoria |
| Janela agressiva de comunicação de incidente | Frequentemente exige aviso antes de sabermos o que aconteceu | Pesquise o piso legal para cada regime aplicável (cite fontes primárias — LGPD art. 48 + Resolução CD/ANPD 15/2024: 3 dias úteis para ANPD); compare à posição do playbook |
| Residência rígida de dados (país/DC único) | Pode não combinar com a arquitetura | Aplique a posição do playbook sobre localização; confirme o que de fato podemos comprometer. Se exige residência no Brasil, considere se isto evita transferência internacional regulada pelos arts. 33-36 |
| Responsabilidade do operador sem cap | Bet-the-company | Aplique a posição do playbook sobre responsabilidade por dados. Atenção: cap não afasta solidariedade do art. 42 LGPD perante o titular |
| Cliente pode emitir "instruções" vinculantes abertas | Controle operacional aberto | Defina instruções como "documentadas no Acordo ou acordadas por escrito" (LGPD art. 39 §1º — instruções do controlador) |
| Eliminação em prazo muito curto | Rotação de backup e retenção de logs torna isto impossível | Aplique a posição do playbook sobre eliminação ao término; documente ressalva de rotação de backup. Atenção ao art. 16 LGPD — hipóteses limitadas de conservação após término |

### Quando somos o controlador: revisão protetiva

Contratos de fornecedor tentam não nos dar nada. Para cada cláusula abaixo, compare ao playbook do lado controlador.

| Cláusula | Gap | Pesquisa / consulta ao playbook |
|---|---|---|
| Sem lista de suboperadores | Não sabemos quem toca em nossos dados | Exija lista atual publicada + aviso prévio conforme playbook |
| "Segurança padrão da indústria" | Não significa nada | Exija anexo com controles específicos ou referência a norma (ex.: ABNT NBR ISO/IEC 27001, NIST CSF, Resolução BCB 4.658/2018 se setor financeiro) |
| Sem prazo de comunicação de incidente | Avisam quando quiserem | Pesquise piso regulatório aplicável (LGPD art. 48 + Resolução CD/ANPD 15/2024); exija posição do playbook |
| Sem direito de auditoria algum | Não conseguimos verificar nada | Exija no mínimo relatório de auditoria independente conforme playbook |
| Fornecedor pode usar dados para "melhoria do serviço" | Risco de treinamento em nossos dados | Exclua; tratamento limitado à prestação do serviço a nós (LGPD art. 39 §1º) |
| Sem mecanismo de transferência internacional | Sem hipótese legal para transferência | **Pesquise o mecanismo de transferência vigente** para o corredor em questão (origem/destino, regime aplicável — LGPD arts. 33-36 — Resolução CD/ANPD 19/2024 — decisão de adequação ou cláusulas-padrão da ANPD ou BCR). Cite fontes primárias e verifique atualidade. |
| Sem compromisso de eliminação | Dados vivem para sempre | Exija posição do playbook sobre eliminação + certificação sob demanda (LGPD art. 16) |

## Checagem de consistência: política de privacidade

O contrato que você assina não pode prometer algo que a política não cobre, e vice-versa.

- Se o contrato compromete a tratar apenas para finalidades X, Y, Z — a política de privacidade lista essas finalidades?
- Se a política diz "não compartilhamos dados" — alguma cláusula do contrato parece compartilhamento sob LGPD?
- Se a política nomeia categorias específicas de suboperadores — a lista do contrato bate?

Sinalize incompatibilidades. Costumam ser política desatualizada, não o contrato errado, mas alguém precisa corrigir um dos dois.

## Granularidade do redline

**Edite na menor granularidade possível.** Redline é artefato de negociação, não reescrita. Substituição em bloco de cláusula sinaliza "jogamos sua redação fora" — é agressivo, força a contraparte a reler a cláusula inteira, e descarta as partes que estavam ok. Redlines cirúrgicos — riscar palavra, inserir frase, reestruturar subitem — sinalizam "temos pedidos específicos" e são mais rápidos de ler, entender, aceitar.

Por padrão, a menor edição que atinge a posição do playbook:
- Substitua uma **palavra** antes de uma frase. ("doze (12)" → "vinte e quatro (24)")
- Substitua uma **frase** antes de uma sentença. ("paga pelo Comprador" → "paga e devida pelo Comprador")
- Reestruture um **subitem** antes de substituir a sentença. (Adicione "(a)" e "(b)" para separar condição composta.)
- Substitua uma **sentença** antes da cláusula inteira.
- Só substitua uma **cláusula inteira** quando a versão da contraparte estiver tão longe da sua posição que edições cirúrgicas seriam mais difíceis de ler do que uma redação nova — e, quando fizer, diga na carta de envio: "Substituímos o §8.2 em vez de marcar porque as alterações foram extensas. Disponível para explicar o delta."

Em dúvida, menor. Cliente que recebe redline cirúrgico confia que você leu com cuidado. Cliente que recebe substituição em bloco fica em dúvida se você leu.

## Saída

Prepende o cabeçalho de material de trabalho do `~/.claude/plugins/config/claude-for-legal/privacidade/CLAUDE.md` `## Saídas` (varia por papel — veja `## Quem está usando`).

```markdown
[CABEÇALHO DE MATERIAL DE TRABALHO — conforme config do plugin ## Saídas]

# Revisão de Contrato de Tratamento: [Contraparte]

**Direção:** [Somos operador / Somos controlador]
**Revisado em:** [data]
**Anexo a:** [Contrato principal / autônomo]

---

## Bottom line

[Duas frases. Podemos assinar? O que tem que mudar?]

**Achados:** [N]🟢 [N]🟡 [N]🟠 [N]🔴

---

## Cláusula por cláusula

[Para cada cláusula central, use um formato padrão de memorando de desvio: o que o contrato da contraparte diz, o que nosso playbook diz, o gap, o risco e a redação de redline proposta. Mantenha cada cláusula como bloco autocontido curto para o(a) revisor(a) ler em diagonal.]

---

## Consistência com a política de privacidade

[🟢 Consistente | 🟡 Flags: liste]

---

## Redlines recomendados

[Consolidado — pronto para devolver]

---

## Se não cederem

[Para cada questão: o fallback do CLAUDE.md de configuração, ou roteamento de escalonamento se não houver fallback]
```

## Nota sobre transferência internacional

Se o contrato contempla transferência internacional, **pesquise os requisitos de mecanismo de transferência vigentes** para o(s) corredor(es) aplicável(is). Para cada par origem/destino, identifique: o regime aplicável, se há decisão de adequação da ANPD em vigor (em 2026-05 a ANPD ainda não emitiu adequação para nenhum país `[verificar]`), qual mecanismo é exigido ou disponível (cláusulas-padrão da ANPD pela Resolução CD/ANPD 19/2024; cláusulas contratuais específicas conforme art. 35 §4º; BCR — regras corporativas globais; selos, certificados e códigos de conduta; cooperação jurídica internacional; necessidade contratual; autorização do titular — art. 33), se análise de transferência é exigida, e quais medidas suplementares podem ser necessárias. Cite fontes primárias (LGPD arts. 33-36, Resolução CD/ANPD 19/2024, orientação da ANPD, jurisprudência) com pinpoints e verifique atualidade — decisões de adequação, versões de cláusulas-padrão e medidas suplementares mudam por novas Resoluções do CD/ANPD, decisões judiciais e orientações. Sinalize incerteza para verificação por advogado(a).

Se mecanismo de transferência estiver ausente e há transferência internacional, isto é 🔴 — não há hipótese lícita de transferência.

## Gate: assinar um contrato de tratamento

Revisar um contrato é pesquisa. *Assinar* — ou instruir alguém a assinar em nosso nome — é o ato consequente.

**Antes de prosseguir para assinar ou contra-assinar contrato de tratamento (incluindo devolver versão executada, consentir execução automática em plataforma da contraparte, ou instruir signatário a executar):** Leia `## Quem está usando` no `~/.claude/plugins/config/claude-for-legal/privacidade/CLAUDE.md`. Se o papel for Não advogado(a):

> Assinar um contrato de tratamento é ato jurídico — vincula a empresa a obrigações específicas de proteção de dados que fluem para reguladores (ANPD) e titulares. Você revisou isto com um(a) advogado(a) inscrito(a) na OAB? Se sim, prossiga. Se não, segue brief para levar a ele(a):
>
> [Gere um resumo de 1 página: contraparte, direção (somos operador / controlador), as cláusulas que desviam do playbook e como foram resolvidas, qualquer decisão de fallback em aberto, e as três coisas para perguntar ao(à) advogado(a) antes de executar.]
>
> Se você precisa encontrar advogado(a) inscrito(a) na OAB para revisão: a Seccional da OAB do seu estado mantém serviços de busca; muitas Subsecções oferecem orientação gratuita inicial. Para pequenas e médias empresas, considere também o SEBRAE Jurídico (parcerias estaduais). Para pessoas físicas com hipossuficiência, Defensoria Pública estadual ou Núcleos de Prática Jurídica (NPJ) universitários.

Não prossiga além deste gate sem um "sim" explícito.

## Encerre com a árvore de decisão de próximos passos

Encerre com a árvore de decisão de próximos passos conforme CLAUDE.md `## Saídas`. Customize as opções ao que esta skill produziu — as cinco ramificações padrão (minutar X, escalar, buscar mais fatos, aguardar, outra coisa) são ponto de partida, não trava. A árvore é a saída; o(a) advogado(a) escolhe.

## O que esta skill NÃO faz

- Não minuta um contrato de tratamento do zero. Se a resposta é "use nosso template", puxe o template do caminho de documentos-semente no CLAUDE.md de configuração.
- Não faz a análise de transferência internacional propriamente dita — sinaliza quando uma é necessária.
- Não decide se aceita cláusulas fora dos fallbacks. Roteia conforme a tabela de escalonamento.
