---
name: cold-start-interview
description: >
  Entrevista de cold-start da casa (request list + memo anterior), ou --new-deal
  para contexto específico da operação. Modular: identifica quais áreas de
  prática aplicam (M&A, Conselho & Secretaria, Companhia Aberta, Gestão de
  Entidades), depois faz perguntas direcionadas para cada módulo ativo e grava
  apenas as seções relevantes na config do plugin. Use em instalação nova,
  quando CLAUDE.md ainda tem [PLACEHOLDER], ao iniciar nova operação, ou para
  re-verificar integrações ou atualizar um módulo.
argument-hint: "[--redo | --new-deal | --check-integrations | --module [m&a | board | public | entities]]"
---

# /cold-start-interview

> **Adaptação BR não oficial.** Calibrado para Lei 6.404/1976, Código Civil, CVM, CADE, ANPD, BCB. Veja `references/terminology-us-to-br.md`. Toda saída é rascunho para revisão por advogado(a) inscrito(a) na OAB.

1. Verifique `~/.claude/plugins/config/claude-for-legal/societario/CLAUDE.md`. Se `--new-deal`, vá direto ao setup por operação. Se `--check-integrations`, pule a entrevista — re-rode apenas a Parte 0 `O que está conectado?` e reescreva a tabela `## Integrações disponíveis` em `CLAUDE.md`. Ao sondar: só reporte ✓ se uma chamada MCP de fato teve sucesso. Conectores configurados-mas-não-testados são marcados ⚪ com um how-to de uma linha. Nunca reporte ✓ baseado apenas em declarações em `.mcp.json`.
2. Rode a entrevista (Parte 0 primeiro — papel + integrações — depois módulos).
3. Documentos seed: request list de diligência + um memo de issues anterior.
4. Extraia: categorias, thresholds, formato do memo, configuração de ferramenta IA.
5. Migração: se um CLAUDE.md populado (sem `[PLACEHOLDER]`) existir em `~/.claude/plugins/cache/claude-for-legal/societario/*/CLAUDE.md` mas não no caminho de config, copie para o caminho de config e diga ao(à) usuário(a) o que foi migrado.
6. Grave `~/.claude/plugins/config/claude-for-legal/societario/CLAUDE.md` (crie diretórios pai conforme necessário). Para `--new-deal`, grave `~/.claude/plugins/config/claude-for-legal/societario/deals/[code]/deal-context.md`.

---

## Propósito

Funções societárias variam mais do que quase qualquer outra prática jurídica. Um(a) Diretor(a) Jurídico(a) solo em startup de 50 pessoas roda M&A, gerencia o cap table e secretaria o conselho. Um(a) advogado(a) societário(a) em multinacional pode cuidar só de divulgações CVM e comitê de divulgação. Esta entrevista descobre quais áreas estão ativas e constrói só o perfil de prática relevante — nada deixado em branco que não se aplique.

## Check de cold-start

Leia `~/.claude/plugins/config/claude-for-legal/societario/CLAUDE.md`:
- **Não existe** → comece a entrevista.
- **Contém `<!-- SETUP PAUSADO EM: -->`** → cumprimente e ofereça retomar dali.
- **Contém `[PLACEHOLDER]` mas sem comentário de pausa** → template nunca foi concluído; ofereça começar do zero ou retomar.
- **Populado (sem placeholders, sem pausa)** → já configurado; pule a menos que `--redo` ou `--module [name]`.

A estrutura do template vive em `${CLAUDE_PLUGIN_ROOT}/CLAUDE.md` — use como scaffold. Grave o perfil de prática completo no caminho de config, criando diretórios conforme necessário.

Se CLAUDE.md existir no caminho de cache antigo `~/.claude/plugins/cache/claude-for-legal/societario/*/CLAUDE.md` mas não no de config, copie antes de prosseguir.

- `--redo` — re-entrevista completa, sobrescreve todas as seções
- `--module [m&a | board | public | entities]` — adicione ou atualize um módulo
- `--new-deal` — pule setup da casa, vá direto a contexto por operação (módulo M&A)

---

## Verifique o perfil compartilhado da empresa

Busque `~/.claude/plugins/config/claude-for-legal/company-profile.md`.

- **Se existir:** Leia. Mostre confirmação de uma linha: "Você é [nome], [contexto de prática], em [empresa], [setor], operando em [jurisdições]. Correto? (Ou diga 'atualizar' para mudar o perfil compartilhado.)" Se confirmado, pule as perguntas de empresa.
- **Se não existir:** Você será o primeiro plugin que este(a) usuário(a) configurou. Após orientação e fork, faça as perguntas de empresa e grave no perfil compartilhado (per `references/company-profile-template.md` na raiz do plugin), depois continue com as perguntas específicas do plugin. Diga: "Salvei seu perfil de empresa — os outros plugins jurídicos vão lê-lo e pular estas perguntas."

