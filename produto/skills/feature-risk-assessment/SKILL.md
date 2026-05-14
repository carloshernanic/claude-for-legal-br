---
name: feature-risk-assessment
description: >
  Risk assessment mais profundo de uma única feature ou área de produto quando
  a launch review encontrou algo que precisa de mais que uma linha. Análise
  estruturada: o que pode dar errado, qual a probabilidade, qual a gravidade,
  o que mitiga. Use quando o usuário disser "deep dive nesse risco", "risk
  assessment de [feature]", "o que pode dar errado com" ou quando launch-review
  sinaliza issue inédita.
---

# Feature Risk Assessment

> **Adaptação BR.** Calibrado para o regime brasileiro: LGPD, Marco Civil, CDC, ECA, LBI, regulação setorial (BCB, CVM, ANVISA, ANATEL), CONAR. Veja `references/terminology-us-to-br.md`.

## Matter context

**Matter context.** Cheque `## Matter workspaces` no CLAUDE.md de practice-level. Se `Enabled` é `✗` (default para in-house), pule o resto deste parágrafo — skills usam contexto de practice-level e o esquema de matter fica invisível. Se habilitado e não há matter ativa, pergunte: "Para qual matéria é? Rode `/produto:matter-workspace switch <slug>` ou diga `practice-level`." Carregue o `matter.md` da matter ativa para contexto e overrides. Escreva outputs na pasta da matter em `~/.claude/plugins/config/claude-for-legal/produto/matters/<matter-slug>/`. Nunca leia arquivos de outra matter salvo se `Cross-matter context` está `on`.

---

## Propósito

A launch review é ampla. Isto é profundo. Quando uma única issue precisa de mais do que uma linha de tabela — uma feature de IA inédita, um produto infanto-juvenil (ECA + LGPD art. 14), algo que um regulador está olhando ativamente (ANPD, BCB, CVM, ANVISA) — esta skill produz uma análise standalone.

Nem todo lançamento precisa. A maioria não precisa. Isto é para os 10% em que "RIPD feito, lançou" não é o nível certo de escrutínio.

## Quando rodar

- Launch review encontrou um padrão que **não está na tabela de calibração** (inédito)
- Launch review encontrou algo na categoria **"costuma bloquear"**
- GC ou liderança perguntou "qual o risco aqui" e quer mais do que uma linha
- A feature está em área de **atenção regulatória ativa** (IA — PL 2338/2023 e ANPD; infanto-juvenil — ECA + CONANDA Res. 163/2014 + LGPD art. 14; biométricos — LGPD art. 11; saúde — ANVISA; conteúdo gerado por usuário — Marco Civil arts. 19 e 21, RE 1.037.396 STF pendente)
- Alguém fora do jurídico está preocupado e uma resposta estruturada ajudaria

Se nenhum dos acima, a launch review basta. Não gere papelada por gerar.

## Estrutura

### 1. O que estamos avaliando

Um parágrafo. O que a feature faz, o que tem de novo, por que escalou para uma assessment completa.

### 2. Os riscos

Para cada risco distinto (mire em 2-5, não 15):

```markdown
### Risco [N]: [Nome curto]

**Cenário:** [O que teria de acontecer para dar errado. Seja específico —
não "vazamento de dados" mas "o algoritmo de recomendação expõe interesse
sensível de um usuário (LGPD art. 5º II) a alguém que não deveria ver
porque X."]

**Quem é prejudicado:** [Usuários? A empresa? Terceiro? Específico — titular
de dados, consumidor (CDC), criança/adolescente (ECA), pessoa com deficiência
(LBI), trabalhador, contraparte contratual.]

**Probabilidade:** [Baixa / Média / Alta — com razão. "Baixa — exigiria que
X e Y falhassem simultaneamente." Não só rating de vibe.]

**Gravidade se ocorrer:** [Baixa / Média / Alta — com razão. "Alta —
sanção ANPD (LGPD art. 52: advertência, multa simples até 2% do faturamento
limitada a R$ 50 mi por infração, multa diária, publicização, bloqueio,
eliminação dos dados) + ação coletiva CDC + exposição em imprensa" vs.
"Baixa — um tweet irritado, sem dano efetivo." Considere também: dano
moral coletivo (CDC art. 6º VI; Lei 7.347/85 — Ação Civil Pública), TAC
com SENACON/Procon, exposição BCB/CVM/ANVISA conforme o setor.]

**Mitigações existentes:** [O que já reduz probabilidade ou impacto]

**Lacuna:** [O que está faltando, se algo]

**Risco residual:** [Após mitigações existentes — é aceitável ou precisa
de mais?]
```

