---
name: diligence-issue-extraction
description: >
  Lê documentos do VDR e extrai questões conforme categorias da casa e
  limiares de materialidade, produzindo achados no formato de memo da casa.
  Use quando o(a) usuário(a) disser "revisa o data room", "extrai questões
  de [pasta]", "revisão de diligência", "o que tem no VDR" ou apontar para
  documentos do VDR.
argument-hint: "[caminho de pasta VDR ou nome da categoria]"
---

# /diligence-issue-extraction

1. Carregue `~/.claude/plugins/config/claude-for-legal/societario/CLAUDE.md` + `~/.claude/plugins/config/claude-for-legal/societario/deals/[code]/deal-context.md`.
2. Use o workflow abaixo.
3. Cheque `ai-tool-handoff` — se a categoria é em massa e a ferramenta está configurada, repasse primeiro.
4. Leia docs, aplique filtro de materialidade, extraia por categoria.
5. Achados em formato de memo da casa. Repasse consentimentos ao closing-checklist.

---

## Contexto de matéria

**Contexto de matéria.** Cheque `## Workspaces de matéria` no CLAUDE.md nível-prática. Se `Habilitado` é `✗` (padrão para usuários(as) in-house), pule o resto deste parágrafo — skills usam contexto nível-prática e o aparato de matéria fica invisível. Se habilitado e não houver matéria ativa, pergunte: "Para qual matéria é isto? Rode `/societario:matter-workspace switch <slug>` ou diga `nível-prática`." Carregue o `matter.md` da matéria ativa para contexto específico e overrides. Grave saídas na pasta da matéria em `~/.claude/plugins/config/claude-for-legal/societario/matters/<matter-slug>/`. Nunca leia arquivos de outra matéria a menos que `Contexto cross-matter` esteja `on`.

---

## Propósito

O VDR tem 2.000 documentos. Em algum lugar lá estão os 30 que importam para a operação. Esta skill lê documentos contra as categorias de diligência e limiares de materialidade de `~/.claude/plugins/config/claude-for-legal/societario/CLAUDE.md`, extrai questões e escreve em formato de memo da casa.

## Carregar contexto

- `~/.claude/plugins/config/claude-for-legal/societario/CLAUDE.md` → Estrutura de diligência (categorias, limiares de materialidade)
- `~/.claude/plugins/config/claude-for-legal/societario/CLAUDE.md` → Formato do memo de issues (como achados são enunciados)
- `~/.claude/plugins/config/claude-for-legal/societario/deals/[code]/deal-context.md` → limiares específicos da operação, localização do VDR

Se deal-context.md não existir, pergunte para qual operação é.

## Workflow

### Passo 1: Inventariar o VDR

Se MCP de VDR (Box/Intralinks/Datasite) estiver conectado, puxe o índice. Mapeie pastas do VDR para categorias da request list. Anote gaps — categorias da request list sem conteúdo correspondente no VDR.

```markdown
## Inventário do VDR: [Código da operação]

| Categoria de request | Pasta VDR | Docs | Status |
|---|---|---|---|
| Societário e organizacional (atos arquivados na Junta) | /01-Societario | 45 | Revisado |
| Contratos materiais | /02-Contratos | 312 | Em andamento |
| PI (INPI) | /03-PI | 89 | Não iniciado |
| Trabalhista (CLT + e-Social) | /04-Trabalhista | 120 | Não iniciado |
| Contencioso (judicial/CARF/CADE/CVM/ANPD) | /05-Contencioso | 73 | Não iniciado |
| Tributário (RFB/ICMS/ISS) | /06-Tributario | 95 | Não iniciado |
| Ambiental (Lei 6.938/81 — IBAMA + LP/LI/LO) | /07-Ambiental | 22 | Não iniciado |
| Regulatório setorial | /08-Regulatorio | 18 | Não iniciado |
| LGPD (Lei 13.709/2018) | /09-LGPD | 14 | Não iniciado |
| Anticorrupção (Lei 12.846/2013) | /10-Compliance | 8 | Não iniciado |
| [etc.] | | | |

**Gaps:** [Categorias da request list sem conteúdo no VDR — solicitação suplementar necessária]
```

### Passo 2: Aplicar filtro de materialidade

Conforme limiares de `~/.claude/plugins/config/claude-for-legal/societario/CLAUDE.md` / deal-context. Não revise tudo se o limiar diz contratos > R$ X.

Para contratos especificamente: ordene por valor declarado (se em filename/metadata) ou por significância da contraparte. Revise de cima para baixo até atingir o limiar ou esgotar a categoria.

### Passo 3: Extrair questões

Para cada documento lido, cheque contra as preocupações de diligência padrão para sua categoria:

**Contratos materiais — conjunto-padrão de extração:**
- Cláusula de mudança de controle (acionada por esta operação? consentimento exigido?)
- Restrição de cessão (o contrato pode migrar para o(a) comprador(a)? — atenção a tag-along obrigatório S.A. aberta art. 254-A Lei 6.404)
- Exclusividade / não-concorrência (restringe o negócio do(a) comprador(a)?)
- MFN — most favored nation (restrições de preço)
- Direitos de terminação (contraparte pode rescindir por causa da operação?)
- Indenidades incomuns ou exposição de responsabilidade (atenção a CC art. 944; CDC art. 51, I quando aplicável)

**Societário — conjunto-padrão de extração:**
- Acurácia do quadro societário, opções/bônus de subscrição em aberto (Lei 6.404 art. 168), planos de opção (RCVM 86)
- Exigências de aprovação por conselho/assembleia para a operação (Lei 6.404 arts. 121-137; CC arts. 1.071-1.085 para Ltda.)
- Restrições do acordo de acionistas (Lei 6.404 art. 118) — drag-along, tag-along, direito de preferência / primeira oferta
- Estrutura de subsidiárias e arranjos intercompany (atenção a preço de transferência — Lei 14.596/2023)

**PI — conjunto-padrão de extração:**
- Cadeia de titularidade (cessões de fundadores(as)/empregados(as) em vigor? — Lei 9.279/1996 art. 88+ para invenções de empregados; Lei 9.609/1998 art. 4º para software)
- Open source no produto (risco copyleft)
- PI-chave licenciada vs. titularizada
- Contencioso de PI pendente ou ameaçado (consultas a INPI, PJe, JusBrasil)

**Trabalhista — conjunto-padrão de extração:**
- Gatilhos de rescisão com indenização em CoC (custo de "parachute" — atenção a CLT, planos de stock options com debate tributário pendente STJ/RFB)
- Risco de retenção de pessoas-chave
- Contencioso trabalhista pendente (TRT/TST, e-Social, FGTS)
- Risco de classificação (pejotização — terceirizados(as) que se parecem com empregados(as); Súmula 331 TST; risco art. 9º CLT)
- Passivo previdenciário e FGTS

**Contencioso — conjunto-padrão de extração:**
- Matérias pendentes e provisões (consultas a DataJud, PJe, e-SAJ, e-Proc, Projudi; CARF e CVM/ANPD se aplicável)
- Pretensões ameaçadas
- Inquéritos regulatórios (CVM, CADE, ANPD, agências setoriais)
- Litígio em massa (ações coletivas — CDC arts. 81-104; ACP Lei 7.347/85)

**Tributário — conjunto-padrão de extração:**
- Auto de infração e contencioso administrativo (RFB → CARF; ICMS estadual; ISS municipal)
- Parcelamentos em curso e responsabilidade tributária sucessória (CTN arts. 132-133)
- Compliance de obrigações acessórias (ECD, ECF, EFD-Reinf, DCTF, DEFIS)
- Preço de transferência (Lei 14.596/2023) se houver operações cross-border
- Reforma tributária (EC 132/2023 — IBS+CBS+IS): atenção a transição

**Ambiental — conjunto-padrão de extração:**
- Licenças LP/LI/LO em vigor (Lei 6.938/81; Res. CONAMA)
- Termos de Ajustamento de Conduta (TAC) com MP/órgão ambiental
- Áreas contaminadas e responsabilidade do adquirente (CC art. 1.228 §1º; responsabilidade objetiva ambiental)

**LGPD — conjunto-padrão de extração:**
- Mapeamento de tratamentos, base legal (LGPD arts. 7º e 11)
- Encarregado(a) nomeado(a) (LGPD art. 41)
- RIPD (LGPD art. 38) quando exigível
- Incidentes reportados à ANPD (Res. CD/ANPD 15/2024)
- Transferências internacionais (Res. CD/ANPD 19/2024)

**Anticorrupção / Compliance — conjunto-padrão de extração:**
- Programa de integridade (Lei 12.846/2013; Decreto 11.129/2022)
- Acordos de leniência em curso
- Risco de improbidade (Lei 8.429/1992 alterada pela 14.230/2021)
- PLD/CFT (Lei 9.613/1998; Resoluções COAF)

### Passo 4: Enunciar cada achado

