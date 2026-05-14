---
name: aia-generation
description: >
  Roda uma avaliação de impacto de IA (AIA) — intake estruturado, análise de
  risco, classificação regulatória por regime aplicável, diff de consistência de
  política e recomendação com condições. Usa a house style aprendida da AIA-semente
  em `~/.claude/plugins/config/claude-for-legal/governanca-ia/CLAUDE.md`.
  Aciona quando o(a) usuário(a) diz "avaliação de impacto para", "avalia esse
  caso de uso de IA", "rode uma AIA", "gera uma AIA", "precisamos documentar
  esse sistema de IA", "avaliação de risco de IA para X", ou após resultado de
  triagem condicional.
argument-hint: "[descreva o caso de uso ou sistema, ou passe um resultado de triagem]"
---

# /aia-generation

> **Adaptação BR não oficial.** AIA aqui é desenho amplo (relatório/avaliação de
> impacto algorítmico) sob o framework brasileiro em construção: PL 2338/2023
> (Marco Legal da IA — `[em tramitação — verificar]`), LGPD arts. 5º, 6º, 7º,
> 11, 18, 20 (decisão automatizada — direito à revisão), 38 (RIPD) e 48
> (incidente), Resoluções CD/ANPD 4/2023 (sandbox), 15/2024 (incidentes) e
> 19/2024 (transferência internacional), princípios OCDE, Resolução CNJ 332/2020
> e 615/2025 (IA no Judiciário). EU AI Act mantido como benchmark global; NIST
> AI RMF como framework técnico; US (EO 14110, CO AI Act, NYC LL144, IL BIPA)
> como referência comparativa. Quando houver tratamento de dados pessoais,
> avalie a necessidade de RIPD (LGPD art. 38) — AIA e RIPD podem se sobrepor
> mas não se substituem.

1. Leia `~/.claude/plugins/config/claude-for-legal/governanca-ia/CLAUDE.md`. Confirme que a house style de AIA está populada.
2. Determine o trilho de risco (rápido ou completo) a partir do tier de governança e das características do caso de uso, usando o framework abaixo.
3. Faça o intake — conversacional, não formulário.
4. Classificação regulatória para cada regime no footprint — trilho de pesquisa, exposição a práticas vedadas e obrigações aplicáveis; cite fontes primárias.
5. Escreva a avaliação na house style (da semente, ou default se não capturado).
6. Diff de política contra os compromissos de IA do `~/.claude/plugins/config/claude-for-legal/governanca-ia/CLAUDE.md`.
7. Output: documento de AIA + lista de condições + flags de handoff (RIPD por privacidade, revisão de fornecedor se necessário).

```
/governanca-ia:aia-generation "Triagem de currículos por IA para RH"
```

---

## Contexto de matéria

**Contexto de matéria.** Cheque `## Workspaces de matéria` no CLAUDE.md de nível de prática. Se `Habilitado` for `✗` (default para usuários in-house), pule o restante deste parágrafo — skills usam contexto de prática e a maquinaria de matéria fica invisível. Se habilitado e não houver matéria ativa, pergunte: "De qual matéria é isto? Rode `/governanca-ia:matter-workspace switch <slug>` ou diga `nível de prática`." Carregue o `matter.md` da matéria ativa para contexto e overrides. Escreva outputs na pasta da matéria em `~/.claude/plugins/config/claude-for-legal/governanca-ia/matters/<matter-slug>/`. Nunca leia arquivos de outra matéria a menos que `Cross-matter context` esteja `on`.

---

## Propósito

Uma AIA é uma decisão documentada, não um formulário. Ela responde: o que esse
sistema de IA faz, como chega a seus outputs, quem é afetado(a) se estiver
errado, qual a supervisão, e está ok implantar. Esta skill estrutura essa
conversa e escreve o output no formato deste time — o aprendido da AIA-semente
durante o cold-start.

Uma AIA não é o mesmo que um RIPD/DPIA. O RIPD (LGPD art. 38) pergunta se o
tratamento de dados pessoais é lícito e proporcional. A AIA pergunta se o
sistema de IA foi desenhado e implantado de forma responsável. Frequentemente
precisam acontecer em paralelo; não são substitutos.

