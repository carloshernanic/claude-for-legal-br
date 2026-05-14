---
name: clearance
description: >
  Busca de anterioridade marcária — primeira passada (knockout + marcas
  similares) que produz lista de flags, NÃO parecer de viabilidade. Use
  quando uma marca nova é proposta, quando se pergunta se a marca está
  disponível ou para rodar um knockout, ou ao avaliar fatores de
  colidência antes de busca profissional completa. Esta skill NUNCA
  conclui que uma marca está livre.
argument-hint: "[descreva a marca, produtos/serviços e jurisdições — ou só a marca e eu pergunto]"
---

# /clearance

> **Nota de adaptação BR.** Adaptada do original norte-americano (TESS/USPTO; testes du Pont/Polaroid/Sleekcraft). Aqui se aplica o regime da **LPI (Lei 9.279/96, arts. 122–175)** — busca no **INPI** via busca.inpi.gov.br + Comissão de Classificação Nice (NCL); colidência avaliada pelos critérios consolidados na jurisprudência do **STJ** (semelhança gráfica/fonética/ideológica + afinidade de produtos/serviços). Quando jurisdições estrangeiras estão em escopo (via **Protocolo de Madri** — Brasil aderente desde Dec. 10.033/2019, vigência 02/10/2019), os testes locais aplicáveis prevalecem.

**Esta é uma triagem, não um parecer de viabilidade.** Parecer exige busca profissional completa (incluindo composições, classes adjacentes, marcas notórias, alto renome) e juízo de advogado(a) inscrito(a) na OAB com prática em PI. Um resultado "sem conflitos óbvios" significa que a triagem não achou — **não** que a marca esteja livre. Clientes já foram processados sobre marcas que "passaram" no knockout.

## Instructions

1. Leia `~/.claude/plugins/config/claude-for-legal/propriedade-intelectual/CLAUDE.md`. Se contiver `[PLACEHOLDER]`, pare e encaminhe para `/propriedade-intelectual:cold-start-interview`.
2. Siga o workflow abaixo.
3. Rode intake (marca, produtos/serviços, classes NCL, jurisdições, estilização).
4. Knockout — barreiras intrínsecas conforme LPI art. 124 (sinais irregistráveis): genérico, descritivo, enganoso, geográfico, sobrenome, falsa indicação, vedações específicas.
5. Busca de marcas similares com o que estiver conectado (INPI Busca, eventual conector internacional). Sem conector, declare e prossiga só com knockout + análise de fatores.
6. Caminhe pelos fatores de colidência (semelhança gráfica/fonética/ideológica + afinidade de produtos/serviços + outros). Sinalize cada um; nunca conclua.
7. Escreva o memorando na pasta de matéria (se ativa) ou em outputs. Aplique cabeçalho de work-product por papel.
8. Encerre com próximos passos recomendados e gate de não-advogado(a) se aplicável.

Esta skill **nunca** conclui que uma marca está livre. Em dúvida, sinalize — advogado(a) decide.

## Examples

```
/propriedade-intelectual:clearance "APEXLEAF para linha de vestuário outdoor, lançamento BR + EU"
```

```
/propriedade-intelectual:clearance
```

(E a skill perguntará marca, produtos, classes e jurisdições.)

---

## ESTA É PRIMEIRA PASSADA, NÃO PARECER DE VIABILIDADE

**Diga isso no topo de toda saída. Não tire. Não suavize.**

> **Esta é primeira passada, não parecer de viabilidade.** Parecer de viabilidade marcária exige busca profissional completa (INPI base completa + composições + classes adjacentes + marca não registrada/uso anterior + WIPO Madrid se internacional + ATA notarial de uso) e juízo de advogado(a) inscrito(a) na OAB sobre colidência, que depende de fatores que triagem estruturada não avalia integralmente. "Sem conflitos óbvios" aqui significa que a triagem não achou — **não** que a marca esteja livre. Clientes já foram processados sobre marcas que "passaram" no knockout. Advogado(a) com prática em PI avalia antes de adotar, depositar ou investir nesta marca.

