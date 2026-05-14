---
name: study-plan
description: >
  Monta ou atualiza um plano de estudo de longo prazo para a OAB (ou prova
  de faculdade) — fases, disciplinas ponderadas por fragilidade, cronograma
  diário, adaptativo ao histórico de sessões em study-plan.yaml. Use quando
  o(a) usuário(a) disser "monta plano de estudo", "planeja minha
  preparação OAB", "agenda meu estudo", ou "como devo estudar para [X]".
argument-hint: "[--build | --update | --status | --cram]"
---

> **Nota BR.** Adaptação não oficial. Cursinhos default: CERS, Gran, Estratégia, Damásio, Ênfase, LFG, Aprovação. Calibrado para Exame OAB FGV (1ª fase 80 questões / 2ª fase peça + 4 discursivas). Também serve para concursos jurídicos (magistratura, MP, defensoria, AGU, PGE/PGM — note CF 93 I que exige 3 anos de atividade jurídica para magistratura/MP).

# /study-plan

1. Carregue `~/.claude/plugins/config/claude-for-legal/estudante-direito/CLAUDE.md` → seccional OAB, fase do exame, data do exame, disciplinas fracas, horas-alvo/dia, cursinho.
2. Carregue `~/.claude/plugins/config/claude-for-legal/estudante-direito/study-plan.yaml` se existir.
3. Aplique o framework abaixo.
4. Roteie pela flag:
   - `--build` (default se não há plano): caminhe pelo gate de entradas (exame, disciplinas, horas/semana, dias de descanso, métodos). Construa fases + cronograma diário das duas primeiras semanas. Escreva `study-plan.yaml`.
   - `--update` (default se há plano): releia `session_history`, ajuste prioridades e `weekly_hours`, preencha o próximo trecho do cronograma diário.
   - `--status`: o que está agendado hoje / esta semana, tendência de score, disciplinas escorregando, próxima sessão por disciplina.
   - `--cram`: força modo cram — 80/20 nos temas de alto rendimento, volume diário de objetiva, taper últimos 2-3 dias.
5. Antes de escrever: resuma o plano em prosa e confirme com o(a) estudante. Ajuste pela resposta.
6. Sempre sanity-check horas/semana contra restrições de vida declaradas. Planos hiper-ambiciosos falham.

---

## Propósito

Sentar para estudar sem saber o que estudar é como semanas somem. Esta skill monta plano — semanas até o exame, sessões por dia, disciplinas por semana, tipos de sessão — e adapta conforme você efetivamente faz. Plano vivo, não export de calendário.

Também dá às skills downstream (bar-prep, flashcards, drill, irac) cronograma compartilhado para honrar, para você não ouvir "o que quer estudar hoje" toda vez que abre uma sessão.

## Disciplina de confiança

Plano é opinião, não doutrina. A skill diz claramente o que é estimativa:

- **Estimativas de tempo por tema** são orientação geral (com base em pesos típicos de cursinhos CERS/Gran/Estratégia/Damásio/Ênfase/LFG). Sinalize como estimativa — seu ritmo real vai diferir.
- **Pesos de disciplina** derivam das suas disciplinas fracas reportadas e do histórico de sessão. Confiante.
- **Priorização de temas de alto rendimento em modo cram** baseia em padrões de frequência multianual da FGV no Exame OAB. Sinalize qualquer alegação "isso cai na prova" como `[INCERTO — frequência passada não é predição]`.

## Carregar contexto

`~/.claude/plugins/config/claude-for-legal/estudante-direito/CLAUDE.md`:
- Seccional OAB, fase do exame, data do exame
- Disciplinas atuais (uso fora-OAB)
- Disciplinas fracas (1ª fase: Ética/Const/Admin/Trib/Civil/PC/Penal/PP/Trab/PT/Empresarial/Internacional/Ambiental/Consumidor/ECA/Filosofia/Direitos Humanos/Mediação; 2ª fase: a área de opção)
- Cursinho
- Horas-alvo/dia

`~/.claude/plugins/config/claude-for-legal/estudante-direito/study-plan.yaml` se existir — estenda, não sobrescreva.

