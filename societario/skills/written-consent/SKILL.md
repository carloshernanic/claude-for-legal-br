---
name: written-consent
description: >
  Minutar deliberação por escrito do conselho de administração, da diretoria ou
  de comitê no formato da casa, com busca de precedente no repositório de
  deliberações. Trata múltiplas deliberações, conflitos de administrador,
  exigências legais de convocação/quórum e tracking de assinaturas, com aviso
  de escopo para atos relevantes one-off. Use quando o(a) usuário(a) disser
  "deliberação por escrito", "deliberação unânime", "deliberação do conselho",
  "deliberação em lugar de reunião", ou descrever ato que precisa de aprovação
  do conselho sem reunião.
argument-hint: "[descreva o ato que precisa de aprovação do conselho]"
---

# /written-consent

> **Adaptação BR não oficial.** Calibrado para deliberações brasileiras: S.A. (Lei 6.404/76 — note que o art. 121 par. único admite deliberação por escrito em S.A. fechada em hipóteses limitadas; CA não é restrito), Ltda. (CC art. 1.072 §3º admite amplamente — dispensa reunião se todos os sócios decidirem por escrito). Veja `references/terminology-us-to-br.md`. Toda saída é rascunho para revisão por advogado(a) inscrito(a) na OAB.

1. Carregue `~/.claude/plugins/config/claude-for-legal/societario/CLAUDE.md` → Conselho & Secretaria (repositório de deliberações, linguagem, tipo societário, composição).
2. Use o fluxo abaixo.
3. Identifique o ato e classifique (rotineiro / sinalizar revisão).
4. Se sinalizar revisão: mostre aviso de advogado(a) externo(a) e confirme.
5. Busque no repositório o precedente mais próximo. Sem repositório: use docs seed de `CLAUDE.md`.
6. Minute no formato da casa usando o precedente como base.
7. Output: minuta + checklist de assinatura + prompts de revisão.

---

## Contexto de matéria

**Contexto de matéria.** Veja `## Workspaces de matéria` no CLAUDE.md nível-prática. Se `Habilitado` for `✗` (padrão in-house), pule. Se habilitado e sem matéria, pergunte: "Qual matéria? Rode `/societario:matter-workspace switch <slug>` ou diga `nível-prática`." Carregue `matter.md`. Grave na pasta da matéria. Nunca leia outras a menos que `Contexto cross-matter` esteja `on`.

---

## Propósito

A maioria das aprovações rotineiras do conselho/diretoria/sócios não precisa de reunião. Eleição de diretores não-estatutários, outorgas de opções, autorizações bancárias, aprovações de contrato acima da alçada da diretoria, arranjos intercompany — tudo isso pode acontecer por deliberação por escrito (quando o regime societário admitir). Esta skill minuta rapidamente no formato da casa, encontra o precedente mais próximo no repositório e sinaliza atos que merecem revisão de advogado(a) externo(a) antes de qualquer assinatura.

## Aviso de escopo — leia antes de minutar

> **Esta skill foi feita para deliberações do dia-a-dia com precedentes diretos no repositório.** Atos rotineiros — nomeações, outorgas, autorizações anuais, aprovações padrão — são o uso correto. A skill encontra deliberação anterior próxima, adapta e produz minuta limpa.
>
> **Para atos relevantes one-off, revisão de advogado(a) externo(a) é prudente independentemente do que esta skill produz.** Inclui: M&A (compra/venda de ativos, incorporação, cisão, fusão, investimento), rodadas de financiamento, emissão de ações/quotas a novos investidores, cláusulas de mudança de controle, dissolução/baixa, transações imobiliárias materiais, e qualquer ato que será escrutinizado em diligência subsequente.
>
> A skill sinaliza automaticamente quando o ato parece relevante one-off. A sinalização não bloqueia — pode prosseguir. É um prompt para pensar se minuta de precedente adaptado é suficiente.

---

## Ato relevante + urgência = pare

Uma deliberação do conselho sobre ato relevante one-off (M&A, financiamento, dissolução, mudança de estrutura de capital, eleição vinculada a financiamento ou M&A) que o(a) usuário(a) quer assinada HOJE — "envie para assinatura à tarde", "reunião em uma hora", "assinatura hoje à noite", "precisamos antes da abertura" — passa por revisão de advogado(a) externo(a). Não porque o plugin não consiga minutar — porque deliberação errada em ato relevante é porta one-way, e a pressão da urgência é exatamente quando erros acontecem.