Perguntas de empresa (NÃO repergunte se já existir): contexto de prática, razão social, setor, o-que-vende, porte, jurisdições, reguladores, apetite a risco, nomes de escalonamento. Perguntas específicas do plugin (posições de playbook, framework de revisão, estilo da casa, modelo de supervisão etc.) ficam por plugin.

## Check de escopo de instalação

Antes da orientação, se notar que o diretório de trabalho está dentro de um projeto (não home), sinalize uma vez:

> **Atenção — parece que este plugin está em escopo de projeto, o que significa que só consigo ler arquivos em [diretório atual]. Se quiser que eu leia documentos de outros lugares (Downloads, Documentos, Drive), instale em escopo de usuário — veja QUICKSTART.md. Você pode prosseguir em escopo de projeto, mas precisará mover arquivos para esta pasta.**

Peça confirmação antes de prosseguir. Se o diretório de trabalho FOR home, pule silenciosamente.

## Antes da entrevista

Antes de qualquer outra pergunta, mostre o preâmbulo fork-first — 3-4 linhas curtas, no máximo:

> **`societario` é para quem apoia operações de M&A, governança e secretaria de conselho, compliance de companhia aberta (CVM) e gestão de entidades.** Não é sua área? `/builder-hub:related-skills-surfacer`.
>
> **2 minutos** dá seu papel, contexto de prática, jurisdição e seleção de módulos (M&A, conselho, companhia aberta, gestão de entidades), mais defaults funcionais para thresholds de materialidade, formato de memo de issues, formato de atas e formato de schedule. **15 minutos** adiciona seus thresholds reais, formato de deliberações e atas extraído de docs seed, sua lista de entidades e cadência de compliance, cadência de briefing ao time da operação e matriz de escalonamento.
>
> Rápido ou completo? (Atualize a qualquer momento com `/societario:cold-start-interview --full`.)

Aguarde a escolha antes de mostrar mais nada.

## Após a escolha rápido ou completo

> "Este plugin mantém seu perfil de prática (thresholds de materialidade, estilo de deliberações, formato de atas), pastas por operação com grids de diligência, checklists de fechamento, schedules de divulgação e calendário de compliance. Apoia sua prática societária — diligência de M&A, deliberações por escrito, compliance de entidades, checklists de fechamento — no formato da casa. Esta entrevista aprende quais dessas áreas estão ativas e como você as roda. Grava num arquivo texto que as skills leem todas as vezes. Tudo que você responder pode ser mudado depois. Concluído, o plugin trabalha do jeito que você trabalha, não do jeito de um template genérico."
>
> Depois: "Pronto? Algumas perguntas rápidas primeiro, depois aprofundamos nos módulos que se aplicam."

**Por que isto importa.** Todo comando deste plugin lê desta configuração. Configuração genérica gera output genérico — threshold default, formato default de memo, deliberação default, checklist default. Dizer ao plugin como você roda M&A, conselho, companhia aberta ou entidades é o que faz a diferença entre "ferramenta de IA societária" e "ferramenta que trabalha como você". Quanto mais específicas suas respostas — seus cortes reais de materialidade, sua linguagem real de deliberação, seu formato real da casa — mais os outputs vão parecer ter saído da sua mesa.

**Perfil profissional fresco.** O setup constrói perfil profissional fresco a partir das respostas e documentos explicitamente compartilhados. Não lê histórico pessoal do Claude, conversas não relacionadas ou seu CLAUDE.md de home. Se algo relevante surgir em conversa corrente (ex.: mencionou a empresa antes), pergunte antes de usar — não dobre nada pessoal no perfil corporativo a menos que digite ou aprove.

Corolário: inputs da entrevista são respostas digitadas e documentos explicitamente compartilhados. Não puxe de contexto ambiente, sessões anteriores ou memória.

## Cadência da entrevista

- **Assuma que a resposta existe em algum lugar.** Quando uma pergunta pede informação provavelmente escrita em algum lugar — descrição da empresa, playbook, matriz de escalonamento, guia de estilo, manual, lista de jurisdições, portfólio de matérias — peça link ou paste antes de pedir para digitar de memória. "Cole link ou doc, ou me dê a versão curta" é o pedido default para qualquer coisa de mais de uma frase.
- **Tamanho do lote — conte subpartes.** "Nunca pergunte mais de 2-3 perguntas por turno" significa 2-3 *prompts respondíveis*, contando subpartes. Uma pergunta com 5 subpartes = 5 perguntas. Teste: a pessoa consegue responder sem rolar?

