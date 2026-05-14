---
name: marketing-claims-review
description: >
  Revisa copy de marketing para claims que precisam de substanciação, reframing
  ou corte. Use quando o usuário disser "revise esta copy", "cheque estes claims",
  "podemos dizer isso", "isso é puffery ou problema" ou colar conteúdo de marketing
  (landing pages, e-mails, ads, taglines).
argument-hint: "[cole a copy ou o caminho do arquivo]"
---

# /marketing-claims-review

> **Adaptação BR.** Calibrado para CDC (Lei 8.078/1990 — arts. 30, 31, 36–38, 39), Código CONAR, CONANDA Res. 163/2014 (publicidade dirigida a criança), Lei 9.294/96 (tabaco/álcool/medicamento), Resoluções ANVISA (RDC 96/2008 medicamentos; RDC 24/2010 alimentos com restrição), Lei 13.787/2018 (saúde), Lei 14.181/2021 (superendividamento), e Resolução CONAR sobre influenciadores. STJ Súmula 281: ônus da prova em publicidade enganosa é objetivo. Veja `references/terminology-us-to-br.md`.

1. Carregue `~/.claude/plugins/config/claude-for-legal/produto/CLAUDE.md` → padrões de claims de marketing.
2. Aplique a taxonomia de claims e o workflow abaixo.
3. Extraia todo claim. Classifique: puffery / factual / comparativo / implícito / absoluto.
4. Para cada claim não-puffery: check de substanciação (ônus de quem patrocina — CDC art. 36 §único), fix sugerido.
5. Output: claim-a-claim com decisões, revisão sugerida se curto.

```
/produto:marketing-claims-review
[cole copy de landing page]
```

---

## Matter context

**Matter context.** Cheque `## Matter workspaces` no CLAUDE.md de practice-level. Se `Enabled` é `✗` (default para in-house), pule o resto deste parágrafo — skills usam contexto practice-level e o esquema de matter fica invisível. Se habilitado e não há matter ativa, pergunte: "Para qual matéria é? Rode `/produto:matter-workspace switch <slug>` ou diga `practice-level`." Carregue o `matter.md` da matter ativa para contexto e overrides. Escreva outputs na pasta da matter em `~/.claude/plugins/config/claude-for-legal/produto/matters/<matter-slug>/`. Nunca leia arquivos de outra matter salvo se `Cross-matter context` está `on`.

---

## Propósito

Marketing quer dizer que o produto é o melhor. Jurídico precisa que seja verdade, ou ao menos que não seja provavelmente falso. Esta skill encontra os claims que vão atrair notificação extrajudicial de concorrente (CDC art. 39 IV; LPI art. 195 III — concorrência desleal), representação no CONAR ou autuação SENACON/Procon (CDC arts. 56 e ss.), e sugere como manter a energia consertando a exposição.

## Carregue padrões

Leia `~/.claude/plugins/config/claude-for-legal/produto/CLAUDE.md` → `## Claims de marketing`:
- Política de claims comparativos (admitida com substanciação — CONAR Código art. 32 / desencorajada / nunca)
- Padrão de substanciação (o que é exigido antes do claim ir ao ar — CDC art. 36 §único: ônus de quem patrocina)
- Claims comumente rejeitados (aprenda do histórico)

## Pesquise os padrões aplicáveis antes de liberar copy

Pesquise os padrões correntes de publicidade e substanciação para jurisdição e mídia aplicáveis (por exemplo, CDC + Decreto 2.181/1997 — Sistema Nacional de Defesa do Consumidor; CONAR — Código e Resoluções; SENACON / Procons estaduais; ANVISA — RDC 96/2008 para medicamento, RDC 24/2010 para alimento; Lei 9.294/96 para tabaco/álcool; Lei 9.279/96 art. 195 III para concorrência desleal; CONANDA Res. 163/2014 se infanto-juvenil; políticas de plataforma; sigilo do anunciante). Identifique a substanciação que *o claim específico* exige — quem mediu, quando, tamanho da amostra, comparação maçã-com-maçã (CONAR art. 32) — não só se *alguma* substanciação existe arquivada. Sinalize claims implícitos e comparativos para escrutínio reforçado. Verifique atualidade: o Código CONAR e Resoluções (especialmente sobre influenciadores — Res. CONAR sobre marketing de influência) atualizam. Cite fontes primárias com pinpoints. Se não consegue verificar o padrão atual, sinalize para verificação por advogado(a) — não afirme regra que não confirmou.