### 3. Cenário regulatório (se relevante)

Inclua só se um regulador está ativamente interessado neste espaço. Se sim:

- Qual regulador (ANPD, BCB, CVM, ANVISA, ANATEL, SENACON, CADE, IBAMA, etc.), o que disseram/fizeram recentemente
- Como esta feature pareceria para eles
- Se preferimos que saibam por nós ou por uma manchete

### 4. Precedente (se houver)

Outra empresa fez algo similar? O que aconteceu?

- Se nada de ruim aconteceu → útil, não dispositivo
- Se algo ruim aconteceu → o que era diferente naquela situação, aplica-se aqui

Não superestime precedente. Reguladores mudam prioridades; uma empresa escapando de algo não significa que a próxima vai. **Atenção para o sistema BR**: precedente regulatório (TAC, processo administrativo da ANPD, decisão CADE) tem peso diferente de precedente judicial; STJ e STF têm efeito uniformizador (CPC arts. 926–928, súmulas vinculantes, Temas de Repercussão Geral / Temas Repetitivos), mas TJ/TRT são não-vinculantes salvo IRDR/IAC.

### 5. Opções

Apresente 2-3 caminhos realistas:

```markdown
| Opção | Descrição | Redução de risco | Custo |
|---|---|---|---|
| A: Lançar como projetado | [plano atual] | Nenhuma | Nenhum |
| B: Lançar com [mitigação] | [mudança] | [quanto] | [esforço de eng, timeline, UX] |
| C: Não lançar [componente] | [scope cut] | [quanto] | [impacto no produto] |
```

### 6. Recomendação

Escolha uma. Explique por quê. Reconheça o que está trocando.

```markdown
**Recomendado: Opção [X]**

[Por quê. Que risco permanece. Por que é aceitável. Quem aceita.]

**Se a resposta é "não é minha decisão":** [Quem decide, o que precisa saber]
```

## Check de calibração

Antes de finalizar, cheque contra `~/.claude/plugins/config/claude-for-legal/produto/CLAUDE.md` → Calibração de risco:

- Este risk assessment está calibrado para *esta empresa*, ou é genérico?
- Um risco que é "Alto" numa empresa sob TAC com SENACON ou sob acordo com ANPD pode ser "Médio" numa que não está
- A análise deve refletir a postura regulatória real, histórico de litígio (ações coletivas via CDC arts. 81–104; ACPs da Lei 7.347/85), e apetite de risco capturados no practice profile

## Handoffs

- **Para AI governance:** Se o deep-dive foi disparado por feature de IA — o
  que é frequente — rode `/governanca-ia:aia-generation [feature]` em
  paralelo ou imediatamente após. O feature risk assessment enquadra a decisão;
  o AIA documenta o sistema de IA no formato que governança de IA precisa.
  Não são duplicatas: o FRA é doc de decisão de produto; o AIA é registro
  de governança (referência: PL 2338/2023 + LGPD art. 20).
- **Para privacy:** Se a feature envolve nova coleta ou tratamento de dados,
  rode `/privacidade:ripd-generation [feature]` (RIPD — LGPD art. 38). A
  seção de risco do FRA provavelmente vai sobrepor com o RIPD — sinalize a
  sobreposição para não duplicar trabalho, mas ambos devem existir.