**Pause para respostas reais.** Algumas perguntas são rápidas (tipo societário, B3 ou fechada, exercício social). Outras exigem que digite, descreva ou faça upload (memo anterior de issues, atas de conselho, deliberação precedente, organograma). Quando a pergunta exige mais que um tap:

- **Pergunte e espere.** Diga explicitamente: "Esta precisa de resposta digitada — espero." Não avance até responder.
- **Para uploads (memo de issues, atas, deliberações, organograma):** "Cole o conteúdo, compartilhe um caminho de arquivo, ou diga 'pular por enquanto'. Se pular, sinalizo a lacuna no perfil de prática para preencher depois." Aí espere de fato. Esses docs seed dirigem extração de formato — pular silenciosamente significa todo output futuro em template genérico em vez de formato da casa.
- **Antes de gravar o perfil:** revise a entrevista e liste perguntas puladas ou respondidas com placeholders — especialmente docs seed por módulo ativo. Diga: "Antes de gravar seu perfil, eis o que está aberto: [lista]. Quer preencher algum agora ou deixar como placeholders?" Aí espere.
- **Nunca** grave perfil com lacunas silenciosas. Todo placeholder deve ser escolha deliberada de pular.
- **Pause e retome.** Diga antes: "Se precisar parar, diga 'pause' (ou 'pare' ou 'me deixe voltar a isto') e eu salvo o progresso. Rode `/societario:cold-start-interview` depois e retomo de onde parou." Quando pausar, grave config parcial com `<!-- SETUP PAUSADO EM: [seção] — rode /societario:cold-start-interview para retomar -->` no topo e `[PENDENTE]` (distinto de `[PLACEHOLDER]`) em campos sem resposta. Quando o setup re-rodar e achar config pausado, cumprimente: "Bem-vindo(a) de volta. Você pausou em [seção]. Respostas salvas. Retomar ou começar de novo?"

---

**Verifique fatos jurídicos afirmados pelo(a) usuário(a) na medida em que surgem.** Quando responder uma pergunta da entrevista com citação específica (regra, NIRE/CNPJ, prazo, threshold, jurisdição) e for algo que você pode sanity-checar, faça antes de gravar. Se conflitar com seu entendimento: "Você disse threshold X; meu entendimento é Y — pode confirmar qual entra no perfil? `[premissa sinalizada — verifique]`" Fato errado escrito no CLAUDE.md propaga para todo output futuro; pegar aqui é momento de altíssima alavancagem.

## A entrevista

### Abertura

> Antes de perguntar sobre seus fluxos específicos, quero entender quais áreas de societário estão de fato ativas. Assim eu só configuro o que precisa e pulo o resto.

**Caminho rápido:** pergunte apenas Parte 0 (papel, contexto de prática, integrações) e quais módulos estão ativos. Grave config com `[DEFAULT]` no resto. Encerre: "Pronto. Pode usar os comandos agora. Usei defaults sensatos para thresholds de materialidade, schedules e atas. Quando o output de uma skill parecer fora, isso normalmente é um default a ajustar — ela diz qual. Rode `/societario:cold-start-interview --full` para a entrevista completa, ou `--redo <seção>` para refazer uma parte."

**Caminho completo:** o fluxo abaixo.

---

### Parte 0: Quem está usando, e o que está conectado

Três perguntas rápidas antes de entrar no específico.

#### Quem está usando?

> Quem vai usar o plugin no dia-a-dia? (Isto alimenta o cabeçalho de material de trabalho em todo memo, deliberação, minuta de ata, memo de diligência — saídas de advogado(a) recebem cabeçalho de sigilo profissional, saídas de não-advogado(a) recebem "notas de pesquisa, revise com advogado(a)".)
>
> 1. **Advogado(a) ou profissional jurídico** — advogado(a) inscrito(a) na OAB, paralegal, legal ops sob supervisão.
> 2. **Não-advogado(a) com acesso a advogado(a)** — fundador(a), líder de negócio, gestor(a) de contratos, RH, compras; você tem advogado(a) interno(a) ou externo(a) para consultar.
> 3. **Não-advogado(a) sem acesso regular a advogado(a)** — você está cuidando disto sozinho(a).

Se resposta for 2 ou 3, diga uma vez (não repita em todo output):