## Carregue a house style

Leia `~/.claude/plugins/config/claude-for-legal/governanca-ia/CLAUDE.md` → `## House style de avaliação de impacto`. Tem:
- O que dispara uma avaliação de impacto nesta empresa
- O template extraído da AIA-semente
- Profundidade típica
- Quem aprova

Se a estrutura-semente estiver no CLAUDE.md, **use-a**. O ponto é que esta
avaliação se pareça com as outras avaliações que este time produz.

**Escopo jurisdicional.** Esta AIA aplica os regimes regulatórios listados em `## Footprint regulatório` do CLAUDE.md. Regras jurídicas de IA, classificações de risco e obrigações de implantação variam materialmente por jurisdição e estão se movendo rápido. Se este sistema for (ou for) implantado fora desse footprint, ou se houver questão de lei aplicável, a análise pode não valer conforme escrita — rode de novo ou expanda o footprint.

---

## Passo 0: A AIA é necessária?

Cheque os critérios de gatilho no CLAUDE.md.

**Cheque também estes, independentemente:**
- A IA toma ou influencia materialmente decisão que afeta uma pessoa (emprego, crédito, acesso, preço, moderação de conteúdo)? → LGPD art. 20 (direito à revisão).
- Há tratamento de dados pessoais sobre indivíduos? → LGPD; possivelmente RIPD (art. 38) e bases legais (arts. 7º e 11).
- É um sistema de IA voltado para cliente/consumidor? → CDC (arts. 6º III, 12, 14, 39).
- A empresa é aplicadora (deployer) de modelo de terceiros?
- O caso de uso está no tier elevado ou alto, conforme CLAUDE.md?
- Tem alcance em judiciário (Resolução CNJ 332/2020 e 615/2025), eleitoral (Resoluções TSE), saúde (Provimento CFM 2.314/2022), financeiro (deliberações BCB/CVM), trabalho (CLT + jurisprudência TST sobre monitoramento), infantojuvenil (Lei 14.811/2024) ou biometria?

Se nada disso e o gatilho da casa não foi atingido:
> "Não parece exigir uma AIA completa. Aqui um parágrafo para arquivo explicando
> por quê — caso alguém pergunte depois."

---

## Passo 1: Trilho de risco

Antes do intake, determine qual trilho rodar. As definições de tier e os
critérios de fast-track vêm do CLAUDE.md (`## Registro de casos de uso` e
`## Tiers de governança`), não de framework regime-específico hardcoded.

Pesquise o framework de classificação de risco aplicável para cada regime no
footprint regulatório do(a) usuário(a). Muitos regimes distinguem por tier de
risco, população afetada e consequencialidade da decisão — pesquise os critérios
específicos. Observe que a maioria dos regimes trata dado de empregado(a) como
dado pessoal e monitoramento de empregado(a) como consequencial; não assuma que
sistema interno está fora do escopo.