> **Cite só os padrões que se aplicam aos claims específicos sob revisão.** Lista cega de toda diretriz CONAR, toda norma SENACON ou toda regra setorial torna invisíveis as que sustentam o resto. Não cite Resolução CONAR de influenciador a menos que a copy contenha endosso, testemunho ou conteúdo de influenciador. Não cite regras de disclosure overlay a menos que um claim no ativo dispare o overlay. Não cite regulador setorial (ANVISA, ANS, ANATEL) a menos que a copy mire ou implique aquele setor. Um padrão ganha seu lugar no output mapeando para um claim citado específico; caso contrário, retire.

> **Sem complementação silenciosa.** Se query à ferramenta de pesquisa configurada retorna poucos ou nenhum resultado para o padrão aplicável (regra do CDC, decisão CONAR, decisão SENACON / Procon, regra setorial, política de plataforma), reporte o que foi encontrado e pare. NÃO preencha lacuna com busca web ou conhecimento de modelo sem perguntar. Diga: "A busca retornou [N] resultados de [ferramenta]. Cobertura parece fina para [padrão / jurisdição]. Opções: (1) ampliar query, (2) tentar outra ferramenta, (3) buscar web — resultados marcados `[busca web — verificar]` e devem ser checados contra autoridade emissora antes de confiar, ou (4) sinalizar como não verificado e parar. Qual prefere?" Um(a) advogado(a) decide.
>
> **Tiering de atribuição de fonte.** Marque toda citação com sua origem. Para citações de conhecimento de modelo, use um de três níveis em vez de tag "verificar" genérica:
>
> - `[estável]` — referências estatutárias estáveis e conhecidas, improváveis de ter mudado (ex.: CDC art. 36, CDC art. 37 §1º, LPI art. 195 III). Verifique antes de aprovar copy, mas prioridade menor.
> - `[verificar]` — citações de conhecimento de modelo que são reais mas devem ser verificadas: decisões CONAR específicas, autos SENACON, Súmulas STJ sobre consumo, regras setoriais (RDCs da ANVISA), políticas de plataforma, datas de vigência, atualizações recentes (Resolução CONAR de influenciadores atualiza com frequência).
> - `[verificar-pinpoint]` — pinpoints (incisos, alíneas, parágrafos específicos do CDC ou CONAR; números de auto administrativo) carregam o maior risco de fabricação e devem SEMPRE ser verificados contra fonte primária.
>
> Citações recuperadas mantêm sua tag de fonte (`[JusBrasil]`, `[Escavador]`, `[LexML]`, `[Planalto]`, `[STJ]`, `[CONAR]`, `[SENACON]`, `[ANVISA]`, `[política de plataforma]`, ou nome da MCP); busca web fica `[busca web — verificar]`; fornecidas pelo usuário (de arquivos de substanciação) ficam `[usuário forneceu]`. O tiering traz à tona o trabalho de verificação real — leitor que verifica tudo verifica nada. Nunca tire ou compacte as tags.

## Taxonomia de claims

As categorias abaixo são padrões estruturais que o revisor deve conseguir reconhecer. Se uma frase é acionável depende da regra correntemente operativa na jurisdição aplicável, da substanciação específica disponível e do público — pesquise antes de concluir.

### Claims vagos / subjetivos (puffery)

Asserções subjetivas sem conteúdo mensurável. Se são acionáveis depende de jurisdição, contexto e público — pesquise antes de concluir. **Atenção BR:** o CDC art. 37 §1º trata "publicidade enganosa por inteiro ou em parte"; CDC art. 37 §2º trata abusiva. Puffery em sentido próprio existe, mas STJ admite linha tênue — superlativos absolutos sem prova podem ser enganosos.

