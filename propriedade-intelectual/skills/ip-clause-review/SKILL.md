---
name: ip-clause-review
description: >
  Revisa as cláusulas de PI de um acordo — cessão (LPI art. 58/134),
  titularidade, licenças, garantias, indenidades. Use para revisar termos de
  PI em contratos de trabalho (CLT/CC 92), prestação de serviços, ordem de
  serviço (OS/SOW), fornecedor ou licenciamento; quando pedirem para checar
  a redação de cessão ou escopo de licença; ou quando contrato com
  provisões de PI for colado ou anexado.
argument-hint: "[caminho de arquivo | link Drive | colar texto]"
---

# /ip-clause-review

Revisa as cláusulas de PI de um acordo contra o perfil de prática em
`~/.claude/plugins/config/claude-for-legal/propriedade-intelectual/CLAUDE.md`. Sinaliza
lacunas de cessão, ambiguidade de titularidade, problemas de escopo de
licença e problemas de garantia/indenidade de PI. Produz memorando com
achados por cláusula, priorizados por risco, com sugestão de redline onde
cabível.

## Instruções

1. **Carregar `~/.claude/plugins/config/claude-for-legal/propriedade-intelectual/CLAUDE.md`.**
   Se houver placeholders, pare e oriente: "Rode
   `/propriedade-intelectual:cold-start-interview` primeiro — preciso aprender seu perfil
   de prática antes de revisar cláusulas de PI contra ele."

2. **Obter o contrato:** por caminho de arquivo, link Drive ou texto colado.
   Se nenhum fornecido, pergunte.

3. **Siga o workflow abaixo.** Em particular:
   - Estabeleça o tipo do contrato e em qual lado a empresa está para PI
     (concedendo / recebendo / ambos). A pergunta de lado é por documento,
     não resposta única de setup.
   - Rode o check de lacuna de cessão primeiro se o contrato for vínculo
     empregatício, prestação de serviços, OS, ou trabalho sob encomenda.
   - Produza achados por cláusula priorizados por risco.
   - Cheque consistência cross-cláusula, não apenas cláusula-por-cláusula.
   - Anote implicações de jurisdição (direitos morais — Lei 9.610 art. 27
     são irrenunciáveis e inalienáveis; obra por encomenda; licença
     implícita; indenidade de patente).

4. **Produza o memorando** conforme o template — cabeçalho de work-product
   primeiro, bottom line, check de lacuna de cessão, cláusulas por
   severidade, sinalizações de consistência, nota de jurisdição,
   roteamento de aprovação.

5. **Respeite a postura de decisão.** Quando uma cláusula puder ser lida
   para alocar PI em qualquer direção, sinalize para revisão de advogado(a)
   e exponha os fatores cortando para ambos os lados. Nunca decida
   silenciosamente uma questão subjetiva de alocação.

## Exemplos

```
/propriedade-intelectual:ip-clause-review ~/Documents/os-fornecedor.pdf
/propriedade-intelectual:ip-clause-review https://docs.google.com/document/d/...
/propriedade-intelectual:ip-clause-review
```

---

## Contexto de matéria

**Contexto de matéria.** Cheque `## Workspaces de matéria` no CLAUDE.md de
nível de prática. Se `Habilitado` for `✗` (default para in-house), pule o
resto deste parágrafo — skills usam contexto de nível de prática e a
maquinaria de matéria fica invisível. Se habilitado e não houver matéria
ativa, pergunte: "De qual matéria é isto? Rode
`/propriedade-intelectual:matter-workspace switch <slug>` ou diga `nível de prática`."
Carregue o `matter.md` da matéria ativa para contexto e overrides
específicos. Grave saídas na pasta da matéria em
`~/.claude/plugins/config/claude-for-legal/propriedade-intelectual/matters/<matter-slug>/`.
Nunca leia arquivos de outra matéria salvo se `Contexto cross-matéria`
estiver `on`.

---

## Propósito

Ler as cláusulas de PI de um acordo e dizer ao(à) advogado(a) o que cada
uma faz, como desvia do mercado ou da posição padrão da equipe, qual o
risco, e — onde cabível — o redline específico a propor. O objetivo é um
memorando em que o(a) advogado(a) possa agir em uma passada.

