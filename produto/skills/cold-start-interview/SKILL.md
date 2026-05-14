---
name: cold-start-interview
description: >
  Entrevista de cold-start — conecta ao seu launch tracker, lê revisões passadas
  e aprende sua calibração de risco. Use em instalação nova, ao onboardar product
  counsel, ou quando o config do plugin tem placeholders. Rode com --redo para
  re-entrevistar, ou --check-integrations para reprobar só conectores.
argument-hint: "[--redo] [--check-integrations para reprobar integrações apenas]"
---

# /cold-start-interview

> **Adaptação BR.** Calibrado para regimes brasileiros (LGPD, Marco Civil, CDC, ECA, LBI, CONAR, regulação setorial BCB/CVM/ANVISA/ANATEL). Veja `references/terminology-us-to-br.md`.

1. Check `~/.claude/plugins/config/claude-for-legal/produto/CLAUDE.md` state.
2. Run the cold-start interview below.
3. Seed docs: 10 past launch review docs (from tracker or Drive). Read them all.
4. Build risk calibration table from what actually blocked vs. shipped.
5. Migration: if a populated CLAUDE.md (no `[PLACEHOLDER]` markers) exists at `~/.claude/plugins/cache/claude-for-legal/produto/*/CLAUDE.md` but not at the config path, copy it to the config path and show the user what was migrated.
6. Write `~/.claude/plugins/config/claude-for-legal/produto/CLAUDE.md` (create parent directories as needed). Show calibration table for confirmation.

## `--check-integrations`

Re-runs the integration availability check (launch tracker, document storage, Slack) and updates `## Available integrations` in `~/.claude/plugins/config/claude-for-legal/produto/CLAUDE.md`. Does not re-interview. Use when you connect or disconnect an MCP and want the plugin to notice without rerunning the full setup.

When probing: only report ✓ if an MCP tool call actually succeeded. Configured-but-untested connectors should be marked ⚪ with a one-line how-to for confirming. Never report ✓ based on `.mcp.json` declarations alone — that misleads users into thinking something is wired up when it isn't.

```
/produto:cold-start-interview
```

```
/produto:cold-start-interview --check-integrations
```

---

# Cold-Start Interview: Product Counsel

## Propósito

Product counsel é específico de cada empresa de um jeito que outras práticas não são. O que conta como bloqueador de lançamento numa fintech (regulada pelo BCB) é FYI numa ad-tech. A mesma feature é alto-risco numa empresa sob TAC com SENACON e rotineira numa que a ANPD nunca ouviu falar.

Esta entrevista aprende a calibração de risco *da sua* empresa lendo seus docs de launch review reais — onde você bloqueou, onde liberou e no que gastou tempo.

## Cold-start check

Read `~/.claude/plugins/config/claude-for-legal/produto/CLAUDE.md`:
- **Does not exist** → start the interview.
- **Contains `<!-- SETUP PAUSED AT: -->`** → greet the user and offer to resume from that section.
- **Contains `[PLACEHOLDER]` markers but no pause comment** → the template was never completed; offer to start fresh or resume from wherever the placeholders begin.
- **Populated (no placeholders, no pause comment)** → already configured; skip unless `--redo`.

The template structure lives at `${CLAUDE_PLUGIN_ROOT}/CLAUDE.md` — use it as the section scaffold. Write the completed practice profile to the config path, creating parent directories as needed.

If a CLAUDE.md exists at the old cache path `~/.claude/plugins/cache/claude-for-legal/produto/*/CLAUDE.md` but not at the config path, copy it forward.

## Check for the shared company profile

Look for `~/.claude/plugins/config/claude-for-legal/company-profile.md`.

- **If it exists:** Read it. Show a one-line confirmation: "You're [name], [practice setting], at [company], [industry], operating in [jurisdictions]. Right? (Or say 'update' to change the shared profile.)" If confirmed, skip the company questions — go straight to the plugin-specific ones.
- **If it doesn't exist:** You'll be the first plugin this user set up. After the orientation and fork, ask the company questions and write them to the shared profile (per the template at `references/company-profile-template.md` in the plugin root), then continue with the plugin-specific questions. Tell the user: "I've saved your company profile — the other legal plugins will read it and skip these questions."

The company questions that belong in the shared profile (and should NOT be re-asked if it exists): practice setting, company name, industry, what-you-sell, size, jurisdictions, regulators, risk appetite, escalation names. The plugin-specific questions (playbook positions, review framework, house style, supervision model, etc.) stay per-plugin.

## Check de escopo de instalação

Antes da orientação, se notar que o diretório de trabalho está dentro de um projeto (não no home do usuário), sinalize. Diga uma vez:

> **Atenção — parece que este plugin pode estar em escopo de projeto, ou seja, só consigo ler arquivos em [diretório atual]. Se você quer que eu leia docs em outros lugares (Downloads, Documents, Dropbox), instale em escopo de usuário — veja QUICKSTART.md. Você pode continuar com escopo de projeto, mas vai precisar mover arquivos para esta pasta.**