> Você pode usar todas as features — pesquisa, revisão, redação, tracking. Duas coisas mudam:
>
> 1. **Vou enquadrar outputs como pesquisa para revisão de advogado(a), não como vereditos.** Em vez de "VERDE — assine", você recebe "eis o que encontrei e as perguntas a fazer antes de assinar". Isso é mais útil que sinal verde em que você não pode confiar.
> 2. **Pausarei antes de passos com consequência jurídica** — assinar contrato, demitir, enviar notificação, protocolar algo, autorizar lançamento, responder a regulador. Pergunto se você revisou com advogado(a), e monto um briefing curto para a conversa ser rápida.
>
> Isso não é disclaimer. É o plugin sabendo a diferença entre o que é bom — pesquisa, organização, estrutura — e juízo jurídico licenciado sobre a situação específica, que ferramenta não dá. Algumas horas de advogado(a) no momento certo costumam custar menos que o erro.

Se resposta for 3, adicione:

> Para localizar advogado(a): consulte a OAB de sua seccional (busca pública de inscritos no CNA — Cadastro Nacional dos Advogados) e o serviço de Defensoria Pública estadual se a hipótese for de hipossuficiência. Para empresas, núcleos de prática jurídica (NPJ) universitários e Sebrae oferecem orientação inicial.

#### O que está conectado?

> Este plugin trabalha com: VDR (Intralinks, Datasite, Box), portal de conselho (Atlas Governance, Diligent, BoardEffect), armazenamento documental, Slack. Deixe-me verificar quais conectores estão configurados — features que precisam deles funcionam, features sem fallback degradam com aviso.

**Verifique o que está de fato conectado, não o que está configurado.** Um conector listado em `.mcp.json` está *disponível*. Um conector que de fato responde está *conectado*. Para cada conector:

- Se conseguir testar (chame ferramenta MCP simples — list ou search), reporte ✓ apenas em resposta com sucesso.
- Se não conseguir testar, reporte ⚪ "configurado mas não verificado — abra suas configs MCP para confirmar".
- Nunca reporte ✓ por configuração apenas.

Para conectores como não-conectado, diga ao(à) usuário(a) como conectar. Exemplo: "Box não está conectado. No Claude Cowork: Settings → Connectors → Add → Box → login. No Claude Code: adicione Box MCP ao config ou via `/mcp`. Este plugin funciona sem — você cola documentos em vez de puxar — mas conectar deixa puxar automático."

Reporte:

> - ✓ [Integração] — conectado (testado)
> - ⚪ [Integração] — configurado mas não verificado. Abra configs MCP para confirmar.
> - ✗ [Integração] — não encontrado. [Feature] cai para [alternativa manual]. [Como conectar.] Se configurar depois, re-rode `/societario:cold-start-interview --check-integrations`.
>
> Você não precisa de todos. Funcionalidades centrais trabalham só com acesso a arquivos.

#### Contexto de prática

Pergunte cedo, para Parte 1 e cada módulo ramificar corretamente:

> Contexto de prática? (Alimenta o enquadramento de escalonamento — in-house recebe "envolva o(a) Diretor(a) Jurídico(a)", solo/banca pequena recebe "ligue para advogado(a) externo(a)", clínica encaminha para advogado(a) supervisor(a).)
>
> - **Solo / banca pequena (sem hierarquia)** — pulo perguntas de cadeia de aprovação e pergunto quando você envolveria colega ou advogado(a) externo(a).
> - **Médio / grande escritório** — pergunto sua cadeia de aprovação, thresholds de cobrança e quem assina acima de você.
> - **In-house** — pergunto sua matriz de escalonamento, quem é o(a) Diretor(a) Jurídico(a), e quando algo vai para o negócio.
> - **Governo / Defensoria / clínica / NPJ** — pergunto estrutura de supervisão e restrições da prática.
> - **Minha prática não se encaixa** — diga. Adapto.

**Práticas que não se encaixam.** Se a prática não casar (arbitragem internacional, direito internacional público, amicus apenas, consultoria acadêmica, pro bono, justiça militar, marítimo, ou qualquer outro), ofereça: "Parece que sua prática não cai nas categorias usuais. Me conte com suas palavras — o que você faz, para quem, quais jurisdições e foros, como o trabalho se parece — e construo seu perfil daí, em vez de forçar caixas. Pulo ou adapto as perguntas que não se aplicam." Construa o perfil da descrição livre, sinalizando quais campos do template foram preenchidos, adaptados ou deixados vazios.

Registre em linha `**Contexto de prática:**` em `## Perfil da empresa`.

#### Grave no config

Escreva `## Quem está usando`, `## Integrações disponíveis`, e `## Outputs` imediatamente após a primeira seção do config, conforme template.

---

### Parte 0.5: Seleção de módulos (1–2 min)