| Exemplo |
|---|
| "A melhor forma de gerenciar seus projetos" |
| "Você vai amar" |
| "Revolucionário" |

### Claims factuais específicos

Mensuráveis, específicos, pessoa razoável pode confiar. Sob CDC art. 30, oferta vincula; sob CDC art. 36 §único, ônus da prova é de quem patrocina.

| Exemplo | Substanciação a procurar |
|---|---|
| "50% mais rápido que [concorrente]" | Dado de benchmark, metodologia disclosure, data; CONAR art. 32 (comparação maçã-com-maçã) |
| "Confiança de 10.000 empresas" | Contagem efetiva (não cadastros cumulativos — *atualmente* clientes ativos) |
| "Economiza 5 horas por semana" | Estudo ou dado de cliente, amostra disclosure |
| "Segurança enterprise-grade" | O que isso significa? ISO 27001? PCI-DSS? Especifique ou é promessa (CDC art. 30) |
| "Conformidade LGPD" | Programa de privacidade efetivo, encarregado(a) nomeado(a), RIPD em escopo aplicáveis — esta é promessa contratual implícita |

### Claims comparativos (escrutínio reforçado)

Nomear concorrente ou implicá-lo. Pesquise regras de publicidade comparativa nas jurisdições e mídias relevantes antes de liberar.

**Regime BR:** CONAR Código art. 32 admite publicidade comparativa, **desde que** (a) leal, sem denegrir; (b) baseada em dados objetivos e verificáveis; (c) comparando características essenciais e checáveis; (d) sem causar confusão. CDC art. 36 §único: ônus da prova de quem patrocina. LPI art. 195 III pune como crime de concorrência desleal o uso de meio fraudulento para desviar clientela. STJ admite comparação não-denigratória.

| Exemplo | Padrão de fix |
|---|---|
| "Mais rápido que [Concorrente]" | Ou nomeie o concorrente com dado head-to-head defensável, ou abstraia para "mais rápido que ferramentas legadas de chat" com substanciação |
| "A única plataforma que faz X" | Falso se qualquer outra faz X — "A primeira plataforma a..." (se verdade) ou retire "única" |
| "[Concorrente] não consegue fazer isto" | Mostre a sua feature. Deixe o leitor comparar. (Evita LPI 195 III) |

Conforme `~/.claude/plugins/config/claude-for-legal/produto/CLAUDE.md` — se claims comparativos são "nunca", sinalize todos. Se "admitidos com substanciação", cheque a substanciação.

### Claims implícitos

Não declarados expressamente mas leitor razoável infere. Pesquise tratamento de claims implícitos sob o regime de publicidade aplicável — claims implícitos frequentemente carregam o mesmo ônus de substanciação dos expressos (CDC art. 36; CONAR Código).

| Exemplo | Implicação | Fix |
|---|---|---|
| "Finalmente, uma alternativa segura" | Concorrentes são inseguros | "Finalmente, segurança que você verifica" |
| Logos de clientes sem contexto | Estas empresas nos endossam | "Clientes incluem..." está OK; "Confiado por..." implica mais |
| "Feito para saúde" | Compliance HIPAA-equivalente / ANVISA / Res. CFM | Esclareça ou qualifique |

### Claims absolutos

Sem margem para erro. Um contraexemplo torna falso. Pesquise se qualificações sanam o issue na jurisdição aplicável. **Atenção BR:** CDC art. 37 §1º é robusto — "qualquer modalidade de informação ou comunicação de caráter publicitário, inteira ou parcialmente falsa, ou, por qualquer outro modo, mesmo por omissão, capaz de induzir em erro" é enganosa.

| Exemplo | Padrão de fix |
|---|---|
| "Nunca cai" | "99,9% de uptime" (com SLA que defina) |
| "100% preciso" | Percentual específico, substanciado, atrelado a benchmark |
| "Garantido" | Só se você de fato oferece garantia com termos — cria exposição de garantia legal e contratual (CC art. 441 e ss.; CDC arts. 24 e 50) |
| "Sempre" / "Todo" | "Tipicamente" / "Maioria" |
| "Líder de mercado" | Sem fonte verificável de market share — risco CDC art. 37 §1º e CONAR |