Peça confirmação antes de seguir: continuar com escopo de projeto, ou pausar para reinstalar em escopo de usuário. Se o diretório de trabalho *é* o home do usuário, pule esta checagem em silêncio.

## Antes da entrevista começar

Antes de perguntar qualquer outra coisa, mostre o preâmbulo de bifurcação — 3-4 linhas curtas, não mais:

> **`produto` é para quem revisa lançamentos de produto, claims de marketing e risco de feature — o lado jurídico de lançar.** Não é sua área? `/builder-hub:related-skills-surfacer`.
>
> **2 minutos** te dão seu papel, nível do framework de revisão (gate formal vs. consultivo) e contexto de produto/prática (consumidor, B2B, ambos), com defaults sensatos em todo o resto. **15 minutos** adiciona sua tabela de calibração de risco (o que bloqueia vs. o que vai ao ar aqui), sua matriz de escalação, suas categorias de framework, seu formato de memo da casa e sua integração de launch tracker.
>
> Rápido ou completo? (Atualize a qualquer momento com `/cold-start-interview --full`.)

Wait for the user's pick before showing anything else.

<!-- COLLATERAL LINKS: when onboarding collateral exists, prepend a line above the preamble:
     "Want a walkthrough first? [Watch the 3-minute intro](URL) or [read the getting-started guide](URL), then come back and run /cold-start-interview." -->

## Depois de o usuário escolher rápido ou completo

Uma vez escolhido, oriente antes da primeira pergunta:

> "Este plugin mantém seu practice profile (framework de revisão, calibração de risco, matriz de escalação), um arquivo de launch reviews e um log de claims de marketing. Atua como product counsel — launch reviews, feature risk assessments, checks de claim — contra a calibração de risco da empresa e o framework da casa. Esta entrevista de setup aprende como você efetivamente trabalha — sua calibração, o que sua empresa trata como P0 vs. FYI, seu framework de revisão, suas convenções de casa — e escreve num arquivo de texto plano que o plugin lê toda vez. Tudo que você responde pode mudar depois. Quando estiver pronto, os comandos do plugin vão trabalhar do jeito que você trabalha, não como template genérico."
>
> Depois: "Setup constrói um profile profissional novo a partir de suas respostas. Não lê seu histórico pessoal do Claude, outras conversas, nem CLAUDE.md de home. Se algo relevante apareceu antes nesta conversa (por exemplo, você mencionou sua empresa), eu pergunto antes de usar. Nada vai pra sua configuração a menos que você digite ou aprove."
>
> Depois: "Pronto? Algumas perguntas rápidas primeiro, depois aprofundamos."

**Por que isto importa.** Todo comando deste plugin lê da configuração que esta entrevista escreve. Configuração genérica dá output genérico — calibração default, framework default, matriz de escalação default, e launch review que trata sua empresa como qualquer outra. Dizer ao plugin como sua empresa de fato calibra risco — o que é P0 bloqueante aqui versus FYI — é a diferença entre "uma ferramenta de produto com IA" e "uma ferramenta que conhece o framework da sua casa". Quanto mais específicas as respostas, mais o output vai parecer seu.

Não leia o `~/CLAUDE.md`, `~/user.md` do home do usuário, nem outra memória pessoal para pré-popular a entrevista. Os únicos inputs são as respostas digitadas pelo usuário e documentos que ele apontar ou colar.

**Caminho quick start:** pergunte só a Parte 0 (papel, cenário de prática, integrações) e área de produto. Escreva o config com marcadores `[DEFAULT]` em todo o resto. Encerre com: "Pronto. Já pode usar os comandos. Usei defaults sensatos para framework de launch review, calibração de risco e postura de claims de marketing. Quando o output de uma skill soar errado, geralmente é um default a tunar — vai te dizer qual. Rode `/produto:cold-start-interview --full` a qualquer momento para fazer a entrevista inteira, ou `/produto:cold-start-interview --redo <seção>` para refazer uma parte."

**Caminho de setup completo:** o fluxo abaixo.

## Pacing da entrevista

- **Presuma que a resposta existe em algum lugar.** Quando a pergunta pede informação que provavelmente está escrita — descrição da empresa, playbook, matriz de escalação, manual da casa, lista de jurisdições, portfólio de matérias — peça link ou colagem antes de o usuário digitar de memória. "Cola um link ou um doc, ou me dá a versão curta" é o pedido default para qualquer coisa que passa de uma frase. Entrevistador que faz a pessoa redigitar o que ela já escreveu falhou no primeiro trabalho do entrevistador.
- **Tamanho do batch — conte subpartes.** "Nunca mais que 2-3 perguntas por turno" significa 2-3 *prompts respondíveis*, contando subpartes. Uma pergunta com 5 subpartes são 5 perguntas. Teste: o usuário consegue responder sem rolar? Se as perguntas não couberem em uma tela, é demais. Prefira perguntas estruturadas de tocar — não exigem rolar ou digitar.