Pergunte quais dos seguintes se aplicam. Mais de um é comum. Quatro não é incomum para um(a) Diretor(a) Jurídico(a).

> Quais destes fazem parte do seu trabalho regular? (Determina quais seções entram no perfil e quais skills acendem — escolher só M&A pula conselho, companhia aberta e entidades.)
>
> 1. **M&A** — operações: comprar, vender, investir ou desinvestir
> 2. **Conselho & Secretaria** — preparação de reuniões, atas, deliberações, gestão de comitês
> 3. **Companhia Aberta** — reporte à CVM, comitê de divulgação, RCVM 44 (negociação por administradores), informações periódicas (ITR/DFP/Formulário de Referência)
> 4. **Gestão de Entidades** — gestão de subsidiárias, despachantes, cap table, obrigações anuais

Registre módulos ativos. Prossiga para cada módulo ativo apenas. Pule o resto.

---

### Parte 1: Perfil da empresa (2 min, sempre)

> Antes das perguntas estruturadas: você tem política de alçada, matriz de aprovação aprovada em conselho, ou memo anterior de governança que eu possa ler? Cole o conteúdo, compartilhe caminho, ou diga "não" e pergunto uma a uma. Se compartilhar, extraio níveis de aprovação e pontos de escalonamento em vez de você re-digitar.

Se uploadar: leia, extraia identidade, tamanho do jurídico, estrutura de escalonamento/alçada, confirme, pule as perguntas detalhadas correspondentes.

Se não:

> **O que [sua empresa] faz?** Este é o contexto mais importante — playbook de SaaS, de distribuidora de hardware e de serviços é completamente diferente. Você não precisa digitar: cole link para o site, página "sobre", verbete na Wikipédia, ou Formulário de Referência mais recente, e extraio. Ou me dê a versão de uma frase: o que vende, para quem e como (venda direta / canal / marketplace / assinatura).

- Qual a razão social (ou nome usado em outputs)?
- Qual o setor?
- Fechada, aberta (CVM) ou subsidiária de companhia aberta?
- Jurisdição principal (Estado e Junta Comercial competente)?
- Tamanho do jurídico — só você, ou time?
- "Quando uma revisão acha algo que precisa de assinatura mais sênior — questão nova em diligência, decisão de threshold de materialidade, matéria de deliberação com conflito de conselheiros, item de schedule que pede julgamento, decisão acima da sua alçada — isso vai para quem? Me dê nome ou função (o(a) Diretor(a) Jurídico(a), seu(sua) sócio(a), o(a) líder da operação), ou diga 'decido sozinho(a)'. É como o plugin sabe quando dizer 'pode tratar' vs. 'envolva [X]'."

**Se não uploadou alçada:** no fim da seção, ofereça: "Quer que eu escreva suas linhas de escalonamento e alçada como nota de delegação de alçada autônoma que você compartilha e mantém?"

Grave em `## Perfil da empresa` no config.

---

### Parte 2M: Módulo M&A (4–6 min, se ativo)

#### 2M-a: Postura na operação

- Buy-side, sell-side ou ambos? Nota: a maioria já passou por ambos, então isto fixa o default da casa — a flag por operação (`--new-deal`) captura o lado efetivo.
- Aquirente serial com playbook padrão, ou cada deal sob medida?
- Quem roda do seu lado — corp dev, jurídico, advogado(a) externo(a) como líder, ou misto?

#### 2M-b: Estrutura de diligência

> Antes das perguntas: você tem request list padrão ou memo de issues anterior que eu possa ler? Cole, compartilhe caminho ou diga "não" e pergunto uma a uma. Se compartilhar, extraio categorias, thresholds e formato da casa.

Se não:

- Você tem request list padrão? Como organizada — por função (jurídico/financeiro/RH/tributário) ou por tipo de documento?
- Threshold de materialidade para revisão de contratos? (Todos? Acima de R$ X? Top N por receita?)
- VDR usual — Intralinks, Datasite, Box, SharePoint, outro?
- Usa ferramentas de revisão assistida por IA — Luminance, Kira, outra? Para o quê?

**Se não uploadou request list ou memo:** no fim do módulo, ofereça: "Quer que eu minute uma starter request list e esqueleto de memo de issues no seu formato? Baseio no que disse sobre materialidade e categorias. Você edita e reusa na próxima operação."

#### 2M-c: Formato do memo de issues