Este é o guardrail mais alto do plugin. Sub-chamar conflito é porta de uma via — logo em caminhão, lançamento de produto, pedido de registro depositado, todos com problema embaixo. Sobre-chamar é porta de duas vias — o(a) advogado(a) reduz a lista. Fique no lado da porta de duas vias.

---

## Contexto de matéria

Cheque `## Workspaces de matéria` no CLAUDE.md de prática. Se `Habilitado` for `✗` (default in-house), pule. Se habilitado e sem matéria ativa, pergunte: "Para qual matéria? Rode `/propriedade-intelectual:matter-workspace switch <slug>` ou diga `practice-level`." Saídas em `~/.claude/plugins/config/claude-for-legal/propriedade-intelectual/matters/<matter-slug>/`. Nunca leia outra matéria salvo `Cross-matter context` `on`.

---

## Carregar perfil de prática primeiro

Antes de rodar, leia `~/.claude/plugins/config/claude-for-legal/propriedade-intelectual/CLAUDE.md`. Puxe:

- **Papel** de `## Quem está usando` (advogado(a) vs não-advogado(a) — muda cabeçalho e gate).
- **Registrado em** e **Jurisdições de watch** de `## Perfil de prática PI` e `## Proteção de marca / brand protection`.
- **Integrações** de `## Integrações disponíveis` (INPI Busca, WIPO Madrid, JusBrasil, eventual conector internacional — cada um determina o que se busca, qual o fallback, e como atribuir a fonte).
- **Postura de decisão** de `## Postura de decisão em juízos jurídicos subjetivos` — esta skill nunca conclui "não colidente".

Se `~/.claude/plugins/config/claude-for-legal/propriedade-intelectual/CLAUDE.md` contiver `[PLACEHOLDER]`, mostre o bounce:

> Vejo que você ainda não configurou o perfil de prática — é assim que eu calibro postura, jurisdições e cadeia de aprovação.
>
> **Duas escolhas:**
> - Rode `/propriedade-intelectual:cold-start-interview` (2 minutos) para configurar; depois eu rodo isto calibrado para SUA prática.
> - Diga **"provisório"** e eu rodo contra defaults genéricos — jurisdição Brasil (INPI), apetite de risco médio, papel advogado(a), sem playbook — e marco toda saída como `[PROVISÓRIO — configure o perfil]`.

### Modo provisório

Se o usuário disser "provisório", rode normalmente com defaults: apetite médio, papel advogado(a), Brasil (INPI), sem playbook. Tag a nota do revisor e todo bloco de achado com `[PROVISÓRIO]`. No fim:

> "Aquilo foi rodada genérica contra defaults. Rode `/propriedade-intelectual:cold-start-interview` para output calibrado para SUA prática — seu playbook, suas jurisdições, seu apetite. 2 minutos."

---

## Intake

Pergunte uma vez, em bloco:

> Algumas perguntas antes de eu rodar a triagem:
>
> 1. **Marca proposta.** Grafia exata, eventual estilização, e se é nominativa, figurativa, mista ou tridimensional.
> 2. **Produtos ou serviços.** O que efetivamente será comercializado ou oferecido. Uma ou duas frases — eu mapeio para classes NCL.
> 3. **Classes NCL.** Se já souber as classes Nice, liste. Senão descreva os produtos/serviços e eu sugiro as classes prováveis e confirmo antes de buscar.
> 4. **Jurisdições.** Onde você planeja usar, registrar ou enforcar? (BR / Madri designando outros países / específicos — default: `Registrado em` do perfil.)
> 5. **Como aparecerá em uso.** Slogans, nomes adjacentes, conjunto-imagem (LPI 195 III), elementos figurativos que aparecerão com a marca.

Espere a resposta. Se a descrição é vaga ("ferramenta de IA", "plataforma"), empurre uma vez:

> Me dê a coisa concreta que o(a) consumidor(a) vê — é app de consumo, API enterprise, produto físico, serviço? As classes dependem disso.

---

## Knockout

Antes de qualquer busca em base, rode os problemas intrínsecos que matam a marca independentemente de registros anteriores — **LPI art. 124** (sinais não registráveis como marca). Para cada item, avalie diretamente e sinalize. Não racionalize problema claro.