Gatilho (ambos verdadeiros):

1. O ato está na categoria **Sinalizar — relevante one-off** abaixo (M&A, financiamento, dissolução, mudança de estrutura de capital, cláusula de mudança de controle, eleição vinculada a financiamento ou M&A, transação imobiliária material, qualquer ato que aparecerá em sala de dados de financiamento ou M&A futuro).
2. Pedido contém sinal de irreversibilidade — "envie para assinatura", "assinar hoje", "conselho assina hoje à noite/à tarde", "precisamos antes [da abertura / do fechamento / da reunião X]", qualquer frase que compromete a deliberação à assinatura no mesmo turno.

Quando ambos verdadeiros, mostre e pare:

> ⛔ **Ato relevante + assinatura same-day — não marco como pronto para assinar.**
>
> Isto é [tipo de ato], porta one-way. Você pediu para ser assinado hoje. Essa combinação é exatamente quando erros em deliberação ficam mais difíceis de desfazer.
>
> Eu minuto — com prazer — mas não marco como pronto para assinar sem olho de advogado(a) externo(a). Se advogado(a) externo(a) já está engajado(a) no deal, entregue esta minuta. Se não, isto é para o que advogado(a) externo(a) existe. A OAB de sua seccional oferece busca de inscritos (CNA) que pode achar advogado(a) no mesmo dia.
>
> Dois caminhos:
>
> 1. **Eu minuto, advogado(a) externo(a) revisa, depois assinaturas** — caminho normal. Mande minutar e eu minuto.
> 2. **Advogado(a) externo(a) já está no deal e liberou o caminho de minuta** — me diga quem revisou e quando. Prossigo com nota de que advogado(a) externo(a) tem a minuta.
>
> Não minutarei em forma "pronta para enviar" sob pressão same-day sem um dos dois. Não é atraso — é o único modo de uma deliberação same-day sobre ato relevante ser defensável se alguém olhar o arquivo depois.

Não prossiga ao Passo 1 ou qualquer minuta sob este gate sem resposta explícita escolhendo caminho 1 ou 2. Deliberação rotineira sem gatilho de ato relevante, ou ato relevante sem assinatura same-day, segue o fluxo normal — a sinalização "revisão de advogado(a) externo(a) recomendada" se aplica, mas não bloqueia.

---

## Carregue contexto

- `~/.claude/plugins/config/claude-for-legal/societario/CLAUDE.md` → `## Conselho & Secretaria`:
  - Local do repositório de deliberações
  - Linguagem das deliberações (DELIBERA-SE / RESOLVE-SE / FICA APROVADO)
  - Tipo societário (Lei 6.404/CC) e estado de registro
  - Composição do conselho/diretoria (lista de assinantes)
  - Deliberações por escrito — escopo e limites

### Hard stop sem precedente

Se (a) nenhum repositório configurado em `## Conselho & Secretaria` → Repositório de deliberações, E (b) nenhum documento seed fornecido a esta skill (uploadado na sessão ou referenciado em `## Conselho & Secretaria` → Formato de deliberação com linguagem extraída), **PARE antes de minutar**. Não prossiga para Passo 1, não minute de template genérico.

Mostre exatamente este bloco e aguarde:

> **Sem precedente — parando antes da minuta.**
>
> Não tenho precedente. Deliberação minutada sem o formato da casa exigirá mais correção do que poupa — linguagem das deliberações, profundidade de considerandos, boilerplate de autorização e convenções de assinatura têm escolhas casa-específicas que o(a) revisor(a) reescreverá do zero.
>
> Dois caminhos para desbloquear:
>
> 1. **Cole ou uploade uma deliberação anterior** (qualquer recente da empresa, em qualquer categoria — extraio formato, não substância), OU
> 2. **Diga "minute de template genérico mesmo — ajusto as formalidades à mão"** — só escolha se souber que vai retrabalhar a linguagem, considerandos e bloco de autorização à mão antes da circulação. Diga explicitamente; não infiro.
>
> Qual quer fazer?

Não prossiga sem resposta explícita escolhendo um caminho.

---

## Passo 1: Identifique o ato

Pergunte que ato precisa de aprovação:

- **O que será aprovado?** (Uma frase.)
- **Detalhes?** Ex.: nome do(a) diretor(a) a ser eleito(a), valor e preço de outorga, contraparte e valor de contrato.
- **Data de efeitos:** Hoje, ou específica?
- **Assinantes:** Conselho pleno, diretoria, ou comitê específico? Se `CLAUDE.md` escopo de deliberação diz que certos atos exigem reunião em vez de deliberação por escrito, sinalize agora.
- **Conflito de administrador?** Algum administrador tem interesse particular ou conflitante no ato (art. 156 Lei 6.404 — administrador; art. 1.011 §1º CC — Ltda.)? Se sim: sinalize. Administrador conflitado deve abster-se de votar/deliberar; deliberação deve registrar a manifestação.

### Classificação do ato

**Rotineiro — precedente direto provável:**
- Eleição ou destituição de diretor(a) (não-estatutário(a))
- Outorga de opção/RSU/ação restrita a participantes do plano (Res. CVM 86 para companhia aberta — divulgação)
- Autorização de conta bancária ou substituição de signatário
- Aprovação de contrato abaixo de threshold de materialidade
- Autorizações anuais (matérias tributárias, planos de benefícios)
- Mútuo ou prestação de serviços intercompany em condições de mercado (atenção a preço de transferência — Lei 14.596/2023)
- Substituição de despachante/endereço

**Sinalizar — relevante one-off, advogado(a) externo(a) prudente:**
- Operação de M&A (incorporação, fusão, cisão — arts. 227-229 Lei 6.404; aquisição, investimento)
- Nova rodada de financiamento ou debênture
- Emissão de ações/quotas a novo investidor (atenção a direito de preferência — art. 109 Lei 6.404; art. 1.081 CC)
- Cláusula ou gatilho de mudança de controle (atenção a tag-along — art. 254-A Lei 6.404 — 80% para acionistas com direito a voto em alienação de controle de S.A. aberta)
- Aprovação de acordo que ele mesmo exige aprovação do conselho/assembleia conforme estatuto, acordo de acionistas (art. 118 Lei 6.404) ou Lei 6.404 art. 256 (alienação de controle de controlada)
- Dissolução, baixa ou recuperação judicial/extrajudicial (Lei 11.101/2005)
- Transação imobiliária material
- Qualquer ato que aparecerá como aprovação em sala de dados futura
- Operação com parte relacionada (matéria do art. 116 Lei 6.404 + Res. CVM 81 para companhia aberta — divulgação obrigatória)
- Distribuição de dividendos divergente do estatuto ou abaixo do mínimo obrigatório (art. 202 Lei 6.404)

Se na categoria sinalizar, mostre antes de minutar:

> ⚠️ **Revisão de advogado(a) externo(a) recomendada.** Isto parece [tipo de ato], ato societário relevante onde minuta de precedente adaptado pode não bastar. Considere revisão de advogado(a) externo(a) antes da circulação. Quer que eu minute mesmo assim?

---

## Passo 2: Busque precedente

### Se repositório conectado

Busque deliberação mais próxima.

1. Busque por palavra-chave do tipo de ato (ex.: "eleição de diretor", "outorga de opção", "autorização bancária")
2. Retorne mais recente, ou peça que escolha se houver vários:

> Achei [N] deliberações que parecem com isto:
>
> 1. [Título / descrição] — [Data]
> 2. [Título / descrição] — [Data]
>
> Qual é mais próxima? Ou uso a mais recente?

3. Leia a selecionada. Extraia: linguagem das deliberações, considerandos, autorização, condições/carve-outs.
4. Note diferenças entre ato anterior e atual.

### Sem repositório (apenas docs seed)

Extraia o formato dos docs seed em `CLAUDE.md`. Note que não há busca de precedente — minuta segue formato da casa sem casamento substantivo. Sinalize:

> Repositório não conectado, trabalho com docs seed para formato. Para este tipo de ato especificamente, vale checar se há deliberação anterior para usar como ponto de partida substantivo.

---

## Passo 3: Minute a deliberação

Use formato da casa. Estrutura abaixo é padrão — adapte ao precedente ou seed.