**Pause para respostas reais.** Algumas têm tap-through rápido. Outras pedem digitação, descrição ou upload. Quando a pergunta exige mais que um toque:

- **Pergunte e aguarde.** Diga claro: "Esta exige resposta digitada — vou esperar." Não enfileire a próxima até a resposta vir.
- **Para uploads (seeds de launch review, PRDs, links de tracker):** "Cole o conteúdo, compartilhe um caminho, ou diga 'pular por ora'. Se pular, sinalizo a lacuna na configuração para você preencher depois." Aí espere de fato.
- **Antes de escrever o practice profile:** revise a entrevista. Liste toda pergunta pulada ou respondida com placeholder. Diga: "Antes de eu escrever sua configuração, eis o que ficou em aberto: [lista]. Quer preencher agora, ou deixar como placeholders?" Aguarde antes de escrever.
- **Nunca** escreva o practice profile com lacunas silenciosas. Todo placeholder deve ser escolha deliberada de pular, não pergunta que rolou sem resposta.
- **Pause e retome.** Diga ao usuário no começo: "Se precisar parar, diga 'pause' (ou 'pare', ou 'deixa eu voltar nisso depois') e eu salvo seu progresso. Rode `/produto:cold-start-interview` de novo depois e eu pego de onde paramos." Quando o usuário pausa, escreva configuração parcial em `~/.claude/plugins/config/claude-for-legal/produto/CLAUDE.md` com comentário `<!-- SETUP PAUSED AT: [seção] — rode /produto:cold-start-interview para retomar -->` no topo e marcadores `[PENDING]` (distintos de `[PLACEHOLDER]`) nos campos em aberto. Quando o setup rerodar e achar config pausado, cumprimente: "Bem-vindo de volta. Você pausou em [seção]. Suas respostas anteriores estão salvas. Continuar de onde paramos, ou começar de novo?" Não reperguntar o respondido.

**Verifique fatos jurídicos afirmados pelo usuário conforme aparecem no setup.** Quando o usuário responde com citação de regra específica, número de lei, nome de caso, prazo, threshold, jurisdição ou número de registro — e é algo que você pode sanity-check — faça antes de escrever no config. Se o que ele disse conflita com seu entendimento ou com algo que ele colou, traga à tona: "Você disse que o prazo do CDC é 30 dias para produto durável; meu entendimento é que o CDC art. 26 II traz **90 dias para produtos duráveis** — pode confirmar qual entra no profile? `[premissa sinalizada — verificar]`" Fato errado escrito no CLAUDE.md se propaga em todo output futuro; pegá-lo aqui é um dos momentos de maior alavancagem do produto.

## A entrevista

### Abertura

> Product counsel é a prática em que o jurídico é mais próximo da empresa — muda mais de lugar para lugar. Preciso aprender o que "arriscado" significa aqui antes de te dizer se algo é arriscado.
>
> Vou perguntar sobre sua empresa, seu processo de revisão e o que você já bloqueou. Depois quero ler dez de suas launch reviews passadas. Não os PRDs — *suas* revisões. É onde sua calibração mora.

### Parte 0: Quem está usando, e o que está conectado

Duas perguntas rápidas antes de entrarmos no específico de produto. Moldam como o plugin funciona, não o que ele faz.

#### Quem está usando?

> Quem vai usar este plugin no dia a dia? (Isto alimenta o cabeçalho de material de trabalho de toda skill e o framing de output — advogado(a) recebe "MATERIAL DE TRABALHO — PRIVILEGIADO E CONFIDENCIAL (Art. 7º, XIX, EOAB)", não-advogado recebe framing de pesquisa e checkpoints de revisão por advogado(a) antes de um lançamento ser liberado.)
>
> 1. **Advogado(a) ou profissional jurídico** — advogado(a) inscrito(a) na OAB, estagiário(a) (Provimento CFOAB 166/2015), paralegal, produto ops sob supervisão de advogado(a).
> 2. **Não-advogado com acesso a advogado(a)** — PM, fundador(a), líder de negócio, marketing ops; você tem advogado(a) in-house ou externo(a) para consultar.
> 3. **Não-advogado sem acesso regular a advogado(a)** — você está cuidando disto sozinho(a).

Se a resposta é 2 ou 3, diga uma vez (não repita em todo output):

> Você pode usar toda feature aqui — launch review, feature risk assessment, revisão de claims de marketing e triagem. Duas coisas mudam no jeito que eu trabalho:
>
> 1. **Vou enquadrar outputs como pesquisa para revisão por advogado(a), não como vereditos.** Em vez de "liberado para lançar", você vai receber "eis o que achei e eis as perguntas a fazer antes de lançar". É mais útil que um sinal verde do qual você não pode ter certeza.
> 2. **Vou pausar antes de passos com consequência jurídica** — liberar um lançamento, publicar claim de marketing, aprovar claim para uso externo. Vou perguntar se você revisou com advogado(a), e vou montar um brief curto para que a conversa com ele(a) seja rápida.
>
> Isto não é disclaimer. É o plugin sabendo a diferença entre o que ele faz bem — pesquisa, organização, estrutura — e juízo jurídico de advogado(a) habilitado(a) sobre sua situação específica, que ferramenta não dá. Algumas horas de advogado(a) no momento certo costuma sair mais barato que o erro.

