---
name: brief-section-drafter
description: Redige uma seção de peça processual no estilo da casa, consistente com a tese do caso — todo fato citado, todo precedente conferido, todo argumento amarrado à tese. Use quando o(a) usuário(a) disser "redigir a [seção]", "escreva a exposição dos fatos", "seção de fundamentos sobre [tema]", ou precisar de uma primeira versão de uma seção de peça.
argument-hint: "[seção — ex. 'exposição dos fatos', 'fundamentos II']"
---

> **Adaptação BR não oficial.** Esta skill foi originalmente escrita para FRCP / FRE / Bluebook / Rule 11 (EUA). No contexto brasileiro, **leia esta seção primeiro** — ela traduz funcionalmente os institutos:
>
> | Conceito EUA → | Equivalente BR |
> |---|---|
> | Brief / motion / pleading | Petição inicial (CPC 319), contestação (CPC 335-342), réplica, alegações finais, recurso (apelação CPC 1009, agravo CPC 1015) |
> | Statement of facts | Exposição dos fatos (CPC 319, III) |
> | Argument section / brief argument | Fundamentos jurídicos do pedido (CPC 319, III) |
> | Standard of review | Não há equivalente direto; em recurso, identificação do efeito (devolutivo, suspensivo, translativo) e juízo de admissibilidade |
> | Bluebook / ALWD citation | Citação no padrão ABNT NBR 6023 ou estilo da casa (formato livre admitido salvo regra local do tribunal) |
> | Rule 11 / candor obligation | Litigância de má-fé (CPC arts. 79–81); deveres de boa-fé e cooperação (CPC arts. 5º e 6º); EOAB arts. 31–33 |
> | FRCP / Federal Rules of Evidence | CPC (Lei 13.105/2015) arts. 369–484 (provas) |
> | PD 57AC (E&W witness statements) | Sem equivalente formal; depoimento pessoal (CPC 385) e oitiva de testemunha (CPC 442) são em audiência sob inquirição direta pelo juiz; declarações escritas têm valor limitado |
> | Westlaw / CourtListener / Trellis / Descrybe | JusBrasil, Lexml, sítios oficiais STF / STJ / TST / TJs / TRFs / TRTs, Diário da Justiça eletrônico |
> | Local rules / judge's standing orders | Regimento Interno do tribunal; portarias da Vara; resoluções do CNJ |
> | CPR 31.22 (E&W implied undertaking) | Não há doutrina formalmente análoga; sigilo dos autos sob segredo de justiça (CPC art. 189); LGPD na hipótese de dados pessoais |
>
> **Cabeçalho de trabalho:** use a marcação configurada em `contencioso/CLAUDE.md → ## Saídas` — sigilo profissional (EOAB art. 7º XIX; CPC art. 388). **Não traduza nome de lei.** Toda saída é **rascunho para revisão de advogado(a) inscrito(a) na OAB**.

---

# /brief-section-drafter

1. Carregue `~/.claude/plugins/config/claude-for-legal/contencioso/CLAUDE.md` → tese do caso, estilo da casa.
2. Siga o workflow e referência abaixo.
3. Redija no formato/tom/estilo de citação da casa. Consistente com a tese.
4. Saída: seção em rascunho. Sinalize toda passagem onde um fato ou citação precisa de verificação.

---

# Redator de Seção de Peça

## Depoimentos e declarações escritas — direito brasileiro

No Brasil, **não existe deposition** (interrogatório de testemunha por advogado fora da presença do juiz). O sistema é:

- **Depoimento pessoal das partes** — CPC art. 385, prestado em audiência, sob inquirição do juiz (sistema presidencialista — CPC art. 459).
- **Oitiva de testemunhas** — CPC arts. 442-463, em audiência, ordenada pelo juiz, com perguntas formuladas diretamente pelas partes mas com filtro judicial.
- **Declarações escritas (afidávits, statements)** não têm o mesmo peso probatório que nos EUA; servem apenas como início de prova, e a contraparte tem direito ao contraditório (CPC art. 9º) — incluindo, em regra, a oitiva da testemunha em audiência.

**Não redija narrativas "como se fossem da testemunha".** O depoimento e a oitiva ocorrem oralmente; a peça processual descreve o que se pretende provar, não cria texto declaratório atribuído à testemunha. Se a casa usa "declaração escrita" como início de prova, coleta-se a declaração da própria testemunha — você ajuda a organizar tópicos, formular roteiro de perguntas, identificar contradições com documentos, redigir nota de impugnação a documento, mas não escreve as palavras da testemunha.

## Propósito

Uma boa seção de peça é consistente com a tese, citada nos autos, escrita no estilo da casa e conferível. Esta skill produz a primeira versão — ênfase em *rascunho*. Sócio(a) ou advogado(a) sênior revisa.

## Escrita ou oral?

Pergunte antes de redigir: "Isso é para peça escrita ou sustentação oral?" São ofícios diferentes:

- **Escrita:** completa. Cobrir os pontos, desenvolver a autoridade, antecipar respostas.
- **Sustentação oral (memorial, sustentação em recurso, alegações finais orais):** estratégica. Escolha os 3-4 pontos que mais importam. Conceda ou ignore os fracos. Lidere com o mais forte. O órgão julgador lembra dos dois primeiros minutos e dos dois últimos. "Detalhada demais" para sustentação oral lê como desfocada.

## Fidelidade ao processo — citações e referências precisas

Duas regras canônicas (CLAUDE.md `## Guardrails compartilhados`):

**Citações textuais devem ser textuais.** Nunca coloque aspas em palavras atribuídas a advogado(a) da parte contrária, testemunha, juiz/desembargador ou qualquer peça dos autos sem ter a passagem exata e citar a folha. Citação inventada é fabricação e configura **litigância de má-fé (CPC art. 80)**. Use:

- **Parafraseie sem aspas:** "O autor argumentou que X `[conferir contra autos — fls. __]`."
- **Marque o placeholder:** `[verificar citação exata — referência aos autos pendente]`
- **Nunca preencha a lacuna.** A nota ao revisor deve flagar todo `[verificar citação exata]`.

**Citação pinpoint deve sustentar a proposição inteira.** Se o argumento é "a contraparte disse X, Y e Z" e você cita uma folha, verifique que ela sustenta X E Y E Z. Caso contrário, divida (fls. 10, 12 e 15) ou reduza a proposição. Citação que sustenta parte da alegação é o jeito mais comum de credibilidade erodir frente ao juízo.

## Candura sobre argumentos fracos

Quando a lei ou o processo é contra um ponto, diga. Não construa argumento frágil e apresente como sólido. Sinalize:

> "Este ponto é fraco — [precedente STJ / texto legal] aponta em sentido contrário. Considere (a) sustentar mesmo assim e enquadrar como `[alternativa]`, (b) conceder e pivotar para [ponto mais forte], (c) excluir. `[review — chamada estratégica]`."

Sustentar argumento fraco sem sinalizar erode a credibilidade frente ao juízo e pode caracterizar **litigância de má-fé** (CPC art. 80, II e V) ou violação de **deveres éticos** (EOAB arts. 31-33; CED-OAB arts. 2º e 6º).

## Cobertura de citações

Quando esta peça for conferida — por você, por outra skill, ou por revisor(a):

1. **Primeira passada: extrair.** Liste TODA citação — leis, decretos, súmulas, acórdãos, doutrina, citações dos autos. Reporte: "Encontradas [N] citações."
2. **Segunda passada: conferir.** Confira cada uma contra a fonte. Não amostre. Não pare por cansaço.
3. **Relate cobertura.** No fim: "Conferidas [N] de [M]. [K] não recuperáveis — verificar manualmente. [J] confirmadas. [I] flagadas como possível erro de citação. [H] flagadas como mal fundamentadas (citação existe mas não sustenta a proposição)."
4. **Quando texto-fonte indisponível, diga "não conferida", nunca "confirmada".**
5. **Os erros mais difíceis são suporte parcial.** Citação que sustenta parte da alegação.

## Ecoar vs repetir

Eco de enquadramentos é bom; copiar frases não. Em recurso, a apelação não pode ser releitura da inicial — deve enfrentar a sentença.

## Carregar contexto

`~/.claude/plugins/config/claude-for-legal/contencioso/CLAUDE.md` → tese, estilo da casa (formato de citação, estrutura, tom, padrões de extensão).

**Conferência de conflitos — não bypassável.** Verifique `~/.claude/plugins/config/claude-for-legal/contencioso/matters/_log.yaml` para o slug. Se a matéria não está no log, recuse:

> "Não vejo [slug] no log. Rode `/contencioso:matter-intake` primeiro — conferência de conflitos é o portão."

## Workflow

### Passo 1: Qual seção?

| Seção | O que faz | Insumos |
|---|---|---|
| Exposição dos fatos | Conta a história, no nosso enquadramento, com referência aos autos (CPC 319 III) | Cronologia, documentos-chave, oitivas |
| Síntese dos pedidos | Define o que pleiteamos (CPC 319 IV) | Tese, pedido principal e subsidiário |
| Fundamentos jurídicos | Tece a tese com lei, doutrina, jurisprudência | Tema, autoridades, fatos |
| Conclusão / pedidos | Pede o que queremos | Pedido final, custas, honorários (CPC 85) |

### Passo 2: Verificação da tese

A seção precisa fazer o quê pela tese? Se a seção contradiz a tese — pare. Sinalize.

### Passo 3: Redigir no estilo da casa

**Pesquise regras locais e despachos do juízo (regimento do tribunal, resolução do CNJ, portarias da Vara) sobre extensão, formato e protocolo. Não confie em "preferência".** Cite normas primárias (artigo do regimento, número da resolução). Confirme vigência.

Conforme `~/.claude/plugins/config/claude-for-legal/contencioso/CLAUDE.md`:

- **Formato de citação:** ABNT NBR 6023 ou estilo da casa (forma livre admite-se desde que clara).
- **Estrutura:** Como a banca organiza fundamentos? Tópicos numerados? Subitens? Sumário?
- **Tom:** Combativo ou contido? Combine com peça-modelo.
- **Extensão:** Conforme regra local; em peticionamento eletrônico (PJe / e-SAJ / e-Proc), limites técnicos do sistema.