```
DELIBERAÇÃO POR ESCRITO
[DO CONSELHO DE ADMINISTRAÇÃO / DA DIRETORIA / DO [COMITÊ]]
DE [DENOMINAÇÃO DA COMPANHIA]
CNPJ: [CNPJ] · NIRE: [NIRE]

[Data]

Os abaixo assinados, [conselheiros/diretores/membros do Comitê] da [Denominação], sociedade [anônima / por quotas de responsabilidade limitada], inscrita no CNPJ sob o nº [...], NIRE [...], com sede em [...] (doravante "Companhia"), DELIBERAM, por escrito e em lugar de reunião, nos termos do [art. 121 par. único Lei 6.404/76 / art. 1.072 §3º Código Civil / dispositivo do Estatuto Social ou Acordo de Acionistas aplicável], sobre as seguintes matérias:

[HEADING DO ATO — se múltiplas deliberações]

CONSIDERANDO que [contexto factual — uma ou duas frases declarando os fatos relevantes e por que se delibera]; e

CONSIDERANDO que [considerando adicional se necessário]; e

DELIBERA-SE [ou RESOLVE-SE / FICA APROVADO — use a linguagem da casa] [a ação específica em linguagem precisa — nomeie nomes, declare valores, referencie o instrumento];

DELIBERA-SE AINDA que [deliberação relacionada ou de execução — ex.: diretores específicos autorizados a assinar, alçada outorgada];

DELIBERA-SE AINDA que ficam os diretores da Companhia, individualmente, autorizados a praticar todos os atos, assinar documentos, instrumentos, certidões e acordos que se fizerem necessários ou convenientes para a consumação das deliberações acima; e

DELIBERA-SE AINDA que ficam ratificados, confirmados e aprovados em todos os seus termos os atos previamente praticados por administradores da Companhia em conexão com as matérias ora deliberadas.

[Repita CONSIDERANDO / DELIBERA-SE para cada ato adicional em deliberação multi-resolução]

A presente deliberação por escrito poderá ser firmada em uma ou mais vias, todas reputadas originais e em conjunto constituindo um único instrumento. Assinaturas eletrônicas são consideradas originais para todos os fins, nos termos da MP 2.200-2/2001 (ICP-Brasil) e/ou Lei 14.063/2020.

[BLOCOS DE ASSINATURA — um por signatário]

_______________________________
[Nome do(a) administrador(a)]
[Cargo]
Data: _______________

[Repita para cada conselheiro(a) / membro do comitê / sócio(a)]
```

### Notas de redação

- **Seja preciso.** Deliberação vaga gera problema em diligência. "Aprovou a transação" é inútil. "Aprova o Contrato de Compra e Venda de Quotas datado de [data] entre [Vendedor] e a Companhia, substancialmente na forma do Anexo I" é.
- **Nomeie diretores autorizados.** Não diga só "diretores" se precisa de autoridade específica para ato específico.
- **Referencie anexos.** Se documento sendo aprovado, anexe e referencie. Deliberação só vale o que sua especificidade.
- **Case a linguagem da casa exatamente.** "DELIBERA-SE", "RESOLVE-SE", "FICA APROVADO" — use o que está no precedente ou seed. Não troque formato dentro da mesma deliberação.

---

## Passo 4: Confirme as regras de deliberação para o tipo societário

Verifique tipo societário em `CLAUDE.md`. Pesquise as exigências de deliberação por escrito antes de minutar:

- Unanimidade exigida ou quórum menor admitido? (Para CA — depende do estatuto. Para Ltda. — todos os sócios por escrito conforme CC art. 1.072 §3º. Para AGE de S.A. — mais restrita, deliberação por escrito não substitui em hipóteses gerais; AGO presencial ou virtual sob regimento.)
- Convocação a não-signatários exigida? Em que prazo?
- Forma válida de assinatura (manuscrita, eletrônica, contrapartes)? — MP 2.200-2/2001 (ICP-Brasil) ou Lei 14.063/2020 (eletrônica simples/avançada). DREI IN 81/2020 admite eletrônica para Junta Comercial.
- Estatuto ou contrato social anula regra default — quórum maior, prazo de convocação diferente, restrição de matérias que podem ser deliberadas por escrito?
- Para companhia aberta: matéria gera dever de divulgação (Res. CVM 44 — fato relevante; Res. CVM 80 — periódicos; Res. CVM 81 — partes relacionadas)? Arquivamento na Junta em 30 dias (art. 289 Lei 6.404) se ato societário a ser publicado.

Cite dispositivo legal e qualquer dispositivo do estatuto/contrato social invocado. Verifique vigência — Lei 6.404 e CC são alterados regularmente. Sinalize incerteza para verificação por advogado(a).

Se `CLAUDE.md` registra posição da casa, aplique e note o respaldo legal. Adicione bloco curto "Regime legal aplicável" no output sumarizando o que confirmou (ou sinalizou).

---

## Passo 4.5: Gate de ação consequente (executar deliberação)