> **Sem suplementação silenciosa.** Se uma consulta de pesquisa à ferramenta
> configurada (JusBrasil, repositórios STJ/STF/CNJ, sites de
> ANPD/BCB/CVM/CNJ/TSE, EUR-Lex para EU AI Act, ou MCP de legislação) retornar
> poucos ou nenhum resultado para os tiers de risco ou gatilhos de um regime,
> reporte o encontrado e pare. NÃO preencha o vão a partir de busca web ou
> conhecimento do modelo sem perguntar. Diga: "A busca retornou [N] resultados
> de [ferramenta]. Cobertura parece fraca para [regime / tópico]. Opções: (1)
> ampliar a query, (2) tentar outra ferramenta, (3) buscar na web — resultados
> serão marcados `[busca web — verificar]` e devem ser checados contra a
> autoridade emissora antes de confiar, ou (4) sinalizar como não verificado e
> parar. Qual prefere?" O(A) advogado(a) decide se aceita fontes de confiança
> mais baixa.
>
> **Tier de atribuição de fonte.** Marque cada citação na AIA — texto
> regulatório, regulamentação infralegal, atos administrativos, normas técnicas
> — com sua fonte. Para citações de conhecimento do modelo, use um dos três tiers:
>
> - `[estabelecido]` — referências estáveis e bem conhecidas (p.ex., LGPD art. 20 como conceito, a existência do PL 2338/2023, GDPR Art. 22 conceitualmente, a existência do Regulamento (UE) 2024/1689). Ainda verifique antes de certificar, mas prioridade menor.
> - `[verificar]` — citações de conhecimento do modelo que são reais mas devem ser verificadas: Resoluções específicas CD/ANPD, Provimentos do CNJ, atos do TSE, Provimentos CFM, atos delegados/implementadores do EU AI Act, datas de eficácia, e qualquer item pós-2023.
> - `[verificar-pinpoint]` — citações pinpoint (números específicos de artigo do PL 2338, anexos, alíneas, números de Resolução, dispositivos de Resolução CNJ ou TSE, subseções do EU AI Act) carregam o maior risco de fabricação e devem SEMPRE ser verificadas contra fonte primária. Numeração do PL 2338 muda a cada substitutivo; numeração do EU AI Act mudou na consolidação; toda cite pinpoint deve ser verificada contra o texto oficial.
>
> Citações recuperadas por ferramenta mantêm a tag de fonte (`[JusBrasil]`, `[STJ]`, `[STF]`, `[CNJ]`, `[ANPD]`, `[EUR-Lex]`, `[site oficial]`, ou nome do MCP); citações de busca web permanecem `[busca web — verificar]`; citações fornecidas pelo(a) usuário(a) permanecem `[usuário forneceu]`. O tiering deixa visível o real trabalho de verificação. Nunca apague ou colapse as tags.
>
> **Para usuários não-advogados, datas incertas vão numa lista de confirmação, não inline.** Uma tag `[verificar]` em "vigência em 1º de fevereiro de 2026" lê como "vigência em 1º de fevereiro de 2026" para uma pessoa de TI que não sabe o que `[verificar]` quer dizer. Leia `## Quem está usando` no CLAUDE.md. Se o papel for **Não-advogado(a)** e uma data, prazo, limiar ou afirmação de vigência for incerta (carregaria `[verificar]` ou `[verificar-pinpoint]` inline), substitua a afirmação inline por "data de vigência: confirmar com advogado(a)" (ou "limiar: confirmar com advogado(a)") e reúna todas as afirmações incertas numa seção final da AIA intitulada:
>
> > **Coisas em que não estou certo — peça ao(à) seu(sua) advogado(a) para confirmar antes de confiar nisso:**
>
> Liste cada item incerto com (1) o que eu disse, (2) sobre o que estou incerto(a), (3) por que importa para a avaliação. Isso evita que um(a) leitor(a) não-advogado(a) confunda um palpite sinalizado com um fato checado. Papéis de advogado(a) recebem o tratamento `[verificar]` inline — sabem o que a tag significa.

**Fast track vs. avaliação completa:** o CLAUDE.md define o que se qualifica para tratamento abreviado. Se não definir critérios de fast-track, default para avaliação completa e pergunte ao(à) usuário(a) quais critérios capturar para a próxima.

Em dúvida, rode a avaliação completa. Um fast-track que se revele errado é pior que uma avaliação detalhada de algo de baixo risco.

---

## Passo 2: Intake

Antes de escrever qualquer coisa, obtenha respostas para isso. Conversacional está bom — não é formulário para enviar.

### O sistema

- O que a IA faz? Descreva em português claro, não copy de marketing.
- Qual modelo ou fornecedor está por trás? Fine-tuned ou pronto?
- Onde se encaixa no workflow — é assistivo (humano(a) revisa output), aumentativo (humano(a) pode sobrepor mas geralmente não sobrepõe), ou automatizado (sem humano(a) no loop)?
- Qual o output — texto gerado, score, classificação, recomendação, ação?

### Quem é afetado(a)