Se a resposta é 3, adicione:

> Para achar advogado(a): no Brasil, o caminho mais rápido é a **OAB da sua seccional estadual** — todas têm serviço de orientação e indicação. Alternativas para quem não pode pagar: **Defensoria Pública** (esfera estadual ou federal, conforme o caso; CF arts. 134 e ss.); **Núcleos de Prática Jurídica (NPJ)** de faculdades de Direito (Resolução CNE/CES 5/2018), que prestam assistência supervisionada gratuita; programas de **pro bono** da OAB (Provimento CFOAB 166/2015). Para questões empresariais de pequeno porte, comece pela seccional OAB e procure por câmaras de mediação (CAM-CCBC, CAM B3, FGV, CBMA) se for disputa.

#### O que está conectado?

> Este plugin pode trabalhar com: launch tracker (Jira, Linear, Asana), storage documental (Google Drive, SharePoint) e Slack. Deixa eu checar quais conectores você tem configurados — features que precisam vão funcionar, e features sem eles caem para manual com graça em vez de falhar em silêncio.

**Cheque o que está efetivamente conectado, não o que está configurado.** Conector listado em `.mcp.json` está *disponível*. Conector que efetivamente responde está *conectado*. São coisas diferentes, e confundi-las destrói confiança. Para cada conector que este plugin usa:

- Se você consegue testar a conexão (chamar uma MCP simples como list ou search), reporte ✓ só com resposta bem-sucedida.
- Se não consegue testar (sem jeito de probar daqui), reporte ⚪ "configurado mas não verificado — abra suas configurações MCP para confirmar" com uma linha de como-fazer.
- Nunca reporte ✓ baseado só em configuração.

Para conectores não conectados, diga ao usuário como conectar. Exemplo: "Jira não está conectado. No Claude Cowork: Settings → Connectors → Add → Jira → sign in. No Claude Code: adicione a MCP do Jira ao seu config ou via `/mcp`. Este plugin funciona sem — você cola PRDs e docs de revisão direto — mas conectar deixa o agente launch-watcher puxar tickets automaticamente."

Depois reporte os findings nesta forma:

> - ✓ [Integração] — conectada (testada)
> - ⚪ [Integração] — configurada mas não verificada. Abra suas configurações MCP para confirmar.
> - ✗ [Integração] — não encontrada. [Feature] vai cair para [alternativa manual]. [Como conectar.]

Você não precisa de todos. Features core funcionam só com acesso a arquivo. Se configurar algo depois, rerode `/produto:cold-start-interview --check-integrations`.

#### Registre no config do plugin

Escreva as seções `## Quem está usando isto` e `## Integrações disponíveis` imediatamente após `## Quem somos`, e atualize `## Outputs` para que o cabeçalho de material de trabalho seja condicional ao papel (veja o template do practice profile abaixo).

#### Cenário de prática

> Mais uma rápida antes de aprofundarmos:
>
> Qual o cenário? (Isto alimenta a matriz de escalação que toda skill usa — in-house pergunta sobre roteamento ao GC; solo mapeia "escalar" para "consultar advogado(a) externo(a)"; NPJ roteia para o(a) advogado(a) supervisor(a).)
>
> - **Solo / banca pequena (sem hierarquia)** — vou pular perguntas de cadeia de aprovação e perguntar quando você chamaria um(a) colega ou outside counsel.
> - **Banca média / grande** — vou perguntar sobre sua cadeia de aprovação, thresholds de billing e quem assina acima de você.
> - **In-house** — vou perguntar sua matriz de escalação, quem é o(a) GC/CLO/Diretor(a) Jurídico(a) e quando algo vai para o negócio.
> - **Setor público / Defensoria / NPJ** — vou perguntar sobre estrutura de supervisão e quaisquer restrições à sua prática.
> - **Minha prática não bate com nenhum desses** — diga. Eu me adapto.

**Práticas que não se encaixam nas caixas.** Se a prática do usuário não bate com as opções acima (arbitragem internacional, direito público, amicus-only, consultoria acadêmica, pro bono, justiça militar, marítimo, ou qualquer outra que as categorias-padrão pressupõem fora), ofereça: "Parece que sua prática não bate com minhas categorias usuais. Me conta com suas palavras — o que faz, para quem, em que jurisdições e foros, como o trabalho parece — e construo seu profile a partir disso em vez de te forçar em caixas que não cabem. Vou pular ou adaptar as perguntas que não se aplicam." Aí construa o profile da descrição livre, sinalizando quais campos do template foram preenchidos, adaptados ou deixados vazios por não se aplicarem. Profile construído com encaixe forçado é pior que profile esparso construído do que de fato é verdade.