## Workflow

### Passo 1: O que estamos planejando

> Para que estamos planejando?
>
> 1. **Exame OAB** (tem data em mente — XLII Exame? 2026.1?)
> 2. **Prova de faculdade específica ou bloco de finais**
> 3. **Cadência semestral geral** (esquematizar, leitura, drill em todas as disciplinas)
> 4. **Concurso jurídico** (magistratura, MP, defensoria, AGU, PGE/PGM — note CF 93 I para magistratura/MP: 3 anos de atividade jurídica após OAB)

Para (1) OAB: leia data do perfil, confirme. Se não há data, pergunte qual Exame (numerador FGV + ano).
Para (2) prova: pergunte qual disciplina, qual data, qual formato.
Para (3) semestre: peça data do fim do período como âncora.
Para (4) concurso: pergunte qual banca, qual cargo, qual fase (1ª objetiva / 2ª discursiva / 3ª oral / prova de títulos), qual data.

### Passo 2: Entradas — uma por vez, espere cada

**Pergunte e espere.** Não junte tudo num só prompt.

- **Data do exame:** confirmada? (Se OAB: peça seccional se não no perfil — embora conteúdo seja nacional, peculiaridades de jurisprudência local podem aparecer.)
- **Disciplinas a cobrir:** para OAB, leia do edital FGV / Provimento CFOAB 144/2011 conforme a fase. Para faculdade, plano de ensino. Confirme — "alguma disciplina para adicionar ou tirar?"
- **Disciplinas mais fortes:** menor prioridade. Ainda revisadas, não drilladas pesado.
- **Disciplinas mais fracas:** maior prioridade. Mais sessões.
- **Horas por semana disponíveis:** realista, não aspiracional. "Consigo 20h" é diferente de "vou fazer 20h por 8 semanas". Pergunte o que sustenta.
- **Sanity check de contexto de vida — force.** Depois do número, pergunte (uma por vez — não pule):

  > Você disse [N]h/semana. Antes de eu montar, me conta o que mais tem na sua semana — trabalho (h/semana), família (filhos, cuidado), deslocamento, treino, terapia, estágio/NPJ, qualquer coisa relevante. O plano deve caber na sua vida, não o contrário. Plano que você não cumpre é pior que plano mais leve que você cumpre.

  Espere a resposta. Faça sanity-check das horas declaradas contra a carga reportada:

  > São ~[X]h/dia em [N] dias de estudo, em cima de [trabalho + família + deslocamento + outros]. Na minha leitura é [realista / apertado / insustentável]. Quer ajustar h/semana antes de montar, ou manter e ver como vai a semana 1?

  Não pule mesmo que o número-alvo já tenha sido capturado no cold-start. O perfil captura o que disse; o sanity-check captura se sustenta. Se o check produz número menor, use o menor e anote em `confidence_flags`.

  Se o(a) estudante recusa compartilhar contexto ("só monta"), respeite — mas adicione `confidence_flags`: "Sanity-check de contexto de vida recusado; plano assume [N]h/semana sustentável. Revisitar fim da semana 2 se aderência < [X]%."
- **Métodos de estudo preferidos:** múltipla seleção. Questões objetivas (1ª fase OAB) / peças e questões discursivas (2ª fase) / flashcards / esquematizado / drill / releitura. Pondere o cronograma para o que efetivamente vai fazer.
- **Dias de descanso por semana:** importa. Planos 7/7 falham na semana 3.

### Passo 2.5: Suplementar vs. substituir (cursinho OAB)

Se `~/.claude/plugins/config/claude-for-legal/estudante-direito/CLAUDE.md` → `Cursinho` é **CERS**, **Gran**, **Estratégia**, **Damásio**, **Ênfase**, **LFG**, **Aprovação**, ou outro cursinho estruturado (NÃO `próprio` ou `N/A`), o(a) estudante já tem cronograma. Esta skill tem que escolher um de dois papéis — não pode rodar currículo paralelo completo sem queimar.

Pergunte, uma pergunta, espere:

> Seu perfil diz [CERS / Gran / Estratégia / Damásio / Ênfase / LFG / Aprovação]. Eles publicam cronograma diário com cada disciplina e tarefa. Duas formas deste plano funcionar — escolha uma:
>
> 1. **Suplementar.** O cursinho é seu currículo principal. Este plano preenche lacunas: drilling extra das objetivas (1ª fase) nas suas disciplinas fracas, prática direcionada de peça/discursiva (2ª fase), loops de flashcard nos temas em que erra. Não reconstruo o cronograma; sobreponho.
> 2. **Substituir.** Você não segue o cronograma do cursinho (talvez porque o ritmo não cabe na sua vida). Monto o plano todo — disciplinas, horas, fases, agenda — e você larga o do cursinho.
>
> Não escolha os dois. Rodar dois currículos completos um contra o outro é como estudante explode na semana 4.

Espere. Anote no yaml `prep_course_mode: supplement | replace`.

Se **supplement**: cronograma diário mais leve — só adiciona drill nas fracas e prática direcionada, não duplica cobertura. Flag em `confidence_flags`: "Modo suplementar — plano assume cobertura principal pelo [cursinho]. Se atrasar, conte e replanejamos."

Se **replace**: monte plano completo conforme abaixo.

Se cursinho é `próprio` ou `N/A`, pule — não há o que suplementar.

### Passo 3: Monte o cronograma

Calcule semanas-ao-exame de hoje. Depois:

**Modo normal (4+ semanas):**
- Divida em fases:
  - **Fase de aprendizagem** (primeiros ~60%): uma disciplina por ~3-5 dias, mistura esquematizado/leitura com flashcards e algumas questões em material fresco.
  - **Fase de drilling** (próximos ~30%): mais volume de objetiva, mais peça/discursiva, condições simuladas, todas as disciplinas em rotação.
  - **Fase de revisão** (últimos ~10%): foco em subtópicos fracos do `session_history`, simulados completos, revisão leve nas fortes.
- Pondere por fragilidade: fracas ganham ~2x as horas das fortes.
- Agenda dia-a-dia: qual disciplina, qual método, quanto tempo. Deixe folga para vida real.

**Modo cram (< 4 semanas):**
- Sinalize: "Você está a menos de 4 semanas. Modo cram — plano prioriza alto rendimento sobre cobertura completa. Vão sobrar lacunas. É o trade-off."
- 80/20: disciplinas OAB mais frequentes historicamente (Ética OAB, Civil, Processo Civil, Constitucional, Empresarial — varia por exame FGV) ganham a parte do leão. Mais estreitas ganham cobertura mínima viável.
- Cronograma diário: bloco de objetiva todo dia (volume importa agora), peça/discursiva dia sim dia não, um simulado por semana.
- Sono e taper nos últimos 2-3 dias. NÃO agende drilling pesado no dia antes da prova. Estudantes que viram a noite antes pontuam pior.

### Passo 4: Escreva

Escreva em `~/.claude/plugins/config/claude-for-legal/estudante-direito/study-plan.yaml`:

```yaml
plan_type: oab  # ou prova-faculdade ou semestre ou concurso
exam_date: 2026-07-28
seccional: SP
exam_format: oab-1afase  # ou oab-2afase / concurso-1fase / prova-final
created: 2026-05-08
last_updated: 2026-05-08
weeks_to_exam: 12
hours_per_week: 25
days_per_week: 6
mode: normal  # ou cram
phases:
  - name: aprendizagem
    start: 2026-05-08
    end: 2026-06-20
    focus: esquematizado, flashcards, objetivas introdutórias
  - name: drilling
    start: 2026-06-21
    end: 2026-07-18
    focus: volume de objetivas, prática de peça, simulados
  - name: revisao
    start: 2026-07-19
    end: 2026-07-27
    focus: revisão de subtópicos fracos, simulados completos
subjects:
  etica-oab:
    priority: high  # sempre alta — Ética cai sempre
    weekly_hours: 4
    methods: [objetivas, flashcards]
  civil:
    priority: high  # fraca
    weekly_hours: 5
    methods: [objetivas, flashcards, peça]
  constitucional:
    priority: medium
    weekly_hours: 3
    methods: [objetivas, esquematizado-revisao]
  # etc.
schedule:
  - date: 2026-05-08
    day: quinta
    sessions:
      - subject: Civil
        method: esquematizado-revisao
        duration_min: 90
      - subject: Civil
        method: objetivas
        duration_min: 60
        n_questions: 25
  - date: 2026-05-09
    day: sexta
    sessions:
      - subject: Penal
        method: flashcards
        duration_min: 45
      - subject: Penal
        method: peca
        duration_min: 60
  # etc.
session_history: []  # anexado por bar-prep-questions, flashcards, socratic-drill, irac-practice
```