- Sobre quem o output da IA age — empregados(as), clientes, terceiros?
- Se a IA produz erro (falso positivo, falso negativo, alucinação), quem suporta o dano e qual o pior caso realista?
- Há grupos vulneráveis desproporcionalmente no escopo — crianças e adolescentes (ECA, Lei 14.811/2024), candidatos(as) a emprego, pessoas em vulnerabilidade financeira, pacientes?

### Inputs e dados

- Que dados a IA recebe?
- Inclui dados pessoais? De quem? (Aciona LGPD; se sensíveis, art. 11; se de criança/adolescente, art. 14.)
- O modelo foi treinado em dados da empresa, ou é foundation model sem treinamento empresa-específico?
- Para onde vão os dados de entrada — saem do perímetro para API de terceiro? (Avaliar transferência internacional — LGPD arts. 33-36, Resolução CD/ANPD 19/2024.)

### Decisões e supervisão

- O output da IA dispara ação automaticamente, ou um(a) humano(a) decide o que fazer com o output?
- Se há revisão humana: com que frequência o(a) humano(a) muda o output da IA? (Se a resposta é "raramente" — o(a) humano(a) não revisa de verdade; está carimbando.)
- Há processo de revisão (LGPD art. 20) ou correção para afetados(as)?
- Quem é responsável pelos outputs do sistema — há dono(a) nomeado(a)?

### Acurácia e falha

- Qual a taxa de erro conhecida ou estimada? Que testes foram feitos?
- O que acontece quando a IA erra — o erro é visível, logado, corrigido?
- Houve teste de viés? Contra quais grupos? (CF art. 5º; Lei 7.716; Lei 12.288; LGPD art. 6º IX — não-discriminação.)

### Estágio e escala

Pergunte:
- **Estágio:** "O sistema é (a) proposto e ainda não construído, (b) em piloto, (c) em produção, ou (d) em produção e escalado?"
- **Escala:** "Quantos indivíduos são afetados por [mês/ano]? Há quanto tempo está rodando?"
- **Histórico:** "Foi avaliado antes? Produziu decisões que foram contestadas, recorridas (revisão LGPD art. 20) ou revertidas?"

Estágio muda a avaliação: sistema proposto recebe revisão de design. Piloto recebe revisão de design + gate "antes de escalar". Sistema em produção recebe verificação retrospectiva de impacto (causou dano?) E revisão prospectiva. Sistema escalado recebe tudo isso mais plano de remediação se issues forem encontradas, porque não dá para simplesmente desligar.

---

## Passo 3: Classificação regulatória

**Passo 3 pré-check — frescor do footprint.** Antes de iterar sobre o `## Footprint regulatório` capturado, compare a população afetada e o tipo de decisão (do Passo 2) contra o footprint escrito. O footprint foi setado no cold-start, baseado na postura operacional daquele momento. Se o caso de uso introduz população afetada (p.ex., crianças, empregados(as) em estado novo, titulares na UE) ou tipo de decisão (p.ex., contratação, crédito, diagnóstico de saúde, segurança pública, infraestrutura crítica) que o footprint não contempla, **re-derive os regimes aplicáveis em vez de iterar sobre a lista velha.**

Diga ao(à) usuário(a):

> "O footprint regulatório do perfil de prática foi setado para [populações afetadas / tipos de decisão capturados no cold-start]. Este caso de uso afeta **[população ou tipo de decisão novo — p.ex., empregados(as) no setor financeiro, menores, decisões de crédito, identificação biométrica]**, que não está no footprint capturado. Vou re-derivar os regimes aplicáveis a partir das jurisdições operacionais ([lista do `## Perfil da empresa`]) e do tipo de decisão deste caso ([Y]), em vez de usar o footprint defasado. Se este caso é representativo de trabalho que você espera ver mais, atualize `## Footprint regulatório` ao fim do run para que a próxima AIA não precise re-derivar."