## A revisão

### Passo 1: Extraia todo claim

Leia a copy. Liste toda frase ou expressão que afirma fato, faz comparação ou promete algo. Ignore puffery puro na lista.

### Passo 2: Classifique e cheque

Para cada claim:

```markdown
**Claim:** "[citação exata]"
**Tipo:** [Factual específico | Comparativo | Implícito | Absoluto]
**Substanciação arquivada:** [Sim — link | Não | Desconhecido]
**Decisão:** [✅ OK | ⚠️ Precisa substanciação | ⚠️ Precisa reformulação | 🔴 Cortar]
**Fix sugerido:** "[redação alternativa que mantém a energia]"
**Por quê:** [uma linha — citar CDC art. 36/37, CONAR art. 32, Súmula STJ etc.]
```

### Passo 3: Cheque contra o produto

O produto de fato faz o que a copy diz? Não é pergunta filosófica — cheque o PRD ou pergunte ao(à) PM.

Drift comum: copy de marketing escrita de spec inicial, produto mudou, ninguém atualizou a copy. Risco: CDC art. 30 vincula a oferta; CDC art. 35 dá ao consumidor escolha entre exigir cumprimento forçado, aceitar produto equivalente ou rescindir.

### Passo 4: Output

Anexe o cabeçalho de material de trabalho de `~/.claude/plugins/config/claude-for-legal/produto/CLAUDE.md` `## Outputs` (varia por papel — veja `## Quem está usando isto`).

```markdown
[CABEÇALHO DE MATERIAL DE TRABALHO — conforme config do plugin ## Outputs]

# Revisão de Marketing: [Nome da campanha/ativo]

**Revisado:** [data]
**Ativo:** [landing page / e-mail / ad / etc.]

---

## Resumo

[N] claims revisados. [N]✅ [N]⚠️ [N]🔴

**Pronto para publicar:** [Sim | Com mudanças abaixo | Não — reescrita necessária]

> **Antes de emitir "Pronto para publicar: Sim" (i.e., aprovar claim para uso externo / publicação):** Leia `## Quem está usando isto` em `~/.claude/plugins/config/claude-for-legal/produto/CLAUDE.md`. Se o Papel é Não-advogado:
>
> > Aprovar claim de marketing para publicação é ato jurídico — uma vez publicado, lacunas de substanciação e exposição em claim comparativo viram risco de autuação (SENACON / Procons), de representação no CONAR, e de ação por concorrente (cessação de uso + danos sob LPI art. 195 III e CDC arts. 36–38). Você revisou isto com advogado(a) inscrito(a) na OAB? Se sim, prossiga. Se não, eis um brief para levar:
> >
> > [Gere resumo de 1 página: ativo, claims aprovados, tipos (factual / comparativo / implícito / absoluto), substanciação arquivada para cada, claims implícitos sinalizados, e as três coisas a perguntar ao(à) advogado(a) antes da copy ir ao ar.]
> >
> > Para achar advogado(a): a OAB de seu estado (seccional) tem serviço de indicação; alternativas: Defensoria Pública (se hipossuficiente) ou NPJ de faculdade.
>
> Não prossiga para "Pronto para publicar: Sim" sem "sim" explícito. "Com mudanças abaixo" e "Não — reescrita necessária" não exigem este gate.

---

## Claim a claim

[Todos os blocos do Passo 2, agrupados: 🔴 primeiro, depois ⚠️, depois ✅]

---

## Revisão sugerida

[Para ativos curtos — menos de 50 palavras, ou um tweet, headline, one-liner, tagline, ad curto — o output neste bloco é a copy revisada com fixes aplicados inline, não descrição do que mudou. O leitor deve conseguir copy-paste deste bloco no ativo.
Para ativos mais longos (>50 palavras mas <300), mostre copy revisada com fixes inline.
Para ativos longos (300+), resuma mudanças como diff em bullets ("Cortar Claim 1. Reescrever Claim 3 retirando 'qualquer'. Suavizar Claim 4 por risco em domínio regulado.") em vez de colar o ativo inteiro.
Meta-descrição de mudanças nunca é output aceitável para ativo curto — quando o ativo é uma linha, o output deve SER a linha revisada.]

