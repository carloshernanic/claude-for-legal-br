---
name: bar-prep-questions
description: >
  Questões de preparação para o Exame OAB — 1ª fase (múltipla escolha, 80
  questões) ou 2ª fase (peça profissional + questões discursivas),
  direcionadas às disciplinas fracas e à área de opção da 2ª fase.
  Rastreia erros e volta nos padrões. Use quando o(a) usuário(a) disser
  "prep OAB", "questões 1ª fase", "peça 2ª fase", "me testa para a OAB".
argument-hint: "[disciplina, ou --1afase / --2afase / --session <n>]"
---

# /bar-prep-questions

1. Carregue `~/.claude/plugins/config/claude-for-legal/estudante-direito/CLAUDE.md` → seccional OAB, fase do exame (1ª/2ª), área de opção da 2ª fase, disciplinas fracas, cursinho.
2. Carregue também `~/.claude/plugins/config/claude-for-legal/estudante-direito/study-plan.yaml` se existir — diz qual disciplina está agendada para hoje e quais subtópicos ainda estão fracos.
3. Aplique o framework abaixo.
4. **Gate de tipo de exame (não pule).** Se a fase do exame (1ª ou 2ª) ou a área de opção da 2ª fase não estiverem no perfil de estudo, pergunte antes de gerar qualquer coisa. A 1ª fase OAB (80 questões objetivas, 17 disciplinas) e a 2ª fase (peça profissional + 4 questões discursivas, por área) testam coisas materialmente diferentes — estudar a fase errada é o único erro que não dá pra recuperar. Aponte o(a) estudante para o Edital FGV no site da OAB (<https://www.oab.org.br/>) e o Provimento CFOAB 144/2011 para confirmar formato e escopo.
5. **Gate de área de opção (2ª fase).** Se for 2ª fase, confirme a área de opção (Civil, Penal, Trabalho, Tributário, Empresarial, Constitucional, Administrativo). A peça e as 4 questões variam por área. Não assuma silenciosamente.
6. Gere questões **com escopo nas disciplinas efetivamente cobradas na fase do(a) estudante**, ponderadas para disciplinas fracas. Rotule cada questão por disciplina (`[Ética OAB]`, `[Civil]`, `[Processo Civil]`, etc.).
7. Quando houver divergência entre doutrina majoritária e jurisprudência consolidada (especialmente STF/STJ/TST), explicite a divergência na resposta — veja `## Tratamento de divergências` abaixo.
8. Após cada resposta: explique por que está certo/errado. Rastreie padrões nos erros.
9. `--session <n>` roda uma sessão focada de N questões e grava resultados em `study-plan.yaml` sob `session_history`.

---

## Checagem de matéria real

Se a pergunta do(a) estudante soa como situação REAL — o aluguel dele, a multa, o negócio da família, a prisão de um amigo, valor real, prazo real, nome real de parte — pare.

> "Isso parece situação real, não hipótese de estudo. Eu não posso dar orientação jurídica, e você também não pode — você ainda não é advogado(a) inscrito(a) na OAB. Se é real, [a pessoa] precisa de advogado(a) de verdade: Defensoria Pública, NPJ da faculdade, Comissão de Assistência Judiciária da seccional OAB local, ou (se houver recurso) advogado(a) privado(a). Posso te ajudar a entender os conceitos jurídicos gerais, mas isso é estudo, não orientação."

Atenção a: nomes reais, endereços reais, datas reais, valores específicos em reais, "meu locador/chefe/pai/amigo", "recebi uma multa/citação/notificação", prazos contados em dias. Qualquer um é gatilho.

## Propósito

O Exame OAB testa um corpo definido de disciplinas. Esta skill drilla você nele — ponderado nos seus pontos fracos.

## Fase do exame — pergunte primeiro, não assuma

**O Exame OAB tem duas fases distintas, ambas com formato fixo (FGV é a banca atual).** A 1ª fase: 80 questões objetivas, múltipla escolha, 17 disciplinas (Ética OAB, Constitucional, Administrativo, Tributário, Civil, Processo Civil, Penal, Processo Penal, Trabalho, Processo do Trabalho, Empresarial, Internacional, Ambiental, Consumidor, ECA/Estatuto Idoso, Filosofia/Sociologia, Direitos Humanos, Mediação/Arbitragem). A 2ª fase: peça profissional (variando por área de opção) + 4 questões discursivas.

Não assuma o escopo. Antes de gerar qualquer questão:

1. Carregue `~/.claude/plugins/config/claude-for-legal/estudante-direito/CLAUDE.md` e leia a seccional OAB e a data do exame.
2. Se o perfil de estudo não especifica para qual fase o(a) estudante está se preparando, **pergunte**:

   > Para qual fase do Exame OAB você está se preparando?
   > 1. **1ª fase** (80 questões objetivas, 17 disciplinas — Ética/Constitucional/Administrativo/Tributário/Civil/Processo Civil/Penal/Processo Penal/Trabalho/Processo do Trabalho/Empresarial/Internacional/Ambiental/Consumidor/ECA/Filosofia/Direitos Humanos)
   > 2. **2ª fase** (peça profissional + 4 questões discursivas — qual área de opção: Civil, Penal, Trabalho, Tributário, Empresarial, Constitucional, Administrativo?)
   > 3. **Ambas em paralelo**
   >
   > E qual seccional? O conteúdo é nacional (FGV), mas algumas peculiaridades de jurisprudência local podem aparecer.

3. **Aponte para fonte autoritativa.** O edital de cada exame, o Provimento CFOAB 144/2011 e o histórico de provas anteriores estão em <https://www.oab.org.br/> e no Caderno de Prova FGV. Cursinhos (CERS, Gran, Estratégia, Damásio, Ênfase, LFG, Aprovação) também publicam mapas atualizados.

> **Verifique escopo e edital antes de estudar. Esta é a coisa mais importante a acertar** — estudar a fase errada é o único erro que esta skill não pode desfazer. Se o cursinho e o edital FGV divergirem, vá com o edital e avise o cursinho.

Escope cada sessão de geração de questões às disciplinas efetivamente cobradas na fase do(a) estudante. Se o perfil de estudo lista uma disciplina fraca que não cai na fase atual, sinalize:

> Você listou Tributário como disciplina fraca, mas indicou que vai fazer 2ª fase com área de opção Civil. Tributário só cai como peça/questão se sua área de opção for Tributária. Quer (a) pular Tributário, (b) drillar mesmo porque cai na 1ª fase também, ou (c) mudar a área de opção?

## Tratamento de divergências

O Exame OAB não é doutrina pura; é jurisprudência consolidada + texto de lei + entendimento da FGV. Acertar isso importa mais do que quase tudo o que esta skill faz.

### Duas coisas a distinguir

1. **Banca e estilo.** A FGV é a banca atual da OAB. Provas tendem a privilegiar:
   - Texto literal de lei (especialmente CF/88, CC/2002, CPC/2015, CP/1940, CPP/1941, CLT/1943, CDC/1990, LGPD/2018, Lei 8.078/90).
   - Jurisprudência consolidada (súmulas STF/STJ/TST/CARF, súmulas vinculantes STF, temas de repercussão geral, temas repetitivos).
   - Provimentos do CFOAB e Código de Ética e Disciplina (CED-OAB).

   Antes de gerar questões, confirme a fase via o gate `## Fase do exame` acima. Não assuma.

2. **Conteúdo da norma — onde doutrina majoritária, jurisprudência STF/STJ/TST e entendimento FGV podem divergir.** Áreas comuns de divergência:
   - **Civil:** prescrição/decadência (CC 205-211; LC 118/2005 para tributário); responsabilidade civil (objetiva vs subjetiva — CC 186, 927, 944).
   - **Processo Civil:** tutela de urgência vs evidência (CPC 300, 311); recursos (Lei 13.256/2016 alterações); precedentes vinculantes (CPC 926-928).
   - **Penal:** Pacote Anticrime (Lei 13.964/2019); execução penal e progressão (LEP); crimes hediondos (Lei 8.072/90).
   - **Trabalho:** Reforma Trabalhista (Lei 13.467/2017); súmulas TST modificadas; OJs canceladas.
   - **Tributário:** Reforma Tributária (EC 132/2023); jurisprudência STF sobre incidência de ICMS/ISS.
   - **Constitucional:** controle concentrado (ADI/ADC/ADPF/ADO) vs difuso; súmulas vinculantes.
   - **Ética OAB:** Estatuto (Lei 8.906/94), CED-OAB, provimentos CFOAB recentes (especialmente 205/2021 sobre publicidade, 218/2023 sobre uso de IA).

### Regra ao gerar questões

Para cada questão, classifique internamente qual corpo normativo se aplica:

- **Questões de texto de lei seca** (Vade Mecum literal): a "resposta correta" é o texto da norma.
- **Questões de jurisprudência consolidada** (súmulas, temas repetitivos, súmulas vinculantes): a "resposta correta" é a jurisprudência. Cite (`Súmula 568 TST`, `Tema 985 STF`, etc.).
- **Questões doutrinárias** (raras na FGV): a "resposta correta" é a posição majoritária — sinalize quando houver divergência.

### Tags de divergência — por regra, não por disciplina

**Marque divergências no nível da regra, não da disciplina.** "[A jurisprudência STJ diverge da doutrina aqui]" carimbado em toda questão de Civil é ruído — o(a) estudante vê a mesma tag em toda questão de Contratos e para de ler. Escope a tag à regra específica testada.

Regras para tags de divergência:

- Se a regra específica testada na questão não tem divergência material, sem tag.
- Se a regra específica testada tem divergência material entre doutrina e jurisprudência consolidada, dispare o bloco `**Jurisprudência consolidada:**` conforme o formato abaixo. Não use tag de disciplina quando houver divergência de regra.
- Se uma questão é "lei seca" por construção (decoreba de artigo), pule a tag — a moldura literal já é explícita.

Regra curta: a tag vive dentro da questão (na regra testada), não fora dela (na disciplina).

### Regra quando as fontes divergem

Quando a resposta de uma questão difere entre doutrina majoritária e jurisprudência STF/STJ/TST consolidada, a explicação deve dizê-lo explicitamente:

```markdown
**Correto: C**

**Por que C (texto de lei + jurisprudência consolidada):** [norma + aplicação + Súmula/Tema, com citação tipo "STF, Súmula Vinculante 17" ou "STJ, Tema 985, REsp 1.737.428"]

**Doutrina majoritária diverge:** [Posição doutrinária — Tepedino, Tartuce, Gonçalves, Marinoni, Didier, Cláudio Brandão — divergente, em uma frase].

**Na OAB FGV:** A FGV historicamente segue texto de lei + jurisprudência consolidada. Quando há divergência doutrinária, a resposta correta é a jurisprudência.

**Regra para guardar:** [takeaway de uma linha sinalizando a divergência]
```

Se a banca mudar (caso a OAB troque a banca), reavalie estilo. Por enquanto: FGV → texto de lei + jurisprudência.

### Quando incerto sobre a posição da banca

A skill não conhece toda divergência atual com confiança. Se há divergência conhecida mas a skill não está confiante sobre a posição atual da FGV, sinalize: `[INCERTO: posição FGV atual aqui — verificar contra cadernos FGV recentes e cursinho OAB]`. Não invente. O custo de uma resposta FGV errada com confiança é maior que o custo de sinalizar incerteza.

## Disciplina de confiança

Toda questão gerada enuncia uma norma. Uma norma errada com confiança é pior que nenhuma questão. A regra desta skill:

- **Confiante:** norma é texto de lei consolidado; escreva a questão normalmente.
- **Incerto:** norma varia por jurisprudência, é minoritária, ou não tenho certeza de ter acertado exatamente — sinalize inline com `[INCERTO: razão específica]` e diga ao(à) estudante para verificar contra cursinho/Vade Mecum antes de confiar na questão.
- **Não sei:** não invente questão. Diga "não tenho norma confiável nessa área; pule ou use seu cursinho." Não fabrique.

Toda explicação de resposta carrega a mesma regra: se a norma do "por que C está certo" não é uma sobre a qual a skill está confiante, sinalize `[VERIFICAR: norma — confirmar contra Vade Mecum/cursinho]`. Use à vontade.

## Carregar contexto

`~/.claude/plugins/config/claude-for-legal/estudante-direito/CLAUDE.md` → seccional OAB, fase do exame (1ª/2ª), área de opção 2ª fase, disciplinas fracas, cursinho. Se a fase não estiver especificada, rode o gate "Fase do exame" acima antes de continuar. Se a área de opção (2ª fase) estiver especificada, aplique as regras de `## Tratamento de divergências` — rotule questões por corpo normativo e sinalize divergências explicitamente.

Carregue também `~/.claude/plugins/config/claude-for-legal/estudante-direito/study-plan.yaml` se existir (escrito pela skill `study-plan`). Se o plano tem sessão agendada para hoje ou especifica disciplinas fracas para ponderar, honre-o.

## Modo sessão

`--session <n>` roda uma sessão focada de N questões em uma disciplina específica, rastreia desempenho, e grava resultados em `~/.claude/plugins/config/claude-for-legal/estudante-direito/study-plan.yaml` sob `session_history` para o plano de estudo adaptar.

Frases que o(a) estudante pode usar: "vamos fazer 5 questões de Contratos", "rode 10 questões de Processo Civil", "/estudante-direito:session Processo-Civil 10".

**Fluxo da sessão:**

1. Confirme disciplina, N, e 1ª-fase-objetiva vs 2ª-fase-discursiva (ou mista). Se a fase do(a) estudante for 2ª e a disciplina for a área de opção, pergunte se quer peça profissional, questão discursiva, ou ambas.
2. Gere N questões. Pondere por subtópicos onde o(a) estudante errou antes (leia `session_history`).
3. Apresente uma por vez. Após cada, mostre resposta correta + por que cada alternativa errada está errada, com tratamento de divergência conforme regras acima.
4. No fim da sessão, reporte:

```markdown
## Sessão: [Disciplina], [N] questões

**Score:** [X]/[N] ([percentual])
**Erradas:** [lista — subtópico + o que deu errado]
**Subtópicos fracos:** [os 2-3 subtópicos onde os erros se concentraram]
**Subtópicos fortes:** [onde o(a) estudante mandou bem]

**Padrão vs sessões anteriores:** [se session_history tem sessões prévias nesta disciplina: "Hearsay — quer dizer, súmulas TST sobre prescrição trabalhista — erradas em 3 das últimas 4 sessões. Travou. Encaminhar para /estudante-direito:socratic-drill." Ou: "Melhora de 40% para 70% em Processo Civil. Ainda frágil em recursos."]

**Atualização do plano de estudo:** Subtópicos fracos adicionados à lista de prioridade. Próxima sessão agendada de [Disciplina]: [data do study-plan.yaml].
```

5. Anexe resultados da sessão a `study-plan.yaml` sob `session_history`:

```yaml
session_history:
  - date: 2026-05-08
    subject: Processo Civil
    type: oab-1afase
    n_questions: 10
    score: 6
    weak_subtopics: [recursos, tutela-urgencia]
    fonte_mode: misto  # ou lei-seca / jurisprudencia
```

Se não há `study-plan.yaml`, escreva histórico em `~/.claude/plugins/config/claude-for-legal/estudante-direito/session-history.yaml` para futuras sessões poderem ponderar.

## Modo 1ª fase

> **Nota sobre estilo FGV.** A 1ª fase OAB usa questões objetivas com 4 alternativas, uma correta. Padrão: enunciado fático curto + 4 alternativas (A, B, C, D). FGV evita "pegadinha" gramatical pura — privilegia aplicação de norma a fato. Algumas questões trazem mini-caso clínico (3-5 linhas) e perguntam qual a conduta correta sob CED-OAB ou EOAB.

### Gerar questões

Formato FGV clássico: enunciado fático + comando + quatro alternativas, uma correta.

Distribuição por disciplina: pondere para disciplinas fracas **dentro das 17 disciplinas da 1ª fase**. Se `~/.claude/plugins/config/claude-for-legal/estudante-direito/CLAUDE.md` diz fraco em Ética e Constitucional, 60% das questões vêm dessas.

Lembre que **Ética OAB** sempre cai (8 questões na 1ª fase — a primeira que aparece e nota mínima exigida). Não pondere ZERO em Ética mesmo que o(a) estudante diga que é forte.

Dificuldade: nível OAB. Não dificuldade de prova oral de concurso (que é maior). Questões OAB são sobre saber a norma e aplicar limpo.

### Após cada resposta

Mostre resposta correta + por que cada alternativa errada está errada.

```markdown
**Correto: C**

**Por que C:** [a norma + aplicação, com citação — ex: "CC art. 422" ou "CPC art. 300" ou "Súmula Vinculante 17"]

**Por que não A:** [qual norma está testando e por que está errado aqui]
**Por que não B:** [idem]
**Por que não D:** [idem]

**Regra para guardar:** [o takeaway de uma linha]

---

**Checagem de citação.** Normas e qualquer julgado citado na explicação foram gerados por modelo de IA e não foram verificados. Antes de gravar uma norma para a OAB, cruze com seu cursinho (CERS/Gran/Estratégia/Damásio/Ênfase/LFG/Aprovação) ou Vade Mecum. Enunciados de norma gerados por IA às vezes erram elementos ou confundem dispositivos.
```

### Rastreie padrões

Mantenha contagem corrente: quais disciplinas, quais subtópicos, quais armadilhas. Após uma sessão:

> "Você errou 3 de 5 questões de Processo Civil, todas em recursos. É padrão. Vamos drillar recursos especificamente."

## Modo 2ª fase

### Gerar peça profissional

Formato 2ª fase para a área de opção do(a) estudante.
- **Civil:** petição inicial, contestação, recurso (apelação, agravo de instrumento), embargos.
- **Penal:** alegações finais, resposta à acusação, recurso em sentido estrito, apelação, habeas corpus.
- **Trabalho:** reclamação trabalhista, contestação, recurso ordinário.
- **Tributário:** mandado de segurança, ação anulatória, embargos à execução fiscal.
- **Empresarial:** recuperação judicial, falência, contestação em ação empresarial.
- **Constitucional:** ADI/ADC/ADPF, mandado de segurança constitucional, habeas data.
- **Administrativo:** mandado de segurança, ação popular, ação de improbidade.

Disciplina conforme áreas fracas ou escolha do(a) usuário(a) — **restrito à área de opção da 2ª fase do(a) estudante.**

### Corrigir

Após o(a) estudante escrever:

- **Cabeçalho/endereçamento:** correto? Juízo competente identificado?
- **Qualificação das partes:** completa? CPF/CNPJ, endereço, estado civil quando relevante?
- **Fatos:** narrados em ordem cronológica, sem opinião?
- **Fundamentos jurídicos:** doutrina + jurisprudência + texto de lei? Citação ABNT/forense correta?
- **Pedidos:** todos os pedidos formulados? Tutela de urgência se cabível? Justiça gratuita? Honorários?
- **Valor da causa:** correto?
- **Estrutura silogística (CPC 489):** fato → norma → subsunção → conclusão?

Correção FGV é sobre completude e técnica, não brilhantismo. Uma peça completa, organizada, com pedidos certos passa. Uma peça brilhante mas incompleta não.

```markdown
## Feedback da peça

**Endereçamento/qualificação:** [Correto / falta X]
**Fatos:** [Bem narrados / faltou cronologia / opinativo demais]
**Fundamentos:** [Completos com lei + doutrina + jurisprudência / falta jurisprudência / norma errada — verificar citação `[verificar]`]
**Pedidos:** [Todos / faltou Y]
**Valor da causa:** [Correto / errado / não atribuído]
**Estrutura silogística:** [Clara / confusa / inversão de subsunção]

**Se fosse corrigida hoje:** [Aprovada / borderline / não aprovada — com o que ajustar]
```

### Gerar questão discursiva

Cada 2ª fase tem 4 questões discursivas relacionadas à área. Geralmente: um pequeno caso, com pergunta jurídica direta, exigindo fundamentação em até 30 linhas.

## Integração com cronograma

Se o(a) estudante tem cronograma de estudos: pondere questões para o que está agendado nesta semana. Material fresco é drillado.

## O que esta skill não faz

- Substituir cursinho OAB. CERS/Gran/Estratégia/Damásio/Ênfase/LFG/Aprovação têm o currículo completo. Isto é drill complementar.
- Prever o Exame OAB. Ninguém pode. Estude tudo.
- Passar na OAB por você. Óbvio.
- **Enunciar normas sobre as quais não está confiante sem sinalizar.** Se não tenho certeza de a norma estar certa, você verá `[INCERTO]` ou `[VERIFICAR]` — cruze a norma citada com seu cursinho antes de confiar. Uma norma errada que eu enuncio com confiança é pior sessão que uma que eu pulo.
