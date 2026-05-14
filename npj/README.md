# Claude para Núcleos de Prática Jurídica (NPJ)

> Adaptação BR não oficial para Núcleos de Prática Jurídica (NPJ). Calibrado para Resolução CNE/CES 5/2018, Estatuto OAB (Lei 8.906/94), Lei 9.099/95 (JEC), Lei Maria da Penha, CDC, Lei 14.181/2021 (superendividamento). Veja `references/terminology-us-to-br.md`.

*Ampliando o acesso à justiça por meio de educação jurídica clínica assistida por IA.*

Um plugin para Núcleos de Prática Jurídica (NPJ) — as estruturas em que estudantes de Direito, sob supervisão de professor(a) advogado(a) inscrito(a) na OAB, prestam assistência jurídica gratuita a pessoas hipossuficientes (CPC arts. 98-102 — gratuidade de justiça). Direito de Família (incluindo Lei Maria da Penha 11.340/2006), Lei do Inquilinato (8.245/91), CDC, Defesa Criminal, Direitos Humanos, JEC (Lei 9.099/95), trabalhista, previdenciário (INSS/Lei 8.213/91), migração e refúgio (Leis 13.445/2017 e 9.474/97).

**Todo output é rascunho para análise pelo(a) estudante e revisão pelo(a) professor(a) advogado(a) inscrito(a) na OAB — marcado, sinalizado e registrado. O plugin organiza o trabalho; o(a) estudante raciocina; o(a) professor(a) supervisor(a) revisa e assume responsabilidade profissional (Estatuto OAB — Lei 8.906/94; Provimento CFOAB 11/2007 e seguintes sobre estágio). Nada sai do NPJ sem passar pelo modelo de supervisão configurado.**

## O problema que isso resolve

NPJs têm capacidade estruturalmente limitada. Um(a) professor(a) supervisor(a) acompanha 5–10 estagiários. Cada estagiário(a) carrega alguns casos enquanto cursa disciplinas. A turma muda a cada semestre. Tarefas administrativas — registro de atendimento, primeiras minutas, ponto de partida de pesquisa, atualizações de status — consomem horas que poderiam ir para o atendimento ao(à) assistido(a). Resultado: longas filas de espera, demanda reprimida, pessoas hipossuficientes que desistem.

Esse plugin reduz o tempo gasto em tudo que está *ao redor* da advocacia, de modo que os mesmos estagiários e o(a) professor(a) atendam significativamente mais pessoas — e que os estudantes passem mais tempo na análise e na estratégia, que é o que torna a educação clínica valiosa.

**Acelera o que não é educacional. Preserva o trabalho analítico.** Esse é o princípio de design.

## Quem usa

| Papel | Roda | Recebe |
|---|---|---|
| **Professor(a) supervisor(a) (advogado(a) OAB)** | `/cold-start-interview` (uma vez), `/supervisor-review-queue` (se revisão formal habilitada) | Contexto do NPJ configurado, trabalho do(a) estagiário(a) revisado |
| **Estagiários(as)** | `/ramp` (início do semestre), depois `/client-intake`, `/draft`, `/memo`, `/research-start`, `/status`, `/client-letter` | Pontos de partida — nunca produto final |

## Comandos

| Comando | O que faz | O que NÃO faz |
|---|---|---|
| `/cold-start-interview` | **Professor(a).** Configuração inicial do NPJ: áreas de atuação, comarca/foro, modelo de supervisão, upload de regimento interno do NPJ | — |
| `/build-guide` | **Professor(a).** Cria guia por área: perguntas de atendimento, postura pedagógica (assist / guide / teach), gates de revisão, checagens cross-plugin | Não substitui `/cold-start-interview` — afina skills para uma área |
| `/ramp` | **Estagiários(as).** Onboarding semestral: procedimentos do NPJ, walkthrough, exercícios práticos | Não substitui a orientação presencial do(a) professor(a) |
| `/client-intake` | Atendimento estruturado: templates por área, identificação de questões cross-área, sinalização de conflito, triagem (inclui encaminhamentos: Defensoria Pública, Procon, CRAS/CREAS, Delegacia da Mulher) | Não decide se o caso será aceito pelo NPJ |
| `/draft [doc]` | Primeira minuta: petição inicial JEC, contestação em despejo, requerimento de medida protetiva (Lei Maria da Penha), notificação extrajudicial, defesa prévia — sensível à comarca | Não produz peça final |
| `/memo` | Análise de caso estruturada (relatório + fundamentos + dispositivo — CF art. 93 IX) com lacunas de pesquisa sinalizadas | Não escreve a análise — estrutura |
| `/research-start [questão]` | Roteiro de pesquisa: legislação (CF, CC, CPC, CDC, CLT, Lei 9.099/95, etc.), jurisprudência STF/STJ/TST, termos para JusBrasil/STJ/STF | **Pistas, não citações autoritativas** — estagiários verificam tudo |
| `/status [audiência]` | Resumo do andamento: para o(a) assistido(a) (linguagem simples), interno, ou para o juízo | Não protocola nada |
| `/client-letter [tipo]` | Correspondência rotineira: confirmação de atendimento, solicitação de documentos, atualização breve | Não dá orientação substantiva — isso é `/status assistido` ou conversa presencial |
| `/deadlines` | Controle de prazos processuais — adiciona, agrega cross-caso com alertas em 14/7/3/1 dias, sinaliza vencidos | Não calcula prazos a partir do fato gerador; o(a) estagiário(a) faz a contagem conforme CPC arts. 218-235 / CPP / CLT |
| `/client-comms-log [caso]` | Registro append-only de comunicação por caso — ligações, e-mails, WhatsApp documentado, cartas, atendimentos presenciais | Não armazena análise jurídica substantiva; só registro de contato |
| `/semester-handoff` | Offboarding de fim de semestre — memorandos de passagem de caso para a próxima turma | Não encerra casos; casos encerrados ganham `/status interno` final e são marcados como arquivados |
| `/supervisor-review-queue` | **Professor(a), se revisão formal habilitada.** Fila de pendências, aprovar/editar/devolver | Opcional — um dos três modelos de supervisão |