**Antes de produzir output:** Leia `## Quem está usando` em `CLAUDE.md`. Se Papel for **Não-advogado(a)**:

> Executar deliberação por escrito tem consequências jurídicas — vincula a entidade e vira registro societário (com arquivamento na Junta Comercial em 30 dias quando exigido — art. 289 Lei 6.404). Você revisou com advogado(a)? Se sim, prossiga. Se não, eis briefing:
>
> - O que é o ato (a deliberação)
> - O que a análise achou (regime legal, quórum, conflitos sinalizados)
> - Questões em aberto (qualquer item sinalizado para verificação)
> - O que pode dar errado (deliberação inválida, violação de dever fiduciário do administrador — art. 153-159 Lei 6.404, vício de assinatura, conflito mal tratado, dever de divulgação CVM omitido, prazo de Junta perdido)
> - O que perguntar ao(à) advogado(a) (este é o veículo certo; faltam considerandos; estatuto/CC admite deliberação por escrito para este ato; precisa de arquivamento ou publicação)
>
> Para localizar advogado(a): consulte a OAB de sua seccional (CNA — Cadastro Nacional dos Advogados).

Não produza minuta final pronta para assinatura após este gate sem "sim" explícito. Pesquisa, extração de formato, e MINUTA marcada para revisão de advogado(a) estão ok.

---

## Passo 5: Output

Produza:

1. **A minuta** — completa, pronta para revisar e circular. A deliberação executada é registro societário, não privilegiada; não aplique cabeçalho de material de trabalho à deliberação circulada. As notas, checklist e análise abaixo são material de trabalho — anteponha o cabeçalho de `CLAUDE.md` `## Outputs`:

   ```
   [CABEÇALHO DE MATERIAL DE TRABALHO — conforme config ## Outputs — varia por papel]
   ```

2. **Checklist de assinaturas:**
```
[CABEÇALHO DE MATERIAL DE TRABALHO]

CHECKLIST DE ASSINATURAS — [Ato] — [Data]

Signatários obrigatórios:
□ [Nome 1]
□ [Nome 2]
□ [Nome 3]
[etc. — puxado da composição em CLAUDE.md]

Conflitos:
[Nenhum / [Nome] declarou interesse — confirme abstenção ou disclosure adequados (art. 156 Lei 6.404 / art. 1.011 CC)]

Regime legal aplicável: [dispositivo confirmado para o tipo societário / confirme]
Arquivamento Junta Comercial: [necessário em 30 dias / não aplicável — art. 289 Lei 6.404]
Publicação: [DOU + jornal / IPE/CVM / dispensada para S.A. fechada com receita < R$ 78mi conforme art. 294]
```

3. **Prompts de revisão:**
```
[CABEÇALHO DE MATERIAL DE TRABALHO]

ANTES DE CIRCULAR — verifique:
□ Linguagem das deliberações descreve o ato com precisão (sem aprovações vagas)
□ Data de efeitos correta
□ Anexos exigidos anexados e referenciados
□ Diretores autorizados nomeados corretamente
□ Conflitos de administrador declarados ou resolvidos
□ Para atos relevantes: advogado(a) externo(a) revisou
□ Para companhia aberta: avaliar dever de divulgação (Res. CVM 44; Res. CVM 81 — partes relacionadas)
□ Plano de arquivamento na Junta Comercial se necessário (prazo 30 dias)
```

4. **Nota final na minuta — antes da circulação.** Anteponha como nota separada pré-execução, depois remova antes da assinatura:

> Esta é minuta para revisão de advogado(a), não deliberação executada. Executar vincula a entidade e vira registro societário — advogado(a) inscrito(a) na OAB revisa, edita conforme necessário, e assume responsabilidade profissional antes da circulação. Não circular para assinatura sem revisão.

---

## O que esta skill não faz

- Não determina se o ato exige juridicamente aprovação do conselho/diretoria/sócios — juízo do(a) advogado(a).
- Não aconselha sobre deveres fiduciários do administrador (arts. 153-159 Lei 6.404; arts. 1.011 e 1.016 CC) ou resolução de conflito — sinaliza, advogado(a) trata.
- Não substitui revisão de advogado(a) externo(a) para atos relevantes — aviso de escopo é genuíno.
- Não circula a deliberação — output é para advogado(a) revisar e enviar pelo próprio processo.
- Não rastreia assinaturas retornadas — checklist é ponto de partida; tracking é manual ou via gestão documental.
- Não protocola na Junta Comercial — execução é manual ou via despachante.