| Vedação LPI 124 | O que significa | Sinalizar quando |
|---|---|---|
| **Inc. VI — sinal de caráter genérico, necessário, comum, vulgar, etc.** | A expressão É a categoria | A marca nomeia o que é a coisa (ex.: "SABÃO" para sabão) |
| **Inc. VI / VIII — sinal de caráter descritivo** | Descreve característica do produto/serviço | Consumidor lê e sabe o que o produto faz sem imaginação |
| **Inc. X — sinal que induza a falsa indicação** | Engana sobre origem/natureza/qualidade | Sugere qualidade que o produto não tem e que importaria |
| **Inc. IX e XII — indicação geográfica / lugar reconhecido** | Nome de lugar conhecido pela produção; ou IG registrada | Lugar + produto típico; ou IG vigente no INPI |
| **Inc. XV — nome civil / sobrenome / pseudônimo** | É nome próprio ou sobrenome | Lê como nome/sobrenome de alguém para o consumidor relevante |
| **Inc. I — brasão, armas, escudo, símbolo oficial** | Símbolos públicos protegidos | Inclui escudo nacional, armas oficiais, etc. |
| **Inc. III — moral, bons costumes, ofensa à honra** | Atenta à moral pública | Termo ofensivo, com observação: STF em decisões recentes (ADO 26; ADI 4275) reduziu margem para censura de marca por moralidade — `[verificar]` |
| **Inc. IV — bandeira, brasão, sigla oficial estrangeira** | Sem autorização | Inclui símbolo de Estado, organismo internacional |
| **Inc. XIII — moeda, título de crédito** | Reprodução não admitida | Reproduz cédula, valor monetário |
| **Inc. XVI — pseudônimo, nome artístico** | De terceiro sem autorização | Mesmo critério do XV |
| **Inc. XVII — obra protegida** | Título / personagem com direitos autorais vigentes | Sem autorização do(a) titular |
| **Inc. XIX — colidência com marca registrada/depositada** | Marca anterior idêntica/semelhante em produto idêntico/afim | Será verificado na busca de similares |
| **Inc. XX — dualidade de marcas** | Mesma marca em registros do mesmo titular sem distintividade | Marca confusa para o consumidor mesmo do próprio titular |
| **Inc. XXI — forma necessária / funcional (3D)** | Função técnica não pode virar marca | Marca tridimensional cuja forma é funcional → patente, não marca |
| **Inc. XXII — objeto patenteável que tenha caído em domínio público** | Forma livre de patente vencida | Aspecto técnico de patente caduca → não pode virar marca |
| **Inc. XXIII — marca anterior de boa-fé (usuário anterior)** | LPI 129 §1º | Reivindicado por terceiro com prova de uso anterior 6 meses |

**Marca de alto renome** (LPI art. 125) e **marca notoriamente conhecida** (LPI art. 126; Convenção de Paris 6º bis) — sinalize separadamente: marca colidente que detenha um desses status amplia a proteção para classes diversas. Verificar registro de alto renome na base INPI.

**Saída:** para cada categoria, "sem problema identificado" ou flag específico com uma linha de razão. Não produza tabela vazia de passes.

---

## Busca de marcas similares

O objetivo é **encontrar marcas anteriores potencialmente colidentes**, não decidir colidência. Essa decisão é do(a) advogado(a).

### O que o usuário tem conectado

Leia `## Integrações disponíveis`:

- **Se conector de busca marcária estiver disponível** (INPI Busca; WIPO Madrid Monitor): rode busca preliminar nas classes e jurisdições relevantes. Atribua cada resultado à sua fonte. Anote data e escopo (quais bases, quais classes, exato vs. fonético, busca figurativa).
- **Se conector de pesquisa jurídica estiver disponível** (JusBrasil, STJ): varra disputas reportadas com a marca ou variante próxima. Mesma regra de atribuição.
- **Se nenhum conector estiver disponível:** diga, explicitamente, na saída. Não inferir resultados do conhecimento do modelo.

### Fallback sem acesso a base

Escreva, na saída, este enunciado verbatim:

> **Nenhuma busca em base foi rodada.** Esta triagem não acessou base INPI completa, WIPO Madrid, JusBrasil, STJ, busca de uso anterior, base de domínio (Registro.br), social media, ou fontes de marca não-registrada. Knockout ou busca completa nessas bases é necessária antes de qualquer conclusão sobre disponibilidade. A triagem abaixo limita-se a análise de barreira intrínseca (LPI 124) e fatores estruturados contra marcas que o(a) usuário(a) identificou ou que apareceram na conversa.

Então prossiga — knockout e análise de fatores ainda são úteis, apenas honestamente etiquetados.

### Para cada marca similar encontrada (ou fornecida)

Capture:

- **Marca** (caracteres exatos, estilização)
- **Fonte** (nº processo INPI, designação Madrid, citação judicial, domínio, handle social — o que for)
- **Classes / lista de produtos-serviços** do registro
- **Titular**
- **Situação** (registrado / depositado / arquivado / extinto / em oposição / em caducidade)
- **Data de depósito / data de concessão / data de primeiro uso** se disponíveis

**Sem suplementação silenciosa.** Se você citar nº de processo INPI, veio da busca que você rodou; se descrever marca que o(a) usuário(a) mencionou, diga. Nunca invente nº de processo nem "complete" detalhe que o registro não suporta. Se a busca não retornou data, escreva "data não disponível no resultado da busca" — não chute.

### Varredura de famílias adjacentes (obrigatória antes de concluir)

Triagem que só checa identidade e quase-identidade perde marcas que concorrentes adotaram *porque* a sua estava tomada. Antes de concluir, identifique 3–5 famílias adjacentes que devem ser varridas, e peça ao(à) usuário(a) que confirme.

Famílias adjacentes são substitutos categoria-convencionais que concorrente razoável consideraria. Para `NEXUS HOME` em smart-home hub, no mínimo:

- **Sinônimos de categoria** para NEXUS: `HUB`, `NEST`, `CORE`, `LINK`, `CONNECT`, `BRIDGE`, `CENTRAL`, `GATEWAY`.
- **Nomes de assistente** na mesma categoria: `ALEXA`, `ECHO`, `SIRI`, `GOOGLE HOME`, `CORTANA`.
- **Variantes HOME / CASA / SMART**: `SMART HOME`, `CASA`, `LAR`, `CONECTADA`.
- **Gêmeos fonéticos**: `NEXIS`, `NEKSUS`, `NEXXUS`.

Saída na seção de Marcas Similares com prompt de confirmação:

> **Famílias adjacentes a varrer (confirme ou acrescente):**
>
> - [família 1 — ex.: HUB / NEST / LINK / CONNECT]
> - [família 2 — ex.: nomes de assistente]
> - [família 3 — ex.: HOME / CASA / SMART variantes]
> - [família 4 — gêmeos fonéticos]
>
> Triagem que só checa identidade perde marcas que concorrente adotou porque a sua estava tomada. Confirme antes de eu continuar.

> **Quando jurisdições não-lusófonas estão em escopo,** a varredura fonética só em português perde fonte importante de conflito transfronteiriço. Adicione:
> - **Equivalentes de tradução.** A marca traduzida para os idiomas relevantes. Em jurisdições da UE (e em vários países) tradução é tratada como mesma marca para fins de colidência.
> - **Transliteração.** A marca escrita em alfabetos relevantes (Cirílico, CJK, Árabe, Devanagari) — equivalência fonética entre alfabetos é base reconhecida.
> - **Variações de grafia.** Marcas registradas em alfabeto não-latino que soam como a sua quando romanizadas.
>
> Se não pode rodar análise cross-language, diga: "Análise fonética/tradução cross-language não realizada — é a fonte mais comum de conflito transfronteiriço. Busca em [jurisdição] deve incluir."

Se houver conector de busca marcária, re-rode a varredura para cada família adjacente confirmada (exato + fonético + tradução-de-equivalente-estrangeiro) e some na tabela de Marcas Similares com `Família adjacente` na fonte. Sem conector, diga, e liste as famílias como input para a busca profissional — não pule silenciosamente.

---

## Fatores de colidência marcária