## Pré-condições éticas e de sigilo

Antes de usar esse plugin com casos reais do NPJ, confirme com o(a) professor(a) supervisor(a), com a coordenação do NPJ e com a TI da instituição:

1. **Sua conta Claude e a política de retenção/treinamento aplicável.** Team, Enterprise, Education e contas individuais têm garantias diferentes. Confirme o que se aplica ao NPJ.
2. **Práticas de consentimento e de transparência sobre uso de IA** conforme **Provimento CFOAB 205/2021** (publicidade), **Provimento CFOAB 218/2023** (uso responsável de IA pela advocacia), **CED-OAB**, **Resoluções CNJ 332/2020 e 615/2025** (IA no Judiciário) e os deveres de sigilo do **Estatuto OAB (Lei 8.906/94, art. 7º XIX)**. Decida se e como o NPJ informa o(a) assistido(a) sobre o uso de IA; documente em ficha de atendimento.
3. **Como material sigiloso e dados pessoais serão tratados** sob a **LGPD (Lei 13.709/2018)** — o que pode ser colado em sessões, onde os outputs ficam, quem acessa, por quanto tempo, e como a rotatividade de estagiários afeta o acesso. Considere o NPJ como controlador e nomeie encarregado(a) se a instituição exigir.
4. **Áreas com sigilo reforçado** — violência doméstica (Lei Maria da Penha), refúgio (Lei 9.474/97), defesa criminal, infância e juventude (ECA 8.069/90), saúde mental — exigem cuidado adicional. Decida se o plugin é apropriado para esses tipos de caso.

Não pule essa etapa. O cold-start (`/npj:cold-start-interview`) captura essas decisões como Parte 0 antes de qualquer outra configuração.

## Marcadores de confiança

As skills sinalizam confiança inline para que estagiários e o(a) professor(a) supervisor(a) vejam onde a estrutura está incerta vs. afirmativa. Todo marcador é um chamado a verificar — nada marcado é confiável sem confirmação.

- `[RASCUNHO ASSISTIDO POR IA — exige análise do(a) estagiário(a) e revisão do(a) professor(a) advogado(a)]` — etiqueta-padrão. Rótulo de revisão, não vai no documento final ao(à) assistido(a) ou ao juízo; remova antes de qualquer protocolo.
- `[INCERTO: motivo específico]` — a skill está genuinamente em dúvida (jurisprudência divergente, tema controvertido, comarca pouco conhecida).
- `[VERIFICAR: alegação — checar fonte]` — afirmação provável mas não verificada. Estagiário(a) confirma antes de usar — citações de lei, redação de súmulas, formato local.
- `[PESQUISA NECESSÁRIA: ...]` — marcador de scaffold onde o enunciado de regra é lacuna, não conclusão. Estagiário(a) roda `/research-start` e preenche.
- `[ANÁLISE DO(A) ESTAGIÁRIO(A): ...]` — onde a aplicação fica em branco por design.
- `[CONCLUSÃO DO(A) ESTAGIÁRIO(A): ...]` — onde a conclusão fica em branco por design.
- `[FATO NECESSÁRIO: ...]` — minuta com fato faltante na ficha de atendimento. Estagiário(a) obtém o fato; não se chuta.
- `CONFIRMAR COM PROFESSOR(A) ANTES DE ENVIAR` / `ANTES DE PROTOCOLAR` — etiqueta de supervisão em modo "flags configuráveis".