Use isto para bifurcar perguntas posteriores:

- **Solo / banca pequena (sem hierarquia):** Pule perguntas de cadeia de escalação na Parte 1 e demais. Reformule: em vez de "quem aprova acima do seu threshold", pergunte "quando você chama outside counsel ou colega para segunda opinião". No practice profile, a matriz de escalação mapeia para *consultar*, não *rotear para aprovação*, e a pergunta "o que o GC pergunta em toda revisão" vira "o que você sempre confere duas vezes antes de lançar".
- **Banca média / grande:** Pergunte cadeia de aprovação, thresholds de billing e quem assina acima do usuário.
- **In-house:** Pergunte matriz de escalação, quem é o(a) GC/CLO/Diretor(a) Jurídico(a), e quando algo vai para o negócio.
- **Setor público / Defensoria / NPJ:** Substitua a cadeia de supervisão usada nesse cenário (advogado(a) supervisor(a), Defensor(a) Público(a)-Geral, coordenação acadêmica/NPJ — Resolução CNE/CES 5/2018). Pergunte restrições à prática. Mantenha estrutura de escalação mas re-rotule papéis.

Registre o cenário de prática no practice profile em `## Quem está usando isto`.

### Parte 1: A empresa (3-4 min)

**O que [sua empresa] faz?** Este é o contexto único mais importante — playbook de vendor SaaS, playbook de distribuidor de hardware e playbook de empresa de serviços são totalmente diferentes. Você não precisa digitar: cole link para o site da empresa, página "sobre", verbete na Wikipedia, ou seu DFP / ITR (CVM) mais recente, e eu extraio o que preciso. Ou me dê a versão de uma frase: o que vende, para quem, e como (venda direta / canal / marketplace / assinatura).

**O que somos?**
- O que a empresa faz?
- Quem usa?
- A empresa é consumer, B2B, ou ambos? *(B2B sem hipossuficiência geralmente fora do CDC — Súmulas 297, 381, 643 STJ)*
- Está em indústria regulada?
- Se sim, qual(is) regime(s)? *(BCB/Open Finance/PIX para fintech; CVM/Lei 14.478/2022 para cripto; ANVISA para healthtech; ANATEL para telecom; SUSEP para insurtech; ANEEL para energia; ANTT para transporte; MEC para educação formal)*
- Há reguladores com quem vocês têm canal direto?
- Algum TAC ativo (SENACON, ANPD, CADE)?
- Alguma investigação ou processo administrativo em curso?
- O produto é internacional?
- Se sim, quais países importam mais para calibração? *(LGPD vs. GDPR; transferência internacional precisa de mecanismo — Res. ANPD 19/2024)*

**Estágio da empresa e postura de funding:**
- Em que estágio — pré-seed, Series A-D, pré-IPO, capital aberto, controle PE, outro?
- Overlays de risco direcionados por investidor (reporte ao conselho, restrições D&O, gating de disclosure de companhia aberta — Resoluções CVM 80 e 44) que afetam como você calibra risco?

**Footprint de jurisdição (mesmo aproximado):**
- Onde estão os usuários — só Brasil, Brasil + LATAM, global?
- Onde estão empregados e data centers? *(LGPD art. 33 — transferência internacional)*
- Algum mercado que dirige parte desproporcional da calibração (ex.: exposição EU pesada — GDPR; presença EUA — CCPA e setoriais; algum país com regulador específico em diálogo)?

**Apetite de risco:** *(Isto alimenta `/launch-review` e `/is-this-a-problem` — define o que conta como bloqueador P0 na sua empresa vs. FYI.)*
- Numa escala "conservador / médio / agressivo", onde a liderança fica em risco de lançamento? Alguma categoria específica diferente (ex.: agressivo em experimentos de pricing, conservador em qualquer coisa que toca criança — ECA/CONANDA)?
- Há postura "vamos rápido e defendemos depois" ou "vamos acertar antes de lançar" — e varia por área de produto?

**O que tira o seu sono?** *(Isto alimenta `/launch-review` — as perguntas que o GC sempre faz viram checks obrigatórios em todo memo de lançamento.)*
- Se algo desse errado num lançamento, qual o pior caso realista? (Não "alguém nos processa" — quem, por quê, e pegaria?)
- O que seu GC pergunta em toda launch review?

**Escalação — quem assina acima de você?** *(Isto alimenta o roteamento de toda skill — `/launch-review`, `/is-this-a-problem` e `/marketing-claims-review` sabem quando dizer "você pode resolver" vs. "envolver [X]".)*

> "Quando uma revisão encontra algo que precisa de alguém mais sênior assinando — risco de lançamento acima da sua calibração de política, claim de marketing que precisa de escrutínio, issue inédita que você nunca viu, ou decisão acima da sua autoridade — para onde vai? Me dá um nome ou papel (o GC, sua chefia, o(a) head de product counsel, sócio(a) responsável), ou diga 'eu decido sozinho(a)'. É assim que o plugin sabe quando dizer 'você pode resolver' versus 'envolver [X]'."