### Passo 4: Citar tudo

Cada fato → referência aos autos (fls. __, mídia __, evento __ no PJe).
Cada proposição jurídica → lei + dispositivo, súmula, acórdão (STF/STJ/TST com data) ou doutrina.

**Disciplina de marcadores:**
- `[VERIFICAR: afirmação factual]` — qualquer fato não confirmado contra autos
- `[INCERTO: proposição jurídica]` — qualquer ponto não confirmado contra autoridade atual
- `[CITAÇÃO PENDENTE: precedente / dispositivo]`

**Sem suplementação silenciosa.** Se uma busca em ferramenta de pesquisa (JusBrasil, Lexml, STF, STJ, TST) retorna poucos ou nenhum resultado para autoridade necessária, reporte e PARE. NÃO preencha de web search ou conhecimento de modelo sem perguntar. Diga: "A busca retornou [N] resultados em [ferramenta]. Cobertura fina para [tema]. Opções: (1) ampliar consulta, (2) tentar outra ferramenta, (3) web search — resultados marcados `[web search — verificar]` checar contra fonte primária antes de usar, ou (4) deixar `[CITAÇÃO PENDENTE]` e parar. Qual prefere?"

**Atribuição de fonte.** Marque cada citação: `[JusBrasil]`, `[STJ]`, `[STF]`, `[TST]`, `[Lexml]`, `[Planalto]`, `[web search — verificar]`, `[conhecimento do modelo — verificar]`, `[fornecido pelo(a) usuário(a)]`.

### Passo 5: Saída

**Antes do protocolo da peça (ato consequente):** Leia `## Quem está usando` no CLAUDE.md. Se papel é Não-advogado(a):

> Protocolar peça tem consequências jurídicas — vira parte dos autos, vincula a parte aos argumentos e fatos, e a assinatura de advogado(a) inscrito(a) na OAB carrega responsabilidade profissional (EOAB arts. 31-33). Você revisou com advogado(a)?
>
> Se sim, prossiga. Se não, aqui está um resumo de 1 página para levar:
>
> [Gere resumo: seção redigida, vínculo com a tese, autoridades citadas, marcadores [VERIFICAR]/[INCERTO]/[CITAÇÃO PENDENTE] em aberto, o que pode dar errado (erro factual, citação não suportada), o que perguntar ao(à) advogado(a) antes de protocolar.]
>
> Para encontrar advogado(a): seccional da OAB do seu estado mantém serviço de referência; OAB Federal mantém cadastro de advocacia voluntária e pro bono (Provimento CFOAB 166/2015).

Não trate como pronto-para-protocolo sem sim explícito.

A seção, no estilo da casa, com marcadores inline.

Prefácio (não vai na peça — nota ao(à) revisor(a)):

```markdown
[CABEÇALHO DE MATERIAL DE TRABALHO — per plugin config ## Saídas — varia por papel; ver `## Quem está usando`]

## Notas de Redação — [Seção] — [data]

**Vínculo com a tese:** [Como esta seção sustenta a tese]
**Autoridades citadas:** [lista — todas precisam de conferência]
**Referências aos autos a verificar:** [N] flagadas inline
**Perguntas abertas ao(à) sócio(a):** [premissas que devem ser confirmadas]
**Extensão:** [palavras/páginas vs. padrão da casa]

---

**Confira citações antes de protocolar.** Citações nesta peça foram geradas por modelo de IA e NÃO foram verificadas contra fonte primária. Rode cada lei, súmula, acórdão e doutrina contra JusBrasil, Lexml, sítios oficiais (STF/STJ/TST) ou plataforma de pesquisa da banca para precisão, vigência e status (não superado, não revogado). Citações fabricadas ou mal transcritas em peça protocolada resultam em **litigância de má-fé (CPC art. 80) e podem caracterizar infração ética (EOAB; CED-OAB)**.

**Rascunho — não é peça pronta.** Protocolar inicia (ou movimenta) processo e carrega responsabilidade profissional do(a) advogado(a) inscrito(a) na OAB (EOAB arts. 31-33). Advogado(a) revisa, edita e assume responsabilidade antes do protocolo. Não protocole sem revisão.
```

## Exposição dos fatos — específicos

A exposição dos fatos é advocacia por seleção e sequência, não argumento.

- Cronológica salvo motivo
- **Todo fato deve referenciar autos** (fls., evento PJe, número Bates de produção interna, mídia)
- Selecione: qual fato lidera, qual recebe uma linha, qual é omitido
- Sem argumento. "O contrato exigia X" é fato. "O contrato inequivocamente exigia X" é argumento.

## Fundamentos jurídicos — específicos

- Lidere com a lei (CC, CDC, CLT, CPC...) ou a tese, conforme o caso
- Um argumento por tópico
- Enfrente o melhor contra-argumento — não fuja
- Citações ganham espaço se acrescentarem algo

## O que esta skill não faz

- Produzir peça final. Produz rascunho.
- Decidir estratégia.
- Protocolar. Nunca.