> **Atribuição de fonte.** Onde um achado referenciar lei, regulação, decisão ou ação de regulador — ex.: cláusula de CoC analisada sob lei aplicável, gap de titularidade de PI citado contra doutrina específica, contencioso pendente com citação processual — marque a citação com a fonte: `[Jusbrasil]`, `[LexML]`, `[CVM]`, `[CADE]`, `[STJ]`, `[STF]`, `[TST]`, `[CARF]`, `[INPI]`, ou nome da MCP para citações recuperadas de conector de pesquisa; `[busca web — verifique]` para citações de busca web; `[conhecimento de modelo — verifique]` para citações lembradas do treinamento; `[fornecido pelo(a) usuário(a)]` para citações vindas do VDR, memos do time ou feedback do escritório externo. Citações de fonte documental (caminho VDR, filename) mantêm sua referência nativa. Citações marcadas `verifique` carregam risco maior de fabricação e devem ser checadas primeiro. Nunca remova ou colapse as tags.
>
> **Ao discordar de dispositivo citado pelo(a) usuário(a), cite o texto ou recuse caracterizar.** Se o(a) usuário(a) (ou nota de time, ou disclosure da parte vendedora) cita um dispositivo para proposição que você não acredita estar correta, e você não dispõe do texto via conector de pesquisa ou VDR, não invente descrição. Diga: "Esse artigo não bate com o que eu esperaria de um requisito de [sucessão tributária / tag-along / o que for] — eu precisaria puxar o texto efetivo para dizer o que cobre. `[lei não recuperada — verifique]`" Em seguida (a) recupere o texto via ferramenta de pesquisa e cite, (b) peça que o(a) usuário(a) cole o texto, ou (c) sinalize para advogado(a) externo(a). Descrição confiante e errada de uma lei real é pior do que "não sei" — memo do time citando subseção fabricada é mais difícil de desacreditar do que vazio.
>
> **Sem suplemento silencioso.** Se uma consulta à ferramenta de pesquisa configurada retornar poucos ou nenhum resultado para a base legal de que o achado precisa (ex.: regra que rege requisito de consentimento por CoC, doutrina de cessão de PI, teste de classificação de empregado(a)), reporte o que foi encontrado e pare. NÃO preencha o gap com busca web ou conhecimento de modelo sem perguntar. Diga: "A busca retornou [N] resultados de [ferramenta]. Cobertura aparenta ser fina para [regra / doutrina]. Opções: (1) ampliar a busca, (2) tentar outro conector, (3) buscar na web — resultados serão marcados `[busca web — verifique]` e devem ser checados contra fonte primária antes de confiar, ou (4) sinalizar como não verificado e parar. Qual você prefere?" Advogado(a) decide se aceita fontes de menor confiança.

Conforme o template de achado em `~/.claude/plugins/config/claude-for-legal/societario/CLAUDE.md`. Se o memo seed usou isto:

```
Questão #N: [Título]
Categoria: [categoria da request list]
Severidade: [nível conforme escala da casa]
Documentos: [caminho VDR + nome do doc]
Achado: [o que o documento diz e por que importa]
Recomendação: [ajuste de preço / indenidade / consentimento requerido / declaração e garantia / walk-away]
```

...então use exatamente isso. Se o memo seed era em bullets, escreva em bullets.

**Calibração de severidade** (se a escala da casa for V/A/V):
- 🔴 **Vermelho:** Afeta valor ou estrutura da operação. CoC exigindo consentimento de cliente material. Contencioso material não divulgado. Gap de titularidade de PI. Passivo trabalhista/tributário superior ao escrow.
- 🟡 **Amarelo:** Precisa atenção, solucionável. Consentimento requerido mas provavelmente obtível. Open source com remediação. Risco de classificação trabalhista (pejotização) com plano de regularização.
- 🟢 **Verde:** Anotado para arquivo. Consistente com declarações. Sem ação necessária além da declaração e garantia.

### Passo 5: Montar por categoria

Agrupe achados por categoria da request list. Dentro da categoria, ordene por severidade.

```markdown
[CABEÇALHO DE MATERIAL DE TRABALHO — conforme config do plugin ## Outputs — varia por papel; ver `## Quem está usando`]

> Esta saída deriva de materiais do VDR que são privilegiados, confidenciais ou ambos. Herda o sigilo e a confidencialidade da fonte — distribuição fora do círculo de sigilo profissional pode quebrar a proteção. Armazene com os arquivos privilegiados da matéria e tome decisões de distribuição deliberadamente.

# Questões de Diligência: [Código da operação] — [Categoria]

**Documentos revisados:** [N] de [M] na categoria
**Cobertura:** [Todos | > R$ X | Top N]
**Achados:** [N]🔴 [N]🟡 [N]🟢

---

### Bottom line

[🔴 N bloqueantes · 🟠 N altos · 🟡 N médios] — [a única coisa que o time da operação precisa saber]

---

[Cada achado em formato da casa]

---

## Gaps