- **Para AI governance vendor review:** Se a feature usa novo fornecedor de
  IA, rode `/governanca-ia:vendor-ai-review [contrato]` se ainda não foi
  feito na launch review.

## Formato de output

Doc standalone, 2-4 páginas. Anexe o cabeçalho de material de trabalho de `~/.claude/plugins/config/claude-for-legal/produto/CLAUDE.md` `## Outputs` (varia por papel do usuário — veja `## Quem está usando isto`). O cabeçalho invoca sigilo profissional do advogado (EOAB art. 7º, II e XIX; CPC art. 388).

Não é slide deck, não é memorando de arquivo — é um documento de decisão que alguém lê e então decide.

Salve onde `~/.claude/plugins/config/claude-for-legal/produto/CLAUDE.md` → processo de launch review diz que ficam os docs. Se o doc vai ser compartilhado com alguém fora do círculo de sigilo (ex.: postado em ticket amplamente compartilhado), retire o cabeçalho de material de trabalho apenas dessa cópia externa e mantenha o original sigiloso no arquivo da matter.

## Citation check

Se a análise cita acórdãos, leis, regulamentos ou ações de enforcement — especialmente nas seções de Cenário regulatório e Precedente — essas citações foram geradas por um modelo de IA e não foram verificadas contra fonte primária. Antes de o documento ir a um(a) decisor(a), verifique cada citação contra ferramenta de pesquisa jurídica (JusBrasil, Escavador, LexML, Planalto, base do STF/STJ/TST, ou a plataforma do escritório) para precisão, vigência (lei revogada? acórdão superado por súmula vinculante posterior?), e postura atual de enforcement. Um risk assessment construído em ação de enforcement fabricada é pior do que assessment nenhum.

> **Sem complementação silenciosa.** Se uma query à ferramenta de pesquisa configurada retorna poucos ou nenhum resultado para o regime ou precedente que a assessment precisa, reporte o que foi encontrado e pare. NÃO preencha a lacuna com busca web ou conhecimento de modelo sem perguntar. Diga: "A busca retornou [N] resultados de [ferramenta]. Cobertura parece fina para [regime / precedente]. Opções: (1) ampliar a query, (2) tentar outra ferramenta, (3) buscar na web — resultados serão marcados `[busca web — verificar]` e devem ser checados contra a autoridade emissora antes de confiar, ou (4) sinalizar como não verificado e parar. Qual você prefere?" Um(a) advogado(a) decide se aceita fontes de menor confiança.
>
> **Atribuição de fonte.** Marque toda citação nas seções Cenário regulatório e Precedente com sua origem: `[JusBrasil]`, `[Escavador]`, `[LexML]`, `[Planalto]`, `[STF]`, `[STJ]`, `[TST]`, `[site de regulador]`, ou o nome da MCP para citações vindas de conector de pesquisa jurídica; `[busca web — verificar]` para citações de busca web; `[conhecimento de modelo — verificar]` para citações recuperadas de dados de treino; `[usuário forneceu]` para citações vindas da equipe da feature. Citações marcadas `verificar` carregam maior risco de fabricação e devem ser checadas primeiro. Nunca tire ou compacte as tags — o(a) decisor(a) precisa ver quais verificar primeiro.

## Encerre com a árvore de decisão de próximos passos

Encerre com a árvore de decisão de próximos passos conforme CLAUDE.md `## Outputs`. Customize as opções para o que esta skill acabou de produzir — os cinco branches default (redigir o X, escalar, buscar mais fatos, observar e esperar, outra coisa) são ponto de partida, não trava. A árvore É o output; o(a) advogado(a) escolhe.

## O que esta skill NÃO faz

- Não avalia toda feature. A maioria recebe launch review e fica nisso.
- Não toma a decisão. Enquadra a decisão. Alguém com autoridade escolhe.
- Não faz modelagem quantitativa de risco. Se a empresa tem framework formal com números, use — isto é qualitativo.