### Passo 5: Confirme com o(a) estudante

**Cabeçalho — obrigatório em cada apresentação em chat e em qualquer documento de plano em prosa salvo ao lado do YAML.** Primeira linha do resumo (e do `study-plan.md` companheiro) deve ser o cabeçalho verbatim do config `## Saídas`:

```
NOTAS DE ESTUDO — NÃO É ORIENTAÇÃO JURÍDICA
```

O cabeçalho não vai dentro do YAML (é arquivo de dados), mas vai no resumo em prosa que mostra ao(à) estudante e em documento humano-legível. Não disclaimer; é identidade. Não omita, reformule ou realoque.

Resuma em prosa (não YAML cru) antes de salvar, com cabeçalho:

> NOTAS DE ESTUDO — NÃO É ORIENTAÇÃO JURÍDICA
>
> Aqui está o que montei. [X] semanas até o [exame]. [Y]h/semana em [Z] dias. Disciplinas fracas (Civil, Penal) ganham 2x. Três fases: aprendizagem até [data], drilling até [data], revisão últimos [N] dias. Agendei as duas primeiras semanas dia-a-dia. Além disso é alocado por semana — preencho o diário conforme você completa sessões, então o plano adapta para onde você efetivamente está.
>
> Parece certo? Ambicioso demais? Leve demais? Falta disciplina?

Ajuste pela resposta. Então escreva.

## Adaptando o plano

Após cada sessão (via bar-prep-questions, flashcards, socratic-drill, irac-practice), a skill correspondente anexa a `session_history`:

```yaml
session_history:
  - date: 2026-05-08
    subject: Civil
    type: oab-1afase
    n_questions: 10
    score: 6
    weak_subtopics: [prescricao-decadencia, responsabilidade-civil-objetiva]
```

No próximo `/estudante-direito:study-plan --update` (ou quando uma skill detecta plano vencido):
- Disciplinas com scores consistentemente baixos sobem em `priority` e `weekly_hours`.
- Subtópicos fracos dentro de uma disciplina são sinalizados para a próxima sessão.
- Se o(a) estudante está atrasado(a) (sessões agendadas não aparecem no histórico), ajuste: compacta cobertura ou anota o gap e pergunta.
- Se está adiantado(a), abra tempo para drill mais profundo nas fracas.

## Modos

`--build` (default) — plano fresco
`--update` — releia `session_history` e ajuste pesos, preencha próximo cronograma
`--status` — o que está hoje / esta semana, tendência de score, o que escorrega
`--cram` — força modo cram mesmo com mais de 4 semanas (override)

## Integração

- `/estudante-direito:session <disciplina> <n>` escreve resultados no `session_history` deste plano.
- `/estudante-direito:bar-prep-questions` lê o plano para saber qual disciplina está agendada hoje.
- `/estudante-direito:flashcards` pode `--session <n>` e resultados caem no plano.
- `/estudante-direito:socratic-drill` e `/estudante-direito:irac-practice` concluídas também anexam.

## O que esta skill não faz

- **Garantir aprovação.** O plano é andaime. O trabalho é seu.
- **Prever o exame.** Modo cram usa frequência histórica; alto rendimento ≠ teste garantido.
- **Substituir cronograma de cursinho.** Se está em CERS/Gran/Estratégia/Damásio/Ênfase/LFG/Aprovação, este plano suplementa — não rode dois currículos completos contra. Use um como principal.
- **Agendar sua vida.** Horas disponíveis é o que você diz. Se exagera, plano quebra na semana 2. Seja honesto(a).