---

## Substanciação necessária antes de publicar

| Claim | Necessidade | De quem |
|---|---|---|
| [claim] | [tipo de dado] | [PM / time de dados / eng] |

---

## Citation check

Qualquer regra do CDC, decisão CONAR, auto SENACON / Procon, regulamento setorial ou política de plataforma citados nesta revisão foram gerados por modelo de IA e não foram verificados contra fonte primária. Antes de confiar em regra específica para liberar ou rejeitar copy, verifique-a contra ferramenta de pesquisa jurídica (JusBrasil, Escavador, LexML, Planalto, base do STF/STJ, ou plataforma do escritório) para precisão e data de vigência — Resoluções do CONAR (especialmente sobre influenciador), regras de plataforma e regimes setoriais (ANVISA, ANS, ANATEL) atualizam com frequência. Tags de fonte em cada citação (ex.: `[CONAR]`, `[busca web — verificar]`) mostram de onde veio; tags `verificar` carregam maior risco de fabricação e devem ser checadas primeiro.
```

## Overlays de disclosure

Copy que envolve qualquer dos fact patterns abaixo entra em regime adicional de disclosure. Pesquise requisitos correntes na jurisdição aplicável (incluindo políticas de plataforma e regras setoriais) e verifique atualidade — esses regimes atualizam com frequência.

- **Testemunhos / reviews** — conexões materiais entre quem fala e o anunciante são tipicamente disclosable (CONAR; STJ); pesquise forma e posicionamento correntes
- **Conteúdo de influenciador** — pesquise requisitos correntes de tagueamento (#publi, #publicidade, #ad), clareza e conspicuidade para o canal e público — **Atenção BR:** Resolução CONAR sobre marketing de influência exige identificação clara; ANCINE/SENACON têm orientações; falha em sinalizar conteúdo pago é prática enganosa (CDC art. 36)
- **"Resultados podem variar" / resultados atípicos** — pesquise se disclosure (e qual forma) é exigido quando resultados mostrados não são representativos (especialmente em saúde, suplementos, perda de peso — RDC ANVISA + CONAR Anexo K)
- **Free trial / auto-renovação / opt-out** — pesquise requisitos correntes de conspicuidade e consentimento para termos de auto-conversão (CDC art. 39 V — vantagem manifestamente excessiva; CDC arts. 30 e 31; possível enquadramento em prática abusiva; Lei 14.181/2021 — superendividamento exige cautela em recorrência)
- **Crianças/adolescentes:** CONANDA Res. 163/2014 considera publicidade dirigida à criança abusiva — não use elementos da linguagem infantil, personagens ou apresentadores infantis, animações, trilhas infantis, representação de criança em produto não-infantil de risco (tabaco, álcool, medicamento, jogos)
- **Setores especiais:** Lei 9.294/96 (tabaco — publicidade só em ponto de venda; álcool e medicamento — restrições); RDC ANVISA 96/2008 (medicamento de prescrição: só para profissional); Lei 14.790/2023 (apostas: vedação a menores; restrições de divulgação)

## Encerre com a árvore de decisão de próximos passos

Encerre com a árvore de decisão conforme CLAUDE.md `## Outputs`. Customize as opções para o que esta skill acabou de produzir — os cinco branches default são ponto de partida, não trava. A árvore É o output; o(a) advogado(a) escolhe.

## O que esta skill NÃO faz

- Não escreve o marketing. Conserta o que está errado. Reescritas sugeridas mantêm a energia, mas marketing é dono da voz.
- Não substancia claims. Identifica quais precisam e quem tem o dado.
- Não revisa design ou imagem — palavras só. Se imagem implica claim (logo de concorrente com X vermelho), sinalize, mas revisão visual é juízo humano.