**As cláusulas de maior risco na maioria dos acordos são titularidade e
cessão de PI.** São difíceis de consertar depois. Falha em obter cessão
limpa em vínculo empregatício, OS ou prestação de serviços aparece em
auditoria legal de M&A, em rodada de captação e em litígio, às vezes anos
depois de o acordo ter sido assinado. Se a redação de cessão for fraca ou
ausente em documento que deveria tê-la, sinalize alto no topo do memorando
— não enterre como mais um item entre muitos.

## Pré-condição: carregar o perfil de prática

**Antes de ler o acordo, leia
`~/.claude/plugins/config/claude-for-legal/propriedade-intelectual/CLAUDE.md`.** Se
faltar ou ainda contiver placeholders, pare e rode
`/propriedade-intelectual:cold-start-interview`. O perfil de prática lhe diz:

- O footprint de jurisdição — que afeta se renúncia a direitos morais é
  exequível (Lei 9.610 art. 27: direitos morais são **inalienáveis e
  irrenunciáveis** no Brasil — renúncia ampla é nula); se invenção do
  empregado é do empregador, do empregado ou comum (LPI arts. 88-93; CLT
  art. 454); como amplas podem ser as concessões de licença
- Quem aprova desvios e em qual severidade
- O cabeçalho de work-product a prefixar nas saídas

## Workflow

### Passo 1: Orientar

Leia o contrato inteiro uma vez, rápido. Responda:

| Pergunta | Resposta |
|---|---|
| Que tipo de acordo é? | Vínculo CLT / prestação de serviços ou OS / MSA de fornecedor / licença de entrada / licença de saída / colaboração ou termo de cooperação / acordo / aquisição ou compra de ativos / outro |
| Em qual lado estamos para PI? | Concedendo direitos ou recebendo / cedendo PI ou adquirindo / licenciante ou licenciada |
| Quem é a contraparte? | Nome e sofisticação — pessoa física, startup, BigCo |
| Há contraprestação fluindo especificamente pela PI? | Salário, honorário, royalty, pagamento à vista, equity, nenhum |
| Lei aplicável e foro | O que diz — e nosso perfil de prática sinaliza essa jurisdição como escalar/nunca? |

A pergunta de lado é por documento, não resposta única de setup.
Advogado(a) in-house revisando contrato de trabalho está do lado
"recebendo"; revisando licença de saída no mesmo dia, do lado
"concedendo". A postura inverte.

Se o lado for ambíguo (cooperação em que ambas as partes contribuem e
ambas recebem direitos, contrato de revenda com PI passante), pergunte:

> Em qual lado [empresa] está para a PI deste acordo? Concedendo,
> recebendo, ou ambos? Se ambos, revisarei cada direção em separado.

### Passo 2: Check de lacuna de cessão (prioridade máxima)

Se o acordo for vínculo empregatício, prestação de serviços, OS,
encomenda de obra ou qualquer outro em que a empresa deveria estar
recebendo cessão de PI da contraparte sobre o produto do trabalho — cheque
a redação de cessão primeiro.

Procure:

- **Cessão em tempo presente** ("cede neste ato em caráter total, definitivo
  e irrevogável" ou "transfere por meio deste instrumento"). Mera "se obriga
  a ceder" é promessa de cessão, não cessão, e pode exigir segundo
  instrumento para aperfeiçoar. Para marca e patente, a cessão também
  precisa de **averbação no INPI** para produzir efeitos contra terceiros
  (LPI art. 59, 136, 137, 211).
- **Escopo** — cobre toda PI criada no curso do engajamento, ou apenas PI
  relacionada ao negócio da empresa, ou apenas PI criada com recursos da
  empresa? Escopo estreito é lacuna se se espera que o produto do trabalho
  varie amplamente.
- **Invenções do empregado vs do empregador** (LPI arts. 88-93): se decorre
  de contrato cuja execução ocorra no Brasil e que tenha por objeto a
  pesquisa ou em que a atividade inventiva do empregado seja prevista, a
  invenção pertence **exclusivamente ao empregador** (art. 88). Se não
  ligada ao contrato e sem uso de recursos, é do empregado (art. 90). Caso
  comum (recursos do empregador + tempo livre) → propriedade comum em
  partes iguais (art. 91). Cláusula que tente alargar o art. 88 para fora
  de seu escopo legal é objeto de questionamento.
- **Direitos morais (Lei 9.610 art. 24/27).** **No Brasil são
  irrenunciáveis e inalienáveis.** Cláusulas de renúncia ampla são
  ineficazes; o que se pode é (a) autorização de uso, (b) cessão
  patrimonial com manutenção dos direitos morais ao autor, (c) compromisso
  contratual de não exercer a paternidade em modos específicos —
  exequibilidade contestável. Anote sempre.
- **Cláusula de garantia ulterior (further assurances)** — contraparte
  obriga-se a assinar o que mais for necessário para aperfeiçoar a cessão
  depois (procuração para averbação INPI, instrumentos específicos).
- **Ressalva de PI preexistente** — o que a contraparte exclui da cessão,
  e essa lista é específica ou aberta?

Se algum dos itens acima estiver faltando ou fraco, sinalize no topo do
memorando com severidade 🔴 ou 🟠 e um redline específico.

```markdown
## ⚠️ LACUNA DE CESSÃO

**Seção [X]** cede PI no produto do trabalho, mas: [problema específico —
ex.: "'se obriga a ceder' em vez de 'cede neste ato'," ou "ausência de
ressalva da inalienabilidade dos direitos morais (Lei 9.610 art. 27)," ou
"nenhuma lista de PI preexistente fornecida e a contraparte tem PI de
plataforma anterior"].

**Risco:** Esse é o tipo de lacuna que aparece em diligência de M&A anos
depois. A contraparte (ou sucessora) pode ter direitos residuais sobre
produto do trabalho que pensávamos possuir. Para marca/patente, sem
averbação INPI a cessão não produz efeito contra terceiros.

**Redline proposto:**
> "[redação substitutiva específica]"

**Escalada:** Conforme `~/.claude/plugins/config/claude-for-legal/propriedade-intelectual/CLAUDE.md`,
lacunas de escopo de cessão escalam para [aprovador].
```

> **A cessão pode transmitir conteúdo gerado por IA?** A Lei 9.610 protege
> obras intelectuais resultantes da **criação do espírito humano** (art.
> 7º; art. 11 define autor como pessoa física). Conteúdo puramente gerado
> por IA sem autoria humana provavelmente **não é obra autoral**
> protegida — e cessão só transmite direitos que existem. Para patente, a
> LPI exige inventor pessoa natural (art. 6º). `[review — status de
> autoria/inventoria de obras geradas por IA no Brasil está em
> desenvolvimento; verificar contra orientação atualizada do INPI e
> jurisprudência]`
>
> Cheque: o acordo tem obrigação de divulgação de uso de IA? Representação
> sobre o papel da IA nos entregáveis? Mecanismo para identificar quais
> partes são assistidas por IA vs autoradas por humano?
>
> Se ausente e criação assistida por IA é previsível (consultoria,
> desenvolvimento, criação de conteúdo, design): 🟠 Alto. "Cláusula de
> cessão bem redigida, mas sem divulgação de uso de IA. Direito autoral
> de conteúdo gerado por IA é incerto, e sem obrigação de divulgação você
> não saberá quais porções são afetadas. Adicione representação de uso de
> IA e obrigação de divulgação."

> **Inventoria assistida por IA.** Patente com inventoria incorreta é
> vulnerável a nulidade. Se um(a) prestador(a) usa ferramentas de IA que
> contribuem ao conceito inventivo, a questão da inventoria fica
> complicada. Para qualquer acordo com provisões de cessão de patente
> sobre produto do trabalho potencialmente patenteável:
>
> Cheque: o acordo tem representação de uso de IA? Processo para
> determinação de inventoria onde IA contribuiu? Obrigação de divulgação
> sobre uso de IA no processo inventivo?
>
> Se ausente: sinalize. "Cessão de patente sem representação de uso de
> IA. Se ferramentas de IA contribuíram para o conceito inventivo, a
> inventoria fica complicada e a patente atribuída incorretamente é
> vulnerável. Adicione representação de uso de IA e protocolo de
> inventoria."

### Passo 3: Revisão cláusula-por-cláusula

Para toda cláusula relevante de PI, produza um bloco. Cláusulas a
procurar:

- **Cessão / obra por encomenda** — quem possui o que é criado sob o
  acordo
- **Titularidade dos entregáveis** — distinto de cessão; frequentemente
  estabelece a saída do engajamento
- **Aperfeiçoamentos e derivados** — quem possui aperfeiçoamentos a PI
  preexistente, quem possui obras derivadas
- **PI de fundo (background) vs PI de primeiro plano (foreground)** — o
  acordo define PI preexistente e PI recém-criada separadamente, e
  licencia a PI de fundo na medida necessária?
- **Concessões de licença** — escopo, exclusividade, território, campo de
  uso, sublicenciabilidade, prazo, gatilhos de rescisão, estrutura de
  royalty ou taxa
- **Garantias de PI** — não-infração de direitos de terceiros, poder para
  conceder, obra original
- **Indenidades de PI** — escopo, teto, procedimento, exclusões
  (modificações pelo usuário, combinações, uso não autorizado)
- **Direitos morais** — observe inalienabilidade Lei 9.610 art. 27
- **Representações sobre open source** — representações sobre que OSS
  está e não está embutido nos entregáveis
- **Uso de marca** — qualquer concessão ou restrição ao uso das marcas
  da outra parte; guidelines de marca; controle de qualidade pelo
  licenciante (essencial sob LPI art. 139: a marca licenciada deve ter
  controle de qualidade pelo titular, sob pena de caducidade por uso
  indevido)
- **Confidencialidade / segredo de empresa** — tratamento de material de
  segredo de empresa (LPI art. 195 XI/XII), medidas razoáveis, devolução
  ou destruição, obrigações pós-rescisão

Para cada cláusula presente, produza:

```markdown
### [Seção X.X]: [Nome da cláusula]

**O que diz:** [resumo em português claro, 1-2 frases]

**O que é mercado (para este tipo de acordo, este lado, esta jurisdição):**
[referência breve]

**Risco:** 🔴 Crítico | 🟠 Alto | 🟡 Médio | 🟢 Baixo

**Por que importa:** [1-2 frases — o que dá errado para o negócio se ficar
como está]

**Redline proposto (se necessário):**
> "[redação substitutiva específica]"

**Decisão:** [Se incerto se a cláusula alcança a alocação de PI
pretendida, sinalize para revisão de advogado(a) e exponha os fatores
cortando para ambos os lados. Não decida silenciosamente questão
subjetiva de alocação.]
```

**Calibração de severidade:**

| Nível | Significa |
|---|---|
| 🔴 Crítico | Não assine sem corrigir. Lacuna de cessão em documento que deveria tê-la. Licença ampla onde estreita era pretendida. Concessão exclusiva onde não-exclusiva era pretendida. |
| 🟠 Alto | Pressione forte; escale se não cederem. Escopo ambíguo, falta de ressalva quanto a direitos morais, falta de garantia ulterior, indenidade estreita. |
| 🟡 Médio | Pressione na primeira rodada; aceite se for o último ponto em aberto. Redação cosmética mas imprecisa, períodos de sobrevivência mais curtos que padrão. |
| 🟢 Baixo | Anote, não gaste capital. Desvio estilístico que não muda a alocação. |

### Passo 4: Consistência cross-cláusula

Cláusulas de PI falham como sistema. Cheque:

- **A concessão de licença bate com o escopo do que está sendo
  licenciado?** ("Usar" o entregável é mais estreito que "usar, modificar
  e criar obras derivadas".)
- **As garantias cobrem tudo o que a concessão cobre?** (Garantia de
  não-infração limitada a patentes, em licença que também cobre direito
  autoral e segredo de empresa, deixa lacunas.)
- **A indenidade cobre o que a garantia promete?** (Garantia sem
  indenidade é promessa sem remédio.)
- **A rescisão puxa a licença de volta?** (Ou licença paga sobrevive à
  rescisão? Ambas defensáveis — a questão é se bate com a intenção.)
- **A alocação de PI entre este acordo e qualquer OS, ordem de compra ou
  side letter relacionados é consistente?** Sinalize conflitos.

### Passo 5: Nota de jurisdição

Regras de PI são específicas à jurisdição em modos que mudam o resultado.
Sinalize se o acordo implica algum destes:

- **Direitos morais** — **Brasil reconhece amplamente** (Lei 9.610 art.
  24/27). São inalienáveis e irrenunciáveis. Cláusula de renúncia ampla
  é nula. UE, Canadá similares. EUA reconhece de forma estreita (VARA,
  para artes visuais).
- **Invenção do empregado** — Brasil: LPI 88-93 (regime tripartite:
  empregador / empregado / comum). EUA: depende do contrato + statute of
  state. UE varia.
- **Licença implícita** — common law pode ler licença implícita onde a
  concessão silencia. **Brasil (Lei 9.610 art. 4º): interpretação
  restritiva** — direitos não expressamente cedidos permanecem com o
  autor.
- **Cessão de marca/patente** — requer **averbação no INPI** para
  oponibilidade a terceiros (LPI 59/136/137/211).
- **Exclusões de indenidade de patente** — combinações, modificações e
  fornecimento pelo usuário de elementos acusados são exclusões padrão
  globalmente; verifique se foram negociadas.

Indique a lei aplicável e o foro do acordo, e se o perfil de prática
sinaliza essa jurisdição como padrão, escalar ou nunca.

## Granularidade de redline

**Edite na menor granularidade possível.** Redline é artefato de
negociação, não reescrita. Substituição de cláusula inteira sinaliza
"jogamos fora sua redação" — é agressivo, força a contraparte a re-ler a
cláusula toda e descarta as partes da redação dela que estavam ok.
Redlines cirúrgicos — riscar palavra, inserir expressão, reestruturar
sub-cláusula — sinalizam "temos pedidos específicos" e são mais rápidos
de ler, entender e aceitar.

Default para a menor edição que atinja a posição de playbook:
- Substitua **palavra** antes de expressão. ("doze (12)" → "vinte e
  quatro (24)")
- Substitua **expressão** antes de frase.
- Reestruture **sub-cláusula** antes de substituir a frase. (Adicione
  "(a)" e "(b)" para dividir condição composta.)
- Substitua **frase** antes de substituir a cláusula.
- Só substitua **cláusula inteira** quando a versão da contraparte estiver
  tão longe da sua posição que edições cirúrgicas seriam mais difíceis de
  ler que minuta nova — e quando o fizer, diga na transmissão: "Subscritamos
  novamente o §8.2 em vez de marcá-lo porque as mudanças eram extensas.
  Disponível para passar pelo delta com vocês."

Em dúvida, menor. Cliente que recebe redline cirúrgico confia que você
leu com atenção. Cliente que recebe substituição atacadista pergunta se
você leu mesmo.

### Passo 6: Montar o memorando

Prefixe o cabeçalho de work-product de
`~/.claude/plugins/config/claude-for-legal/propriedade-intelectual/CLAUDE.md` →
`## Outputs` (varia por papel — vide `## Quem está usando`).

Este memorando e o acordo subjacente podem estar sob sigilo profissional,
confidenciais, ou ambos. A saída herda esse status da fonte. Distribua
apenas dentro do círculo do sigilo profissional; marque e armazene onde
materiais sigilosos vivem; remova o cabeçalho de work-product antes de
qualquer entrega externa.

> **Sem suplementação silenciosa.** Se consulta a ferramenta configurada
> de pesquisa retornar poucos ou nenhum resultado para regra que o
> memorando precisa (exequibilidade de cláusula de direitos morais em
> jurisdição dada, escopo de licença implícita, padrão para período de
> sobrevivência de garantia de PI), reporte o achado e pare. NÃO preencha
> a lacuna a partir de web search ou conhecimento do modelo sem
> perguntar. Diga: "A busca retornou [N] resultados de [ferramenta].
> Cobertura aparenta ser fina para [regra / jurisdição]. Opções: (1)
> ampliar a query, (2) tentar outra ferramenta, (3) buscar na web — os
> resultados serão tagueados `[web search — verificar]` e devem ser
> checados contra fonte primária, ou (4) sinalizar como não-verificado e
> parar. Qual prefere?" Advogado(a) decide se aceita fontes de menor
> confiança.
>
> **Atribuição de fonte.** Onde o memorando citar lei, regulamento,
> jurisprudência ou doutrina, tague a citação: `[Planalto / DOU]`,
> `[INPI]`, `[STJ]`, `[STF]`, `[TJ-XX]`, `[JusBrasil]`, ou o nome da
> ferramenta MCP para citações recuperadas por conector; `[web search —
> verificar]` para citações de busca web; `[conhecimento do modelo —
> verificar]` para citações recordadas de treinamento; `[fornecido pelo
> usuário]` para citações da minuta da contraparte ou de arquivos da
> casa. Citações tagueadas `verificar` carregam risco de fabricação
> maior e devem ser checadas primeiro. Nunca remova ou colapse as tags.

```markdown
[CABEÇALHO DE WORK-PRODUCT — conforme config do plugin ## Outputs]

# Revisão de Cláusulas de PI: [Contraparte] [Tipo de Acordo]

**Revisado:** [data]
**Nosso lado para PI:** [Concedendo / Recebendo / Ambos]
**Lei aplicável:** [jurisdição]

---

## Bottom line

[Duas frases. A alocação de PI fica de pé? O que tem de mudar primeiro?]

**Issues:** [N]🔴 [N]🟠 [N]🟡 [N]🟢

**Aprovação necessária de:** [nome, conforme perfil de prática]

---

## Check de lacuna de cessão

[✅ Limpa | ⚠️ Lacuna presente — vide acima]

---

## Cláusulas por severidade

[Todos os blocos do Passo 3, agrupados Crítico → Baixo]

---

## Consistência cross-cláusula

[Sinalizações do Passo 4]

---

## Nota de jurisdição

[Sinalizações do Passo 5]

---

## Roteamento de aprovação

[Do perfil de prática — quem aprova, o que dispara escalada automática]
```

## Postura de decisão

Quando uma cláusula puder ser lida para alocar PI em qualquer direção, ou
quando for incerto se as palavras escolhidas pelo redator atingem a
intenção declarada, **sinalize para revisão de advogado(a) e exponha os
fatores cortando para ambos os lados**. Não decida silenciosamente questão
subjetiva de alocação. Alocação de PI não resolvida que vai a assinatura é
porta de uma via — o erro aparece em diligência, captação ou litígio.
Sinalizar cláusula ambígua que acaba indo bem é porta de duas vias.

## Checagens de qualidade antes de entregar

- [ ] Perfil de prática foi carregado e a nota de jurisdição reflete o
  que está lá
- [ ] Lacuna de cessão checada primeiro (para vínculo/OS/prestação de
  serviços/encomenda)
- [ ] Toda issue 🔴 e 🟠 tem redação substitutiva específica
- [ ] Consistência cross-cláusula checada, não apenas cláusula-por-cláusula
- [ ] Tags de fonte aplicadas a citações; nenhuma tag `verificar` removida
- [ ] Aprovador nomeado conforme perfil de prática, não "escalar ao
  jurídico"
- [ ] Saída marcada com o cabeçalho de work-product

## Encerre com a árvore de decisão de próximos passos

Encerre com a árvore de decisão de próximos passos conforme CLAUDE.md
`## Outputs`. Customize as opções ao que esta skill acabou de produzir —
os cinco ramos default (minutar X, escalar, buscar mais fatos, observar e
esperar, outra coisa) são ponto de partida, não trava. A árvore é a
saída; o(a) advogado(a) escolhe.