Confie nos flags mais do que na ausência deles. Uma afirmação sem flag significa que a skill está confiante — não significa que o(a) estagiário(a) ou o(a) professor(a) pule a verificação. Os Provimentos da OAB sobre IA exigem verificação independentemente.

## Salvaguardas embutidas

Todo output de toda skill inclui:

- **Etiqueta de IA-assistido** — exige análise do(a) estagiário(a) e revisão do(a) professor(a) advogado(a)
- **Indicadores de confiança** — `[INCERTO: ...]` onde há dúvida genuína, em vez de chute
- **Prompts de verificação** — pontos específicos para conferir antes de confiar no output
- **Avisos éticos calibrados à tarefa** (CED-OAB, Provimentos CFOAB, sigilo profissional Lei 8.906/94 art. 7º XIX)
- **Encaminhamentos** — quando o caso foge da competência ou capacidade do NPJ: Defensoria Pública (LC 80/1994), Procon, CRAS/CREAS, Delegacia da Mulher / DEAM, MP, Conselho Tutelar (ECA), CONARE (refúgio)

**Saídas de pesquisa especificamente:** `/research-start` entrega pistas e arcabouços para o(a) estagiário(a) verificar e desenvolver. **Não** entrega citações como autoridade. Salvaguarda ética e recurso pedagógico — estagiários continuam aprendendo a pesquisar (Vade Mecum, sites do STF/STJ/TST, JusBrasil, Planalto/LEXML), só partem de um lugar melhor.

## Fluxo de supervisão (configurável)

Se o plugin inclui ou não fila formal de revisão — minuta do(a) estagiário(a) → revisão do(a) professor(a) → aprovado — é decisão real. Alguns NPJs querem gate duro; outros acham prescritivo demais.

O cold-start pede ao(à) professor(a) que escolha:

1. **Fila formal de revisão** — outputs voltados a assistido(a) ou juízo entram em fila, professor(a) aprova/edita/devolve, tudo logado
2. **Flags configuráveis, revisão informal** — gatilhos rotulam o output como "CONFIRMAR COM PROFESSOR(A)", sem fila — estagiário(a) tem o ônus de buscar a revisão
3. **Mão leve** — etiquetas-padrão e prompts de verificação em tudo, supervisão pelo formato existente (reuniões de caso, atendimento conjunto)

Mude depois editando `~/.claude/plugins/config/claude-for-legal/npj/CLAUDE.md`. Sua configuração fica nesse caminho independente de versão e sobrevive a atualizações do plugin.

## Rotatividade semestral: a solução `/ramp`

Todo semestre o NPJ reconstrói. Estagiários novos precisam de semanas para aprender procedimentos, ferramentas, fundamentos de cada área. `/ramp` é o onboarding interativo — lê o regimento do NPJ enviado pelo(a) professor(a) e ensina, com exercícios de baixa pressão (atendimento simulado, minuta de prática, roteiro de pesquisa) antes do(a) estagiário(a) tocar em caso real.

`/ramp --card` gera um cartão de referência de uma página: comandos, o que o Claude pode e não pode ajudar, hábitos de verificação. Distribua no primeiro dia.

## Marco ético: Estatuto OAB + Provimentos CFOAB + CED + Resoluções CNJ

A moldura ética em que esse plugin opera. Advogados(as) podem usar IA generativa, mas devem garantir competência na tecnologia, manter o sigilo profissional (EOAB art. 7º XIX), supervisar outputs, comunicar-se com o(a) assistido(a) quando apropriado, e verificar antes de confiar. As salvaguardas acima — etiquetas, indicadores, prompts de verificação, a não-autoridade explícita das saídas de pesquisa — foram desenhadas para esse modelo. Bases: **Lei 8.906/94 (Estatuto OAB)**, **Provimento CFOAB 205/2021**, **Provimento CFOAB 218/2023**, **CED-OAB**, **Resolução CNJ 332/2020**, **Resolução CNJ 615/2025**, **Resolução CNE/CES 5/2018** (DCN Direito + NPJ), **Lei 11.788/2008** (estágio) e **Estatuto OAB art. 3º §2º** (atuação do(a) estagiário(a) sob a OAB do(a) supervisor(a)).

Professores(as) de NPJ estão entre os(as) que mais pensam ética profissional na docência jurídica. O plugin foi feito para operar como eles(as) gostariam.

## Skills