Modo de falha comum: o footprint lista LGPD + Marco Civil + CDC, e o caso de uso é sistema de contratação implantado em empresa do setor financeiro. O footprint não tem entrada para BCB ou TST sobre monitoramento; iterar silenciosamente perde deliberação BCB sobre IA em serviços financeiros e jurisprudência trabalhista de monitoramento. Re-derive.

Segundo modo de falha: o footprint foi setado antes de regime que agora importa (p.ex., antes de Resolução CNJ 615/2025; antes da nova versão do PL 2338; antes de Lei 14.811/2024). Se a re-derivação trouxer regime fora do footprint, sinalize no output e recomende atualizar o footprint.

Para cada regime em `~/.claude/plugins/config/claude-for-legal/governanca-ia/CLAUDE.md` → `## Footprint regulatório` que se aplique a este sistema — **mais qualquer regime trazido pela re-derivação acima** — pesquise o framework de classificação de risco vigente e determine onde o sistema cai.

Tarefas de pesquisa:
- Qual a taxonomia de tiers do regime (p.ex., proibido / alto-risco / risco-limitado / risco-mínimo, ou equivalente)? Para BR: o PL 2338/2023 traz categorias de risco excessivo e alto risco — texto `[em tramitação — verificar]`. Para LGPD: tratamento que pode gerar alto risco aos titulares aciona RIPD (art. 38) e medidas adicionais (Resolução CD/ANPD 4/2023 sobre encarregado(a) e segurança).
- Quais os critérios para cada tier? Cite fontes primárias com pinpoints.
- Em que tier este sistema cai dada função, partes afetadas e consequencialidade?
- Há práticas vedadas que o sistema possa tocar? Trate qualquer possível match como crítico — sinalize imediatamente. Práticas vedadas a observar: identificação biométrica em massa (CF art. 5º X — privacidade; LGPD art. 11 — sensíveis; ECA quanto a menores); social scoring de cidadãos por ente público; exploração de vulnerabilidades (CDC art. 39 IV); deepfake eleitoral (Resolução TSE em ano eleitoral); CSAM (Lei 14.811/2024).
- Há obrigações de transparência que se apliquem independentemente do tier? (LGPD arts. 6º VI, 9º, 18 — informação ao titular; LGPD art. 20 — direito à revisão e informação sobre decisão automatizada; CDC arts. 6º III e 31 — informação ao consumidor; Resolução CNJ 332/2020 art. 7º — transparência em IA do Judiciário; rotulagem de conteúdo sintético por TSE em ano eleitoral.)
- Se a empresa é desenvolvedora (provedora) de modelo de uso geral ou foundation, que obrigações de provedor se aplicam (documentação técnica, transparência de dados de treinamento, conformidade autoral — Lei 9.610 e debate do PL 2338, testes de risco sistêmico)?
- **Algum regime no footprint exige avaliação separada de direitos fundamentais ou impacto algorítmico?** O PL 2338/2023 prevê avaliação de impacto algorítmico como instrumento próprio `[verificar contra texto atual]`. A LGPD prevê RIPD (art. 38) — distinto de AIA. O EU AI Act Art. 27 exige FRIA para certos aplicadores de alto risco. Cheque cada regime para equivalente. Se exigido, sinalize como entregável separado nas recomendações — não trate esta AIA como substituta.

Não assuma que sistemas só internos estão fora do escopo — a maioria dos regimes trata dado de empregado(a) como dado pessoal e monitoramento de empregado(a) como consequencial. Verifique a regra específica (CLT + súmulas TST; LGPD; Resoluções CD/ANPD).

**Split provedor-vs-aplicador (quando `Papel em IA: Ambos`).** Se `## Perfil da empresa` → `Papel em IA` for `Ambos` (empresa é desenvolvedora/provedora E aplicadora), a Seção 6 DEVE incluir tabela de mapeamento provedor-vs-aplicador por regime. A maioria dos regimes impõe obrigações materialmente diferentes a provedor (ou desenvolvedor) versus aplicador (ou usuário) — colapsar numa lista indiferenciada perde obrigações e confunde riscos. Não combine. Produza, por regime:

| Obrigação | Como provedor(a) | Como aplicador(a) |
|---|---|---|
| [obrigação específica, cite pinpoint] | [o que se aplica / não se aplica / com quais ressalvas] | [o que se aplica / não se aplica / com quais ressalvas] |

**Se classificação de alto risco ou equivalente se aplicar:**
Sinalize na avaliação, citando o dispositivo específico e o regime. Note que esta AIA documenta a revisão interna mas não substitui qualquer avaliação formal de conformidade que o regime exija. Recomende revisão jurídica externa antes de implantar na jurisdição afetada.

Capture a classificação e a autoridade citada no output da avaliação.

---

## Passo 4: Escreva a avaliação

**Use a estrutura-semente do CLAUDE.md.** Se nenhuma foi capturada, use este default:

```markdown
[CABEÇALHO DE MATERIAL DE TRABALHO — por config do plugin ## Outputs — difere por papel; veja `## Quem está usando`]

# Avaliação de Impacto de IA (AIA): [Nome do Sistema/Feature]

**Preparado(a) por:** [nome] | **Data:** [data] | **Status:** RASCUNHO / APROVADA
**Dono(a) do sistema:** [nome] | **Revisor(a) de governança de IA:** [nome]
**Tier de governança:** [Padrão / Elevado / Alto]
**Trilho:** [Fast track / Avaliação completa]

---

## Resumo executivo