### Parte 2: O processo de revisão (3-4 min)

Antes das perguntas estruturadas: "Você tem um framework de launch review existente, uma tabela de calibração de risco ou memos de revisão anteriores que pode compartilhar? Cole o conteúdo ou um caminho de arquivo, e eu extraio as categorias, os cortes P0/FYI e o formato da casa em vez de te fazer redigitar. Se não, diga 'não' e eu pergunto uma de cada vez."

Se o usuário enviar: leia, extraia o framework, confirme o que achou e pule as perguntas detalhadas correspondentes.

**Como os lançamentos chegam até você?**
- Launch tracker — Jira? Linear? Asana? Uma planilha?
- Os PMs sabem te envolver, ou você descobre pelo calendário?
- Quanto lead time você costuma ter? É suficiente?

**Qual seu framework?** *(Isto alimenta `/launch-review` — as categorias que você checa aqui viram títulos de seção em todo memo.)*
- Você tem categorias contra as quais checa todo lançamento? (Contratual, privacidade/LGPD, segurança, PI/INPI, terceiros, regulatório setorial, marketing/CDC+CONAR, consumidor/CDC, acessibilidade/LBI, infanto-juvenil/ECA, Marco Civil, IA, etc.)
- Sign-off formal, ou consultivo?
- Qual o output — memo, comentário em ticket, thread de Slack?

**P0 vs. FYI — esta é a pergunta-chave:**
- Um exemplo de algo que você bloqueou?
- Um exemplo de algo que parecia assustador mas você disse "pode lançar"?
- O que PMs continuam perguntando e quase nunca é problema?

**Se o usuário não enviou framework ou revisões passadas:** ao final desta seção, ofereça: "Quer que eu escreva isto como um framework standalone que você pode compartilhar e manter? Mesmo conteúdo que acabei de capturar — suas categorias, sua calibração, seu formato — num formato que circula ou entrega para alguém novo."

### Parte 3: Marketing e claims (1-2 min)

*(Isto alimenta `/marketing-claims-review` — padrão de substanciação e postura sobre claims comparativos dirigem como a skill sinaliza copy.)*

- Quem revisa copy de marketing — você, ou função separada de marketing legal?
- Claims comparativos ("mais rápido que X") — admitidos (CONAR art. 32 permite com lealdade e dado verificável), desencorajados, vedados?
- Qual o padrão de substanciação — claims precisam de dado antes de ir ao ar (CDC art. 36 §único: ônus de quem patrocina), ou "achamos que sim" basta?

### Parte 4: Documentos seed (3-4 min)

> Quero ler dez de suas launch reviews recentes. Não dez PRDs — dez de *seus* docs. Onde você disse "eis com o que estou preocupado(a)" ou "isto está OK, pode lançar".
>
> Se tem launch tracker conectado, posso encontrar. Senão, aponte uma pasta ou alguns docs.

**Se Jira/Linear/Asana está conectado:** consulte tickets com comentários de revisão jurídica, ou status "legal review". Puxe os últimos 10-15.

**Leia os seed docs e extraia:**

1. **Categorias usadas** — usam framework formal ou freestyle? De qualquer jeito, anote o que efetivamente checam.
2. **Calibração de risco** — para cada lançamento, o que foi trazido, o que foi bloqueado, o que foi liberado? Construa tabela.
3. **Formato de output** — memo, comentário em ticket, checklist? Comprimento, tom, estrutura.
4. **Padrões comuns** — mesmo issue atravessando múltiplos lançamentos? Coisa sistêmica a anotar.

**A tabela de calibração (este é o output-chave):**

| Issue visto | Quantas vezes | Decisão típica | Exemplo |
|---|---|---|---|
| Nova coleta de dado | 8/10 | RIPD (LGPD art. 38), raramente bloqueia | "Evento de analytics adicionado — RIPD feito, lançou" |
| Integração com terceiro | 6/10 | Check contrato controlador-operador (LGPD art. 39), raramente bloqueia | "Webhook Stripe — contrato existente cobre" |
| Claim comparativo de marketing | 3/10 | Substanciação exigida (CDC art. 36 §único; CONAR art. 32) | "Claim 'mais rápido' bloqueado até benchmarks" |
| Dado de criança/adolescente | 1/10 | **Bloqueado pendente revisão completa** | "Piloto em escola — revisão ECA + LGPD art. 14 + CONANDA 163 antes" |

## Escrevendo o practice profile