> **Framework de colidência é jurisdição-específico.** Não aplique o errado.
>
> - **Brasil:** jurisprudência consolidada do STJ — semelhança **gráfica/fonética/ideológica** + **afinidade de produtos/serviços** (princípio da especialidade — LPI art. 124 XIX); proteção mais ampla para marca de alto renome (LPI 125) em todas as classes; notoriamente conhecida (LPI 126) na própria atividade. Considera-se o consumidor médio do segmento. Critérios complementares: distintividade do sinal anterior, intenção do adotante, prova de confusão efetiva. Decisões de referência: STJ REsp 1.105.422; REsp 1.114.745; vasta jurisprudência sobre conjunto-imagem (LPI 195 III).
> - **UE (Art. 8(1)(b) EUTMR):** Apreciação global — todos os fatores relevantes ponderados holisticamente pelo consumidor médio. Peso maior em semelhança fonética; equivalentes de tradução padrão; "risco de associação" além de confusão de origem; distintividade da marca anterior pesa mais.
> - **UK (TMA 1994 §5(2)):** segue apreciação global pós-Brexit com case law divergente. Cheque decisões UK específicas.
> - **EUA (circuitos federais):** Testes multifatoriais (*du Pont*, *Polaroid*, *Sleekcraft*) — força da marca, semelhança (visão/som/sentido), proximidade dos produtos, canais, sofisticação do comprador, confusão efetiva, intenção. **Diferente do BR — não aplicar silenciosamente.**
> - **Outras jurisdições:** Se intake inclui jurisdição sem framework acima, diga: "Não tenho o framework de [jurisdição]. Aplicar o brasileiro daria resposta errada que parece certa. Opções: (a) busco o padrão aplicável, (b) você roteia para especialista de [jurisdição], (c) anoto fora de escopo." Nunca aplique silenciosamente doutrina BR.

Para Brasil, caminhe pelos fatores e produza **flag**, não veredicto. Cada fator diz o que pesa para cada lado e onde está a incerteza:

- **Semelhança gráfica** (escrita / visual) — composição, número de letras, sílabas, traçado em marca mista/figurativa.
- **Semelhança fonética** — som ao pronunciar; consumidor brasileiro lê de viva voz.
- **Semelhança ideológica** — significado, ideia transmitida, evocação.
- **Afinidade de produtos/serviços** (princípio da especialidade — LPI 124 XIX) — mesma classe NCL é forte sinal; classes diferentes mas com mesmo público/canal podem afinar; classes muito distantes geralmente afastam (salvo alto renome).
- **Canal de comercialização** — varejo físico, e-commerce, B2B, profissional, etc.
- **Sofisticação do consumidor** — compra por impulso (gôndola) × compra refletida (B2B) muda o padrão de atenção.
- **Força da marca anterior** — fantasiosa / arbitrária / sugestiva / evocativa / descritiva; marca de alto renome (LPI 125) ou notoriamente conhecida (LPI 126) goza de proteção ampliada.
- **Intenção** — evidência de aproveitamento parasitário ou usurpação (LPI 195 — concorrência desleal).
- **Confusão efetiva** — relatos de consumidor confundido, atendimento mal-endereçado, pesquisa.
- **Coexistência prévia** — se marcas conviveram em mercado sem disputa, é fator de minoração.

Conforme `## Postura de decisão`:

- **Nunca conclua "não há colidência".**
- Em dúvida, escreva: "Marcas similares encontradas — avaliação de colidência por advogado(a) requerida antes de adoção." Ou: "Fatores cortam para os dois lados; advogado(a) decide."
- "Sem marcas similares encontradas nas bases pesquisadas" é aceitável *só* se busca real foi rodada; senão, use o fallback.

---

## Próximos passos recomendados

Toda saída encerra com próximos passos concretos, agrupados pelo que a triagem encontrou:

- **Se barreira intrínseca (LPI 124) encontrada:** reframear a marca, ou aceitar a descritividade e planejar secondary meaning (distintividade adquirida — admitida pelo INPI em hipóteses); rotear para advogado(a) antes de adotar.
- **Se marcas similares nas bases pesquisadas:** revisão por advogado(a) requerida antes de adotar, depositar ou veicular. Próximo passo usual: busca profissional completa.
- **Se sem similares mas sem busca em base:** busca completa requerida antes da adoção. Nomeie as bases que precisam ser tocadas (INPI completo, RPI atual, Madrid Monitor para jurisdições estrangeiras, base de domínio Registro.br, social).
- **Se similares encontradas e a anterior é fraca, antiga, em classe diferente ou em caducidade:** sinalize para advogado(a) — a triagem não toma essa decisão.
- **Sempre:** parecer completo de viabilidade por advogado(a) com prática em PI, dimensionado ao investimento. Marca que vai em linha de produto + campanha nacional pesa mais que marca para pop-up.