[Duas frases: o que esta IA faz e se está ok implantar. P.ex., "Este sistema
usa LLM de terceiro para rascunhar respostas iniciais a tickets de suporte
antes de revisão por agente humano(a). O tratamento é consistente com a
política de IA da empresa; três condições antes de produção."]

**Risco geral:** 🟢 Baixo / 🟡 Médio / 🟠 Alto / 🔴 Muito alto

---

## 1. Descrição do sistema

**O que faz:** [português claro — não marketing]
**Modelo / fornecedor:** [quem provê a IA]
**Modo de implantação:** [Assistivo / Aumentativo / Automatizado]
**Tipo de output:** [texto / score / classificação / recomendação / ação]
**Status:** [Não iniciado / Piloto / Produção]

---

## 2. Partes afetadas

**Sobre quem age:** [empregados(as) / clientes / terceiros]
**Escala:** [quantas pessoas, com que frequência]
**Dano se errar:** [pior caso realista — específico, não genérico]
**Grupos vulneráveis no escopo:** [sim — [quem] / não]

---

## 3. Inputs de dados

**Categorias de dados usados:** [campos específicos, não "dados do usuário"]
**Dados pessoais:** [sim — [de quem] / não] (LGPD aplicável? Sensíveis — art. 11?)
**Dados saem do perímetro?** [sim — para [fornecedor] / não] (Transferência internacional — LGPD arts. 33-36, Resolução CD/ANPD 19/2024?)
**Treinamento do modelo:** [dados da empresa usados / foundation model / fine-tuned em [dataset]] (Conformidade autoral — Lei 9.610? Debate PL 2338 sobre mineração de dados.)

---

## 4. Tomada de decisão e supervisão

**Humano(a) no loop:** [Sempre / Nominal (risco de carimbo) / Não]
**Mecanismo de override:** [como humano(a) pode intervir ou corrigir]
**Revisão / correção para afetados(as):** [sim — [como] / não] (LGPD art. 20 — direito à revisão de decisão automatizada.)
**Dono(a) nomeado(a):** [nome ou papel] (Encarregado(a)/DPO envolvido(a)? — LGPD art. 41.)

---

## 5. Acurácia e viés

**Taxa de erro:** [conhecida / estimada / não testada]
**Modo de falha:** [o que acontece quando erra — visível? logado? corrigido?]
**Teste de viés:** [feito — [resultados] / não feito / não aplicável] (CF art. 5º; Lei 7.716; LGPD art. 6º IX — não-discriminação; CDC art. 39 IV — exploração de vulnerabilidade.)

---

## 6. Classificação regulatória

*[Uma subseção por regime do footprint que se aplique a este sistema.]*

**Regime:** [nome — LGPD / PL 2338/2023 / Resolução CNJ 332/2020 / Resolução TSE / Provimento CFM / EU AI Act etc.]
**Classificação sob este regime:** [tier, com pinpoint ao dispositivo controlador]
**Práticas vedadas acionadas:** [nenhuma / [dispositivo específico e porquê]]
**Obrigações aplicáveis:** [lista pesquisada com citações — transparência, documentação, supervisão humana, testes, registro, etc.]
**Avaliação separada (RIPD/FRIA/relatório de impacto algorítmico) exigida?** [Sim — p.ex., LGPD art. 38 (RIPD) se houver alto risco aos titulares; PL 2338/2023 — avaliação de impacto algorítmico `[em tramitação — verificar]`; EU AI Act Art. 27 (FRIA) se nexo na UE / regime equivalente / Não / N/A. Se sim, é entregável separado, não subsumido nesta AIA.]
**Data de vigência / fiscalização:** [data(s)]
**Ambiguidade ou interpretação em aberto:** [sinalize qualquer coisa não consolidada]

**Split provedor-vs-aplicador (obrigatório se `Papel em IA: Ambos`):**

| Obrigação | Como provedor(a) | Como aplicador(a) |
|---|---|---|
| [obrigação específica + pinpoint] | [o que se aplica / não se aplica] | [o que se aplica / não se aplica] |

---

## 7. Consistência com a política de IA

| Compromisso de política | Consistente? | Notas |
|---|---|---|
| [compromisso do `## Compromissos de política de IA` do CLAUDE.md] | 🟢 / 🟡 / 🟠 / 🔴 | |

[Se qualquer item for 🟡 ou pior: atualização de política necessária antes de implantar, ou design precisa mudar. Um deles tem que mudar — não os dois sinalizados e deixados em aberto.]

---

## 8. Riscos e mitigações

| # | Risco | Probabilidade | Impacto | Mitigação | Status | Dono(a) |
|---|---|---|---|---|---|---|
| 1 | [risco específico ligado a este desenho — não "alucinação de IA" genericamente] | B/M/A | B/M/A | [controle específico] | Feito / Planejado / Gap | [nome] |

**Risco residual após mitigações:** [avaliação]

---

## 9. Recomendação

**[APROVADA / APROVADA COM CONDIÇÕES / MUDANÇAS EXIGIDAS / NÃO APROVADA]**

**Condições (se houver):**
- [ ] [ação específica antes da implantação — dono(a), prazo]

**Revisão de privacidade exigida?** [Sim — rode `/privacidade:pia-generation` (RIPD), se o plugin estiver instalado / Não]

**Aprovação:** [nome, data]

---

## Cite-check

Citações regulatórias na Seção 6 (e em qualquer outro lugar) foram geradas por modelo de IA e não foram verificadas contra fontes primárias. Antes de a avaliação ser certificada ou utilizada, rode passagem de verificação contra ferramenta de pesquisa (JusBrasil, repositório de STJ/STF/CNJ, sites de ANPD/CNJ/TSE/BCB/CVM, planalto.gov.br, in.gov.br, EUR-Lex para EU AI Act, ou plataforma da banca) para cada dispositivo citado — confirme pinpoint, vigência, e qualquer ato regulamentar derivado. O cenário regulatório de IA muda rápido; verifique antes de orientar. Tags de fonte em cada citação (p.ex., `[ANPD]`, `[busca web — verificar]`) mostram de onde veio; tags `verificar` carregam maior risco de fabricação e devem ser checadas primeiro.
```

**Antes de certificar a AIA (passo Sign-off, marcando Status: APROVADA):** Leia `## Quem está usando` no CLAUDE.md. Se o papel for Não-advogado(a):

> Certificar esta AIA tem consequências jurídicas — torna-se o registro em que a
> empresa se apoia se um(a) regulador(a) (ANPD, MP, CNJ, TSE, BCB, CVM, Procon)
> ou afetado(a) pedir como o caso foi avaliado. Você revisou com advogado(a)?
> Se sim, prossiga. Se não, segue um brief para levar:
>
> [Gere resumo de 1 página: o sistema, a classificação regulatória, os riscos
> identificados, as mitigações, risco residual, perguntas em aberto, o que
> perguntar ao(à) advogado(a) antes de certificar.]
>
> Se precisa encontrar advogado(a): a OAB seccional da sua jurisdição tem
> serviço de orientação; defensorias e clínicas universitárias atendem casos
> que se enquadrem.

Não passe deste gate sem um sim explícito. Avaliações em RASCUNHO para revisão por advogado(a) não exigem o gate — a certificação exige.

---

## Padrões de qualidade de risco

Mesmo padrão da skill de RIPD/PIA — riscos devem ser **específicos e ligados ao desenho**.

| Risco ruim | Por que ruim | Melhor |
|---|---|---|
| "Alucinação de IA" | Vale para todo LLM; não diz nada | "Modelo pode gerar citações jurídicas plausíveis mas incorretas — agentes de suporte não têm passo de verificação atual antes de enviar a cliente" |
| "Viés" | Vago demais | "Modelo de pontuação de currículo treinado em contratações históricas; se cohort histórico foi demograficamente homogêneo, candidatos(as) sub-representados podem ser sistematicamente pontuados(as) abaixo (CF art. 5º; Lei 7.716; LGPD art. 6º IX)" |
| "Risco de fornecedor" | Circular | "Termos da OpenAI permitem treinamento sobre inputs da API por default; sem opt-out confirmado no contrato, mensagens de suporte podem ser usadas para treinar o modelo (LGPD — base legal; Resolução CD/ANPD 19/2024 — transferência internacional)" |

Mire em 2-5 riscos reais, não 12 enchidos.

---

## Diff de política de IA

Toda avaliação deve cruzar contra os compromissos de IA do CLAUDE.md. Drift comum:

- Política proíbe uso de IA em [categoria] — este caso é essa categoria. Pare.
- Política exige revisão humana — esta implantação não tem passo humano. Design precisa mudar.
- Política exige divulgação a afetados(as) — mecanismo de divulgação não foi construído.
- Lista de fornecedores aprovados existe — este fornecedor não está. Procurement step obrigatório.

Sinalize cada mismatch. Um deles tem que mudar antes da implantação.

---

## Handoffs

- **Para produto / engenharia:** Lista de condições com donos(as) e prazos. Não "adicionar supervisão" — "adicionar passo de revisão humana antes de qualquer e-mail automatizado, dono(a): [product lead], antes do lançamento."
- **Para privacidade:** Se há dados pessoais, sinalize: "Rode `/privacidade:pia-generation [nome do sistema]` em paralelo (RIPD — LGPD art. 38), se o plugin estiver instalado — a AIA não substitui o RIPD."
- **Para vendor-ai-review:** Se há fornecedor novo, sinalize: "Se não há aditivo de IA revisado para [fornecedor], rode `/governanca-ia:vendor-ai-review` antes da produção."
- **Para reg-gap-analysis:** Se novas obrigações regulatórias emergiram (PL 2338 aprovado, nova Resolução ANPD/CNJ, ato setorial), a skill rastreia o gap.

---

## Feche com a árvore de decisão de próximos passos

Feche com a árvore de decisão por CLAUDE.md `## Outputs`. Customize as opções ao que esta skill acabou de produzir — os cinco branches default (esboçar X, escalonar, levantar mais fatos, observar e aguardar, outra coisa) são ponto de partida, não trava. A árvore é o output; o(a) advogado(a) escolhe.

## O que esta skill NÃO faz

- Não aprova a implantação. Um(a) humano(a) assina a avaliação.
- Não constitui qualquer avaliação formal de conformidade regulatória — onde um regime (p.ex., PL 2338/2023 quando aprovado, EU AI Act) exija avaliação formal, é exercício separado que requer revisão jurídica externa e documentação técnica além do que está aqui.
- Não desenha as mitigações. Descreve o que precisa ser mitigado; engenharia desenha o conserto.
- Não substitui RIPD quando há dados pessoais. Rode os dois.