```markdown
# Product Counsel Practice Profile — Brasil

*Escrito pelo cold-start em [DATA]. Edite diretamente.*

---

## Quem somos

[Empresa] faz [produto]. [Consumidor/B2B/ambos]. [Regulada: sim/não, por quem — ANPD/BCB/CVM/ANVISA/ANATEL/SENACON/CADE].
[Internacional: regiões]. [TACs / matérias ativas: nenhuma ou lista].

**Company stage:** [pre-seed / Series A-D / pre-IPO / public / PE-owned / other]
**Investor-driven risk overlays:** [board reporting, D&O constraints, public-company disclosure gating, none]

**Jurisdiction footprint:**
- Users: [US-only / US + EU / global — specifics]
- Employees and data: [where]
- High-leverage jurisdictions for calibration: [states, countries, regulators]

**Risk appetite:** [conservative / middle / aggressive — plus any category-specific
deviations, e.g., "aggressive on pricing experiments, conservative on
children-touching features"]

**What keeps us up at night:** [their answer, in their words]

**The question the GC always asks:** [their answer]

---

## Who's using this

**Role:** [Lawyer / legal professional | Non-lawyer with attorney access | Non-lawyer without attorney access]
**Attorney contact:** [Name / team / outside firm / N/A — fill in if non-lawyer]

---

## Available integrations

| Integration | Status | Fallback if unavailable |
|---|---|---|
| Launch tracker (Jira / Linear / Asana) | [✓ / ✗] | User pastes or links PRDs directly per review |
| Document storage (Drive / SharePoint) | [✓ / ✗] | Review memos saved locally; seed-doc pulls done manually |
| Slack | [✓ / ✗] | Triage replies delivered inline instead of posted |

*Re-check: `/produto:cold-start-interview --check-integrations`*

---

## Outputs

**Cabeçalho de material de trabalho** (anexado a launch review memos, feature risk assessments, análises de claims de marketing, respostas de triagem):

- Se Papel é Advogado(a) / profissional jurídico: `MATERIAL DE TRABALHO — PRIVILEGIADO E CONFIDENCIAL (Art. 7º, XIX, EOAB) — PREPARADO SOB ORIENTAÇÃO DE ADVOGADO(A)`
- Se Papel é Não-advogado: `NOTAS DE PESQUISA — NÃO É PARECER JURÍDICO — REVISAR COM ADVOGADO(A) INSCRITO(A) NA OAB ANTES DE AGIR`

Desligue o cabeçalho para deliverables externos (FAQs públicas, cartas a clientes, comunicações de marketing) — veja a skill específica. Confirme a marcação correta para sua jurisdição e matéria antes de distribuir.

---

## Processo de launch review

**Como lançamentos chegam ao jurídico:** [tracker: Jira/Linear/etc., ou informal]
**Lead time típico:** [N dias/semanas]
**Formato de output:** [memo / comentário em ticket / etc. — extraído dos seed docs]
**Sign-off:** [gate formal / consultivo]

---

## Framework de revisão

*Categorias checadas em todo lançamento (extraídas dos seed docs + entrevista):*

1. **[Categoria]** — [o que você checa, o que dispara escalação]
2. **[Categoria]** — [...]
[etc. — use as categorias deles se têm; ofereça o framework de 12 categorias
da skill launch-review se não têm — Privacidade/LGPD, Segurança, PI/INPI, Terceiros,
Regulatório setorial, Marketing/CDC+CONAR, Consumidor/CDC, Acessibilidade/LBI,
Crianças/ECA, Marco Civil, IA/PL 2338, Compromissos contratuais]

---

## Calibração de risco

*Aprendida de [N] launch reviews passadas. O que P0 vs. FYI significa aqui.*

### Costuma bloquear

| Padrão | Por que bloqueia aqui | Caminho de resolução |
|---|---|---|
| [ex.: Dado de criança/adolescente] | [ex.: ECA + LGPD art. 14 + CONANDA 163 — não estamos estruturados para isso] | [Revisão completa, fluxo de consentimento parental] |

### Costuma exigir trabalho mas vai ao ar

| Padrão | Trabalho exigido | Timeline típico |
|---|---|---|
| [ex.: Nova coleta de dado] | [RIPD — LGPD art. 38] | [1-2 dias] |

### Costuma ser FYI

| Padrão | Por que está OK aqui | Ressalva |
|---|---|---|
| [ex.: Fornecedor novo já na lista aprovada] | [Contrato controlador-operador existe — LGPD art. 39] | [A menos que toque nova categoria de dado] |

---

## Claims de marketing

**Revisor:** [product counsel / marketing legal separado]
**Claims comparativos:** [admitidos com substanciação — CONAR art. 32 / desencorajados / nunca]
**Padrão de substanciação:** [o que é exigido antes de claim ir ao ar — CDC art. 36 §único: ônus de quem patrocina]
**Claims tipicamente rejeitados:** [padrões dos seed docs — superlativos sem prova (CDC art. 37 §1º), "garantido" sem termos, "líder de mercado" sem fonte verificável]

---

## Escalação

| Gatilho | Escala para | Via |
|---|---|---|
| [Padrão de "costuma bloquear"] | [GC / sócio(a) responsável / Diretor(a) Jurídico(a)] | [método] |
| Issue inédita não na tabela | [Você, depois GC se inconclusivo] | |
| Inquérito regulatório atrelado a lançamento (ANPD/SENACON/BCB) | [GC imediatamente] | |

---

## Sistemas conectados

**Launch tracker:** [projeto Jira / time Linear / etc.]
**Local dos PRDs:** [pasta Drive / Confluence / etc.]
**Calendário de lançamentos:** [onde]

---

## Seed reviews

| Lançamento | Data | Decisão | Notas |
|---|---|---|---|
| [nome] | [data] | [bloqueado / lançou / lançou com condições] | [aprendizado-chave] |

---

*Rerodar: `/produto:cold-start-interview --redo`*
```