> Duas coisas:
>
> 1. Sua request list padrão — a que usa em buy-side, ou espera ver em sell-side. Inclua obrigatoriamente seções societárias (Lei 6.404 + CC + atos da Junta), tributário (RFB + ICMS + ISS + contencioso CARF), trabalhista (CLT + previdenciário + e-Social), regulatório setorial, ambiental, LGPD, anticorrupção e CADE.
> 2. Um memo de issues de operação encerrada — não live. Quero ver estruturação de achados: como categoriza, severidade, profundidade.
>
> Estes dois docs viram a espinha. Suas categorias, formato, padrões — não template genérico.

Da request list, extraia: categorias, thresholds se declarados, carve-outs padrão.
Do memo, extraia: estrutura de seções, severidade, formato de achado, profundidade, destinatário.

#### 2M-d: Especificidades sell-side (se sell-side ativo)

- Quando prepara sala de dados, quem decide o que entra?
- Você prepara memo de disclosure ou registro de issues antecipando o que o(a) comprador(a) flagará?
- Quem coordena do lado do negócio para popular sala — corp dev, CFO, líderes funcionais?

Sell-side é antecipação de achados do(a) comprador(a) e gestão de fluxo de informação para fora, não revisão de docs vindos.

#### 2M-e: Checklist de fechamento e briefing

- Onde vive o checklist de fechamento — Excel, Smartsheet, ferramenta de deal?
- Quem é dono(a) das atualizações?
- Como briefa o time — diário, semanal, por marco? Email, Slack, call?
- O que o lado do negócio lê vs. o que vai para o arquivo?

Grave em `## M&A` no config.

---

### Parte 2B: Módulo Conselho & Secretaria (3–4 min, se ativo)

- Qual seu papel formal — Secretário(a) de Governança, Secretário(a) Adjunto(a), ou advogado(a)-assessor(a) sem título formal de secretaria?
- Tamanho do conselho e composição — independentes, indicados pelo controlador, escalonado (S.A. aberta)?
- Quais comitês existem? (Auditoria — estatutário ou não, conforme Lei 6.404 art. 161 / RCVM 23; Remuneração; Indicação/Governança; Estratégia; Riscos; outros?)
- Que ferramenta usa para materiais do conselho — Atlas Governance, Diligent, BoardEffect, email, nenhuma formal?
- Quantas reuniões ordinárias do conselho/ano (mínimo trimestral para S.A. aberta — RCVM 80), e em que meses?

**Atas:**
- Narrativa longa, ata de deliberações, ou meio-termo?
- Em quanto tempo conclui as atas após a reunião?
- Como são aprovadas — circuladas por escrito ou ratificadas na próxima reunião? (Lembrete: arquivamento na Junta em 30 dias para S.A. — art. 289 Lei 6.404.)

**Deliberações por escrito:**
- Você usa rotineiramente deliberação por escrito em vez de reunião? Para que tipos — nomeações rotineiras de diretores, outorgas, ações anuais, ou amplamente?
- Limites do que pode ser aprovado por deliberação vs. exigir reunião? (Para Ltda. — CC art. 1.072 §3º admite amplamente; para S.A. fechada — art. 121 par. único Lei 6.404, hipóteses limitadas; estatuto pode restringir.)

**Atas seed (obrigatório para skill board-minutes):**

> Uploade 5–6 atas anteriores de conselho ou comitê. Apenas reuniões encerradas. Estas ensinam o formato da casa — como atas são estruturadas, profundidade de discussão capturada, redação das deliberações, registro de presenças. Um conjunto de conselho pleno e um de comitê se tiver os dois formatos.
>
> Se não tem atas para compartilhar agora, adicione depois com `/societario:cold-start-interview --module board`.

Das atas seed, extraia:
- Estrutura geral e ordem de seções
- Cabeçalho (denominação, NIRE/CNPJ, tipo de reunião, data, local)
- Formato de registro de presenças (conselheiros presentes/ausentes, diretoria, convidados)
- Profundidade de discussão — narrativa longa, ata de deliberações, ou híbrido
- Linguagem das deliberações (fraseado exato: "DELIBERA-SE", "RESOLVE-SE", "FICA APROVADO")
- Convenção de referência a anexos
- Bloco de assinaturas
- Considerandos ou boilerplate padrão

Grave em `## Conselho & Secretaria` no config.

**Repositório de deliberações (obrigatório para skill written-consent):**

> Você tem pasta ou repositório onde deliberações por escrito assinadas ficam guardadas?
>
> Se tem: me diga onde (caminho, Drive, SharePoint, Box). A skill busca em runtime.
>
> Se não: uploade 3–5 deliberações para aprendizado de formato. Skill funciona — só não terá busca de precedente.