---

## Formato de saída

Prepende o cabeçalho de work-product de `## Outputs`.

```markdown
[CABEÇALHO DE WORK-PRODUCT]

# Busca de Anterioridade Marcária — Primeira Passada (NÃO É PARECER)

**Esta é primeira passada, não parecer de viabilidade.** Parecer exige busca profissional completa e juízo de advogado(a) com prática em PI. "Sem conflitos óbvios" significa que a triagem não achou — não que a marca esteja livre. Advogado(a) inscrito(a) na OAB avalia antes de adotar, depositar ou investir.

**Resultado da triagem:** [VERDE / AMARELO / VERMELHO — uma frase de razão]

## Marca proposta

- **Marca:** [texto exato, estilização anotada]
- **Tipo:** [nominativa / figurativa / mista / tridimensional]
- **Produtos / serviços:** [descrição]
- **Classes NCL:** [números das classes Nice com uma linha de descrição]
- **Jurisdições:** [BR / Madri designando outros / específicas]
- **Teste de colidência aplicado:** [BR — semelhança gráfica/fonética/ideológica + afinidade / EU global / outro — com razão]

## Knockout — barreiras intrínsecas (LPI 124)

| Vedação | Flag | Nota |
|---|---|---|
| Genérico / descritivo / enganoso / geográfico / sobrenome / falsa indicação / símbolo oficial / etc. | [nenhuma / sinalizada] | [uma linha se sinalizada] |

## Marcas similares

**Fontes pesquisadas:** [bases tocadas, com datas — ou "nenhuma busca rodada; vide nota de escopo abaixo"]
**Escopo:** [classes, jurisdições, exato vs. fonético, busca figurativa ou não]

**Famílias adjacentes varridas (confirmadas com o usuário):**
- [família 1]
- [família 2]
- [família 3]
- [família 4]

*Triagem que só checa identidade perde marcas que concorrente adotou porque a sua estava tomada. Família não varrida (sem conector, sem tempo) é listada explicitamente como input para busca profissional — não pulada em silêncio.*

| Marca | Fonte | Classes / Produtos-serviços | Titular | Situação | Datas | Nota |
|---|---|---|---|---|---|---|
| [exata] | [nº processo INPI / citação / URL] | [classes] | [titular do registro] | [reg/depós/arquiv/extinto/em opos] | [depósito / concessão] | [por que importa — exato / família adjacente] |

*Sem busca:* **Nenhuma busca em base foi rodada.** Esta triagem não acessou INPI Busca, WIPO Madrid, JusBrasil, STJ, Registro.br, ou fontes de uso anterior. Busca completa nessas bases é necessária antes de conclusão.

## Fatores de colidência — flags para revisão por advogado(a)

| Fator (Brasil) | Flag | Direção |
|---|---|---|
| Semelhança gráfica | [nota] | [pesa para colidência / contra / misto] |
| Semelhança fonética | [nota] | [direção] |
| Semelhança ideológica | [nota] | [direção] |
| Afinidade de produtos/serviços (princípio da especialidade) | [nota] | [direção] |
| Canal de comercialização | [nota] | [direção] |
| Sofisticação do consumidor | [nota] | [direção] |
| Força da marca anterior (incluindo alto renome / notoriamente conhecida) | [nota] | [direção] |
| Intenção / parasitismo (LPI 195) | [nota] | [direção] |
| Confusão efetiva | [nota ou "sem evidência"] | [direção] |
| Coexistência prévia | [nota ou "n/d"] | [direção] |

**Conclusão sobre colidência:** *Esta skill não conclui.* Ou:
- "Marcas similares encontradas; avaliação por advogado(a) antes de adoção."
- "Sem similares nas bases pesquisadas; busca completa antes de adoção."
- "Fatores cortam para os dois lados; advogado(a) decide."

## Próximos passos recomendados

- [passo 1 — ex.: "Busca profissional completa INPI + Madrid + uso anterior + Registro.br antes de adoção"]
- [passo 2 — ex.: "Design-around da marca `APEXLEAF` em classe 25 se prosseguir"]
- [passo 3 — ex.: "Reframear — atual é descritiva e exigirá distintividade adquirida"]
- [roteamento conforme perfil — advogado(a)/escritório externo da linha de prossecução de marca]

## Verificação de citações

Toda decisão, nº de processo INPI, lei e resultado de base neste memorando deve ser conferido contra fonte autoritativa antes de qualquer uso. Nº de processo INPI, classificação NCL e datas de primeiro uso são os campos com maior taxa de erro. Não cite resultado que não consegue abrir.
```