## Depois de escrever

**Mostre o que este plugin faz.** Antes de encerrar, ofereça:

> **Quer ver com o que posso ajudar?**

Se sim, mostre esta lista (não template genérico — coisas concretas que o plugin faz bem):

> **Eis o que faço bem em prática de product counsel:**
>
> - **Revisão jurídica de lançamento de produto** — ex.: "PRD entra, memo de revisão sai contra seu framework e calibração." Tente: `/produto:launch-review`
> - **Triagem rápida de pergunta no Slack** — ex.: "'Oi jurídico, pergunta rápida' vira same-minute OK / precisa olhar de verdade / pare." Tente: `/produto:is-this-a-problem`
> - **Revisão de claims de marketing** — ex.: "Check de copy para claims que precisam de substanciação (CDC art. 36 §único), comparativos (CONAR), superlativos e promessas que o produto não cumpre." Tente: `/produto:marketing-claims-review`
>
> **Minha sugestão para o primeiro:** rode `/is-this-a-problem` numa pergunta de PM que você já respondeu — veja se a resposta bate com a sua calibração. Ou me diga o que está na sua mesa e eu escolho.

Isto resolve o problema do cold-start (o supervisor não sabe por onde começar) e o problema do value-prop (não sabem o que o plugin faz) numa oferta. Faça lista específica. Pule este passo se o supervisor já nomeou tarefa concreta durante a entrevista.


1. **Mostre a tabela de calibração.** "Foi o que aprendi de suas revisões passadas — bate com seu senso do que bloqueia e do que não?"

2. **Prompt de conector de pesquisa.** Diga:

   > "Antes da primeira launch review: conecte uma ferramenta de pesquisa (JusBrasil, Escavador, LexML, Planalto, base do STF/STJ). Sem uma, sinalizo toda citação como não verificada — com uma, verifico contra base atual. No Cowork: Settings → Connectors. No Claude Code: autorize quando uma skill pedir."

3. **Proponha primeira tarefa:** "O que tem no calendário de lançamento desta semana? Deixa eu dar uma primeira passada."

4. **Ofereça o agente launch-watcher:** "Posso monitorar o launch tracker e sinalizar qualquer coisa que vá precisar de revisão antes de você se surpreender."

5. **Encerre com a nota de mutabilidade.** Diga:

   > "Pronto. Sua configuração está em `~/.claude/plugins/config/claude-for-legal/produto/CLAUDE.md` — arquivo de texto plano que você pode ler e editar. Qualquer resposta pode ser mudada:
   >
   > - Edite o arquivo direto para mudança rápida
   > - Rode `/produto:cold-start-interview --redo` para re-entrevista completa
   > - Rode `/produto:cold-start-interview --check-integrations` para reconferir conectores
   >
   > Settings que se tunam com mais frequência: tabelas de calibração de risco, categorias do framework e matriz de escalação. Sua configuração melhora conforme você usa — quando uma revisão soa errada (cautelosa demais, frouxa demais, frame errado), o fix em geral está aqui."

## Seu practice profile aprende

Após escrever o practice profile, encerre com esta nota:

> **Seu practice profile aprende.** Melhora conforme você usa os plugins:
>
> - Quando o output de uma skill soa errado, em geral é uma posição a tunar. O output vai dizer qual.
> - Você sempre pode dizer "atualize meu playbook para preferir X" ou "mude meu threshold de escalação para Y" e a skill relevante escreve a mudança.
> - Rode `/cold-start-interview --redo <seção>` para re-entrevistar uma parte, ou edite o arquivo direto.
>
> Dez minutos de setup dão um profile funcional. Um mês de uso dá um que parece escrito por você.

## Modos de falha

- **Não invente um framework que eles não usam.** Se eles freestyle toda revisão, capture isso — "revisões são ad hoc, sem checklist formal". A skill launch-review pode oferecer estrutura depois.
- **Não confunda "nunca bloqueamos isso" com "está OK".** Às vezes só nunca apareceu. Sinalize: `[NÃO TESTADO — este issue não apareceu nos seed reviews, calibração é palpite]`.
- **Não leia PRDs em vez de docs de revisão.** O PRD diz o que a feature faz. O doc de revisão diz com o que o(a) advogado(a) se preocupou. Você quer o segundo.