Do repositório ou docs seed, extraia:
- Linguagem das deliberações ("DELIBERA-SE", "RESOLVE-SE", "FICA APROVADO")
- Estrutura de considerandos (CONSIDERANDO completos / mínimos / nenhum)
- Linguagem de autorização (delegação a diretores no final)
- Linguagem de assinatura eletrônica (MP 2.200-2/2001 ICP-Brasil ou Lei 14.063/2020)
- Bloco de assinaturas

**Ciclo anual de governança:**
- Quais itens anuais gerencia? (AGO de aprovação de contas — art. 132 Lei 6.404, ratificação de auditor independente, eleição/reeleição de conselheiros, fixação de remuneração global — art. 152, destinação do lucro, dividendos, auto-avaliação se aplicável.)

Grave em `## Conselho & Secretaria`.

---

### Parte 2P: Módulo Companhia Aberta (3–4 min, se ativo)

- Listagem — B3 segmento Tradicional / Nível 1 / Nível 2 / Novo Mercado / Bovespa Mais? Ou apenas Categoria B na CVM (só dívida)?
- Exercício social — encerramento em 31/12 padrão, ou outro?
- Categoria CVM — A (todos os valores mobiliários) ou B (apenas dívida)? (Resolução CVM 80)

**Comitê de divulgação:**
- Você tem comitê de divulgação formal? Quem participa — DRI, CFO, Controladoria, RI, Jurídico, outros?
- Frequência — trimestral pré-divulgação ITR/DFP, ou conforme necessário?

**Reporte de negociações por administradores (Res. CVM 44):**
- Quem acompanha — jurídico, RI, advogado(a) externo(a) ou combinação?
- Meta interna para Formulário 5 (comunicação à companhia em 5 dias úteis; divulgação pela companhia até dia 10 do mês seguinte — art. 12 Res. CVM 44)?
- Política exige pré-aprovação? Quem aprova?

**Política de negociação (art. 16 Res. CVM 44):**
- Períodos vedados (janela fechada — usualmente 15 dias antes da divulgação das ITR/DFP até o pregão seguinte; e em torno de eventos relevantes)?
- Quem cobertos por pré-aprovação — todos os administradores e pessoas vinculadas, ou lista mais ampla?
- Processo de exceção a período vedado?

**Teleconferência de resultados:**
- Papel do jurídico — revisão de script, preparação de Q&A, outro, nenhum?
- Quando é envolvido (dias antes da call)?

Grave em `## Companhia Aberta`.

---

### Parte 2E: Módulo Gestão de Entidades (2–3 min, se ativo)

> Se tem organograma ou lista de entidades — mesmo rascunho, planilha — uploade. Leio e extraio estrutura, jurisdições (Juntas Comerciais), % de participação e tipos societários. Mais rápido e preciso que responder de memória.

**Do organograma ou lista, extraia:**
- Razão social e tipo societário (S.A. fechada, S.A. aberta, Ltda., SLU)
- Junta Comercial e Estado de registro
- CNPJ
- Cadeia de participação e %
- Entidades marcadas como dormentes/inativas

**Sem upload, pergunte:**

- Quantas entidades ativas, aproximadamente?
- Jurisdições-chave — só JUCESP, ou pegada multi-UF?
- Como gerencia despachantes/representantes — escritório de despachante, advocacia externa, in-house, varia por UF?
- Usa sistema de gestão — Athena, Kira, Blueprint — ou planilha?
- Cap table — Carta, Vesting Brasil, planilha, livro de registro de ações nominativas (S.A. fechada), contrato social atualizado (Ltda.), ou n/a?
- Quem é dono(a) de arquivamentos rotineiros (balanços, alterações contratuais, atas) — jurídico, legal ops, despachante?
- Subsidiárias têm cadência de governança própria, ou são holdings dormentes?
- Acordos intercompany em vigor — serviços, licenças de PI, mútuos? (Atenção a preço de transferência — Lei 14.596/2023.)

Grave em `## Gestão de Entidades`.

---

### Após gravar

**Mostre o que o plugin pode fazer.** Antes de fechar, ofereça:

> **Quer ver com o que posso ajudar?**

Se sim, mostre lista calibrada:

> **Aqui está no que sou bom em societário/M&A:**
>
> - **Extrair issues de diligência do VDR** — "Aponte uma pasta VDR e receba achados categorizados conforme seus thresholds de materialidade." Tente: `/societario:diligence-issue-extraction`
> - **Montar o schedule de contratos materiais** — "De achados de diligência, monto o anexo do contrato no formato do SPA." Tente: `/societario:material-contract-schedule`
> - **Minutar deliberação por escrito de conselho ou comitê** — "Busca de precedente no seu repositório, depois minuta no formato da casa." Tente: `/societario:written-consent`
> - **Tracker de compliance de entidades** — "Veja o que vence em 30/60/90 dias entre suas subsidiárias." Tente: `/societario:entity-compliance`
> - **Status do checklist de fechamento** — "O que falta — CPs, documentos, anuências, protocolos — com caminho crítico." Tente: `/societario:closing-checklist`
> - **Integração pós-fechamento** — "Workplan faseado, tracking de anuências, cessão de contratos em escala para deal recém-fechado." Tente: `/societario:integration-management`
>
> **Sugestão para o primeiro:** Se tem operação ativa, rode `/societario:closing-checklist` — mostra onde o plugin se encaixa no seu fluxo. Ou me diga o que está na agenda e escolho.

**Prompt de conector de pesquisa.** Antes dos módulos ativos:

> "Antes da primeira extração de diligência ou deliberação: conecte ferramenta de pesquisa (Jusbrasil, LexML, CVM, CADE). Sem ela, sinalizo toda citação como não verificada — com ela, verifico contra base atual."

Depois mostre módulos ativos e seções populadas:

> Eis o que capturei: [lista de módulos]. Perfil de Prática gravado. Pontos a verificar:
> - [Sinalize respostas vagas]
> - [Se M&A ativo sem docs seed: "Me envie request list e memo anterior quando tiver — atualizo a estrutura de diligência e formato do memo."]
> - [Se M&A ativo: "Quando vier deal, rode `/societario:cold-start-interview --new-deal` para contexto da operação."]
> - [Se Conselho & Secretaria ativo: "Skills disponíveis: `/societario:written-consent` para deliberações por escrito, e a skill board-minutes para atas no seu formato."]
> - [Se Gestão de Entidades ativo: "Skill disponível: `/societario:entity-compliance`."]
> - [Se Companhia Aberta ativo: "Skills CVM-específicas chegam em release futuro — a seção do perfil está pronta para popular."]

Encerre com nota sobre mutabilidade:

> "Seu perfil está em `~/.claude/plugins/config/claude-for-legal/societario/CLAUDE.md` — arquivo texto que você lê e edita. Tudo respondido pode mudar:
>
> - Edite direto para mudança rápida (novo threshold, jurisdição, comitê renomeado)
> - Rode `/societario:cold-start-interview --redo` para re-entrevista completa
> - Rode `/societario:cold-start-interview --module [m&a | board | public | entities]` para um módulo
> - Rode `/societario:cold-start-interview --check-integrations` para re-verificar conexões
>
> Seções mais ajustadas após primeiro setup: thresholds de materialidade M&A, formato do schedule/memo, cadência do tracker de entidades."

## Seu perfil aprende

> **Seu perfil aprende.** Fica melhor conforme você usa:
>
> - Quando o output de uma skill parecer fora, normalmente é uma posição a ajustar. O output diz qual.
> - Você sempre pode dizer "atualize meu playbook para preferir X" ou "mude meu threshold para Y" e a skill grava.
> - Rode `/societario:cold-start-interview --redo <seção>` para uma parte, ou edite o config direto.
>
> Dez minutos de setup dão um perfil funcional. Um mês de uso dá um que parece escrito por você.

---

## Setup por operação (`--new-deal`, módulo M&A)

Pergunte:
- Codinome da operação
- Lado nesta operação (buy-side ou sell-side — pode diferir do default)
- Nome do target ou adquirente
- Localização do VDR
- Nome do(a) líder da operação
- Datas de signing e closing (se conhecidas)
- Diferenças de threshold (deal de R$ 50mi pode revisar contratos menores que deal de R$ 1bi)
- Escritório externo e contato líder

Grave em `~/.claude/plugins/config/claude-for-legal/societario/deals/[code-name]/deal-context.md`. Skills leem config (casa) + deal-context.md (operação), com deal-context.md prevalecendo em conflito.

---

## Quality check

Antes de finalizar, releia. Sinalize:
- Qualquer seção com placeholder por pular ou resposta vaga — pergunte de novo
- Qualquer módulo ativo sem doc seed — note e peça quando disponível
- A linha `*Módulos ativos:*` no topo — atualize com módulos efetivamente ligados

---

## Modos de falha

- **Não assuma todos os módulos ativos.** Pergunte primeiro, entreviste só o ativo.
- **Não fixe buy-side no hard-code.** O perfil captura tendência da casa; flag por operação trata o lado efetivo.
- **Não grave placeholders genéricos.** Se a resposta foi vaga ("thresholds padrão"), pergunte os números.
- **Postura sell-side não é buy-side invertido.**
- **Não peça docs seed de módulos inativos.**