- [Item da request list sem documento responsivo]
- [Documento referenciado mas não no VDR]
```

## Handoffs

- **Para ai-tool-handoff:** Se Luminance/Kira está em uso conforme `~/.claude/plugins/config/claude-for-legal/societario/CLAUDE.md`, repasse revisão em massa de contratos para lá. Esta skill cuida dos documentos nuançados (side letters, aditivos, qualquer coisa em que a ferramenta IA tropece).
- **Para deal-team-summary:** Achados agregados alimentam o briefing ao time.
- **Para material-contract-schedule:** Extrações em nível de contrato alimentam o anexo de declarações.
- **Para closing-checklist:** Qualquer achado que implique ação discreta pré-fechamento vira item do checklist. O handoff não se limita a consentimentos de terceiros — também cobre:
  - **Voto da assembleia / outra ação pré-fechamento** — autorizações estatutárias (Lei 6.404 art. 122, X para alienação de bens do ativo permanente em S.A. — também CC art. 1.071 II para Ltda.), aprovações de conselho exigidas, períodos de notificação para direito de recesso (Lei 6.404 art. 137), mecânica de conversão, ou qualquer outra aprovação societária que a operação precisa. Caracterize a ação, o quórum/threshold de aprovação, a fonte estatutária ou regimental, e a restrição temporal.
  - **Notificações e aprovações regulatórias** — CADE (Lei 12.529/2011 — thresholds atuais R$ 750mi e R$ 75mi por Portaria Interministerial 994/2012; **verifique vigência**), aprovações setoriais sinalizadas durante extração (BCB, CVM, ANS, ANEEL, ANP, ANATEL, ANVISA conforme o caso).
  - **Consentimentos de contrapartes** — mudança de controle, cessão, gatilhos MFN.
  - **Quitações, terminações ou pagamentos** — quitações trabalhistas vinculadas a CoC, cartas de quitação, levantamento de gravames.
  - **Mecânica de escrow / retenção** — se a extração trouxe escrow de indenidade, entregável de seguro de R&W (mercado BR existente), ou retenção atrelada a questão específica.
  Todo achado com tag de ação pré-fechamento deve chegar ao closing-checklist, não só os rotulados "consentimento". Se um achado fica na zona cinza (pode precisar de ação pré-fechamento, pode ser covenant pós-fechamento), repasse com sinalização — closing-checklist pode descartar se o contrato disser o contrário. Sub-handoff é porta one-way; super-handoff é corrigido em revisão.


**Sucessão e responsabilidade do adquirente.** Sinalize: pretensões tort/responsabilidade de produto pendentes ou ameaçadas, questões ambientais e obrigações de remediação (responsabilidade objetiva e solidária — Lei 6.938/81 art. 14 §1º), exposição por sucessão tributária (CTN arts. 132-133 — atenção ao art. 133, II que rende sucessor responsável subsidiariamente se o alienante continuar atividade), sucessão trabalhista (CLT arts. 10 e 448 — independe de cláusula contratual), plano pós-fechamento de dissolução da vendedora (se vendedora dissolve, autores correm atrás de quem comprou), e se o SPA / Contrato de Trespasse (CC arts. 1.142-1.149 quando aplicável) tem anexo de passivos assumidos/excluídos que efetivamente cobre as exposições conhecidas. Mesmo em operações de ativos, as doutrinas de "incorporação de fato", "mera continuação" e "linha de produtos" podem transferir responsabilidade — esta é a análise que surpreende clientes buy-side que pensam estar comprando ativos limpos.

## Processamento em lote

Para categorias grandes (300 contratos), processe em lotes. Após cada lote, atualize a lista corrente de questões e sinalize qualquer 🔴 imediatamente — não espere pela categoria inteira para trazer à tona uma questão que afeta a operação.

## Encerre com a árvore de decisão de próximos passos

Encerre com a árvore de decisão de próximos passos conforme CLAUDE.md `## Outputs`. Customize as opções para o que esta skill acabou de produzir — os cinco ramos padrão (redigir o X, escalar, buscar mais fatos, observar e aguardar, outra coisa) são ponto de partida, não amarração. A árvore é a saída; o(a) advogado(a) escolhe.

Se a extração trouxe mais que ~10 questões, ou sempre que o(a) usuário(a) pedir: ofereça o dashboard (ver CLAUDE.md `## Outputs → Oferta de dashboard para saídas com muitos dados`). Modele a oferta para esta saída — contagens por severidade (🔴 / 🟠 / 🟡 / 🟢), contagens por categoria da casa, e grid ordenável de questões com materialidade, categoria e fonte VDR.

## O que esta skill não faz

- Não faz a chamada de materialidade em casos limítrofes. Aplica o limiar; humano(a) decide o borderline.
- Não negocia declarações e garantias. Produz os achados que as informam.
- Não substitui revisão IA em massa. Para extração de cláusulas em alto volume, repasse para Luminance/Kira conforme `~/.claude/plugins/config/claude-for-legal/societario/CLAUDE.md`. Esta skill é para a camada de juízo.