| Skill | Propósito |
|---|---|
| **cold-start-interview** | Setup único do(a) professor(a) — áreas, comarca, supervisão, documentos-semente |
| **build-guide** | Guia por área — atendimento, postura pedagógica (assist/guide/teach), gates, checagens cross-plugin |
| **ramp** | Onboarding semestral do(a) estagiário(a) — procedimentos, ferramentas, exercícios |
| **client-intake** | Atendimento por área com identificação cross-área, conflito, triagem, encaminhamentos (Defensoria, Procon, CRAS/CREAS, DEAM) |
| **draft** | Primeira minuta — templates por área, sensível à comarca, ponto de partida explícito |
| **memo** | Estrutura relatório/fundamentos/dispositivo com lacunas sinalizadas — análise é do(a) estagiário(a) |
| **research-start** | Roteiro de pesquisa — pistas, não autoridades; estudantes verificam e desenvolvem |
| **status** | Resumos por audiência — assistido(a) / interno / juízo |
| **client-letter** | Correspondência rotineira a partir de templates |
| **supervisor-review-queue** | Fila formal opcional — ativa só se o(a) professor(a) escolher |
| **deadlines** | Prazos por caso, rollup cross-caso, cadência de alerta, sinalização de vencidos (CPC arts. 218-235) |
| **client-comms-log** | Registro append-only de comunicação — ligações, e-mails, WhatsApp, cartas, presencial |
| **semester-handoff** | Memorandos de fim de semestre; espelho do `/ramp` |

*(Duas skills depreciadas — `form-generation`, `plain-language-letters` — redirecionam para `/draft` e `/client-letter` + `/status assistido`.)*

## Conectores e verificação de citações

**Conecte uma ferramenta de pesquisa primeiro — os guardrails de citação dependem disso.** Sem ela, toda citação ganha `[verificar]` e a nota de revisão acima do entregável registra que as fontes não foram verificadas. O plugin funciona dos dois jeitos; só faz mais da verificação por você quando há ferramenta conectada.

Os conectores de pesquisa jurídica não são só fontes — são a diferença entre uma citação verificada e uma que você precisa checar. Uma citação obtida por **JusBrasil**, **STF**, **STJ**, **TST**, **Planalto** ou **LEXML** é marcada com a fonte e rastreável. Uma citação vinda do conhecimento do modelo ou de busca web genérica é marcada `[verificar]` ou `[verificar-pinpoint]` e deve ser conferida contra a fonte primária antes de qualquer uso. O plugin distribui as citações por confiança para o tempo de verificação ir onde importa.

## Integrações (perguntas em aberto)

Vem com o pacote geral de conectores em `.mcp.json`:

- **Slack** — busca em mensagens, leitura de canais, localização de discussões
- **Google Drive** — busca, leitura e download de documentos

Integração com sistema de gestão de NPJ (planilha institucional, sistema próprio da faculdade, ou ferramentas como Astrea/ADVBOX) é integração futura opcional. Começa com upload de arquivo.

Tier da conta (Team vs. Enterprise) para sigilo de assistido(a) é pergunta em aberto para a TI/coordenação de cada instituição. A arquitetura desktop processa dados localmente.

## Como aprende

Seu perfil de prática em `~/.claude/plugins/config/claude-for-legal/npj/CLAUDE.md` não é estático — melhora com o uso. Skills avisam quando um output usou um default que você devia ajustar. Você pode rodar o setup de novo, editar o arquivo direto, ou pedir a uma skill que registre uma nova posição.

## Estrutura de arquivos

```
npj/
├── .claude-plugin/plugin.json
├── .mcp.json                          # integração com sistema do NPJ opcional (futuro)
├── CLAUDE.md                          # perfil do NPJ — preenchido pelo cold-start
├── README.md
├── deadlines.yaml                     # registro operacional de prazos
├── skills/                            # cada skill é também o slash command /npj:<skill>
│   ├── cold-start-interview/          # Professor(a) — setup único
│   ├── build-guide/                   # Professor(a) — guia por área
│   ├── ramp/                          # Estagiários(as) — onboarding semestral
│   ├── client-intake/
│   │   └── references/intake-templates/
│   ├── draft/
│   ├── memo/
│   ├── research-start/
│   ├── status/
│   ├── client-letter/
│   ├── supervisor-review-queue/       # Professor(a), se revisão formal habilitada
│   │   └── references/review-queue.yaml
│   ├── deadlines/
│   ├── client-comms-log/
│   ├── semester-handoff/
│   ├── form-generation/               # depreciada → /draft (somente referência)
│   └── plain-language-letters/        # depreciada → /client-letter, /status assistido (somente referência)
├── handoffs/                          # memorandos de passagem por semestre
│   └── [AAAA-período]/
│       ├── _summary.md
│       └── [id-caso].md
├── client-comms/                      # registros de comunicação por caso
│   └── [id-caso]/
│       └── log.md
└── hooks/hooks.json
```

## Testes & QA


## Pré-requisitos

Algumas features dependem de integrações externas (gestão documental, sistema do NPJ, feeds normativos). Não vêm embutidas — se você tiver MCP server para uma delas no ambiente, as features correspondentes a usam. Sem isso, o plugin cai para upload de arquivo e fluxo manual. Rode `/npj --check-integrations` para ver o que está disponível.