---

## Gate de não-advogado(a)

Antes de emitir, leia `## Quem está usando`. Se Papel for Não-advogado(a):

> Esta saída é triagem de pesquisa, não parecer. Adotar, depositar ou investir nesta marca com base só nesta triagem tem consequências jurídicas — incluindo ser processado(a) por infração sobre marca que "passou" no check. Advogado(a) inscrito(a) na OAB com prática em PI avalia antes de você se mover.
>
> Segue brief para a conversa com advogado(a) — corta o tempo da reunião:
>
> [Gere síntese de 1 página: marca proposta, produtos/serviços e classes, barreiras intrínsecas (se houver), marcas similares (se houver), o que foi e não foi pesquisado, três perguntas a fazer.]
>
> Caso precise localizar advogado(a): **OAB-seccional** do seu estado (Serviço de Orientação Jurídica); **ABPI** (Associação Brasileira da Propriedade Intelectual) e **ASIPI** mantêm diretórios de praticantes; **INTA** (International Trademark Association) também tem diretório de membros brasileiros; **NPJs** universitários para hipossuficientes.

Entregue o memorando completo junto com o brief. Não retenha a análise.

---

## Local de saída

Se workspaces estão habilitados e há matéria ativa, escreva em `~/.claude/plugins/config/claude-for-legal/propriedade-intelectual/matters/<matter-slug>/outputs/clearance-<marca-slug>-YYYY-MM-DD.md`. Senão em `~/.claude/plugins/config/claude-for-legal/propriedade-intelectual/outputs/clearance-<marca-slug>-YYYY-MM-DD.md` e mostre o caminho ao(à) usuário(a).

Anote uma linha em `history.md` da matéria se ativa.

---

## Encerre com a árvore de próximos passos

Encerre com a árvore de próximos passos conforme `## Outputs` do CLAUDE.md. Customize as opções ao que a skill produziu — os cinco ramos default são ponto de partida, não trava. A árvore É a saída; advogado(a) escolhe.

## O que esta skill NÃO faz

- **Concluir que uma marca está livre.** Nunca. Guardrail mais alto do plugin.
- **Substituir busca completa INPI, base de uso anterior, Madrid, Registro.br, social, e busca figurativa.**
- **Depositar pedido de marca.** Depósito é ato profissional (advogado(a) ou agente de PI/mandatário no INPI — LPI art. 217).
- **Avaliar conjunto-imagem (trade dress), diluição, alto renome com profundidade** além de flag preliminar. Alto renome requer registro específico no INPI (LPI 125 — Resolução INPI 107/2013 e atualizações); notoriamente conhecida (LPI 126) requer prova robusta — `[verificar]`.
- **Endereçar barreiras locais estrangeiras** (ex.: equivalentes fonéticos no Japão, equivalentes de tradução UE) além de sinalizar que análise estrangeira é necessária.
- **Citar resultado para cliente/contraparte/imprensa.** É pesquisa interna. Coberta pelo sigilo se cabeçalho de topo se aplicar.

---

## Tom

Conciso, concreto, honesto quanto a escopo. O(a) advogado(a) leitor(a) deve saber em dez segundos o que a triagem achou, o que não achou, e o que precisa acontecer antes de qualquer adoção. Sem prosa hedger. Os guardrails de topo e a linha "esta skill não conclui" fazem o trabalho de escopo.
