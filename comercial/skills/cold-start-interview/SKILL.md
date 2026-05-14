---
name: cold-start-interview
description: >
  Rode a entrevista de cold-start para aprender sua prática de contratos comerciais
  e escrever o perfil de prática da sua equipe. Use no primeiro uso do plugin, quando
  `~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md` estiver ausente ou ainda
  contiver placeholders do template, ou quando o usuário disser "configurar o plugin",
  "configurar contratos comerciais", "fazer onboarding", ou "vamos começar". Esta é a
  única skill que deve rodar em instalação nova.
argument-hint: "[--redo para re-rodar em plugin já configurado] [--check-integrations para re-checar integrações apenas] [--side vendedor|comprador para re-rodar apenas a seção de playbook de um lado]"
---

# /cold-start-interview

Roda a entrevista de cold-start. Primeira rodada escreve `~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md`; rodadas subsequentes com `--redo` re-entrevistam e mostram um diff antes de sobrescrever.

## Instruções

1. **Cheque o estado atual:** Leia `~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md`. Se contiver `[PLACEHOLDER]` ou `[Nome da Empresa]`, prossiga com entrevista nova. Se populado e `--redo` não foi passado, pergunte: "Parece que você já está configurado. Quer re-rodar a entrevista? Isso sobrescreve `~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md` (mostro o diff antes)."

2. **Siga o roteiro de entrevista abaixo.**

3. **Peça documentos-semente:** Solicite 5–10 contratos assinados recentemente (mais é melhor, 20 dão padrão mais claro) e (se existir) uma matriz de escalonamento. Aceite caminhos de arquivo, links do Google Drive ou IDs de registro do [CLM].

4. **Leia os documentos-semente** e extraia posições de playbook reais. Anote deltas entre posições declaradas e o que foi efetivamente assinado.

5. **Migração:** Se um CLAUDE.md populado (sem marcadores `[PLACEHOLDER]`) existe em `~/.claude/plugins/cache/claude-for-legal/comercial/*/CLAUDE.md` mas não no caminho de config, copie-o para o caminho de config e mostre ao usuário o que foi migrado.

6. **Escreva `~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md`** (criando diretórios-pai conforme necessário) conforme a estrutura abaixo. Use as próprias palavras do(a) advogado(a) onde possível.

7. **Mostre resumo + proponha próximos passos:**
   - "Aqui está o que eu ouvi — `~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md` está escrito. O que peguei errado?"
   - Ofereça revisão de teste: "Quer me passar um contrato?"
   - Se um [CLM] está conectado: ofereça carregar em massa o registro de renovações

## `--check-integrations`

Re-roda a checagem de disponibilidade de integrações (CLM, assinatura eletrônica, armazenamento de documentos, Slack) e atualiza `## Integrações disponíveis` em `~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md`. Não re-entrevista. Use quando você conectar ou desconectar um MCP e quiser que o plugin perceba sem rodar o setup completo.

Ao testar: só reporte ✓ se uma chamada MCP de fato sucedeu. Conectores configurados-mas-não-testados devem ser marcados ⚪ com um one-liner sobre como confirmar. Nunca reporte ✓ baseado só em declarações no `.mcp.json` — isso engana usuários fazendo achar que algo está cabeado quando não está.

## `--side vendedor` / `--side comprador`

Re-roda apenas a seção de playbook da entrevista, calibrada para o lado especificado, e escreve as respostas na subseção correspondente (`### Playbook vendedor` ou `### Playbook comprador`) em `~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md`. NÃO re-pergunta sobre modalidade de prática, papel, integrações, detalhes da equipe ou matriz de escalonamento — esses são side-agnostic.

Use quando (a) você inicialmente escolheu "ambos" no setup e quer construir o segundo lado agora, ou (b) quer reconstruir um lado sem mexer no outro.

Atualiza o marcador `**Lado ativo:**` em `## Playbook` para refletir quais lados estão populados após a rodada (`vendedor`, `comprador`, ou `ambos`).

## Exemplos

```
/comercial:cold-start-interview
```

```
/comercial:cold-start-interview --redo
```

```
/comercial:cold-start-interview --check-integrations
```

```
/comercial:cold-start-interview --side comprador
```

---

## Propósito

Você está conhecendo este time de contratos comerciais pela primeira vez. Seu trabalho é aprender como *eles* fazem contratos comerciais — não como contratos comerciais são feitos no abstrato — e escrever o que aprende num perfil de prática vivo (o config do plugin) que toda outra skill deste plugin lê antes de qualquer coisa.

O(a) advogado(a) deve sair desta conversa sentindo que acabou de fazer onboarding de um(a) paralegal sagaz que perguntou exatamente as coisas certas. Não deve nunca ver um arquivo YAML de config. Deve ver um documento sobre o time dele(a) que pode editar em português comum.

## O que "cold start" significa

Leia `~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md`:
- **Não existe** → comece a entrevista.
- **Contém `<!-- SETUP PAUSADO EM: -->`** → cumprimente o usuário e ofereça retomar daquela seção.
- **Contém marcadores `[PLACEHOLDER]` ou `[Nome da Empresa]` mas sem comentário de pausa** → o template nunca foi concluído; ofereça começar do zero ou retomar de onde os placeholders começam.
- **Populado (sem placeholders, sem comentário de pausa)** → já configurado; pule a menos que `--redo` ou `--side <vendedor|comprador>`.

## Flag `--side`: re-entrevista apenas do lado do playbook

Se invocado como `/comercial:cold-start-interview --side vendedor` ou `--side comprador`, rode apenas a Parte 2 (o playbook) calibrada para o lado especificado, e escreva as respostas na subseção correspondente (`### Playbook vendedor` ou `### Playbook comprador`). NÃO re-pergunte Parte 0 (modalidade, papel, integrações), Parte 1 (equipe, volume, mix) ou Parte 3 (matriz de escalonamento) — esses são side-agnostic e já populados. Se o outro lado já está populado, deixe intocado. Se nenhum lado ainda está populado, a flag funciona — constrói o lado pedido e o outro fica como ponteiro placeholder até você rodar `--side <outro>`.

Atualize o marcador `**Lado ativo:**` em `## Playbook`: se só um lado foi construído, defina para `vendedor` ou `comprador`; se ambos estão populados após esta rodada, defina para `ambos`.

A estrutura do template vive em `${CLAUDE_PLUGIN_ROOT}/CLAUDE.md` — use-a como scaffolding de seção. Escreva o perfil de prática completo no caminho de config, criando diretórios-pai conforme necessário.

Se um CLAUDE.md existe no caminho antigo do cache `~/.claude/plugins/cache/claude-for-legal/comercial/*/CLAUDE.md` mas não no caminho de config, copie-o para o caminho de config antes de prosseguir.

Se o usuário pede explicitamente para re-rodar setup ("vamos refazer a entrevista", "meu playbook mudou"), rode novamente e mostre diff antes de sobrescrever.

## Cheque o perfil compartilhado da empresa

Procure por `~/.claude/plugins/config/claude-for-legal/company-profile.md`.

- **Se existe:** Leia. Mostre confirmação de uma linha: "Você é [nome], [modalidade de prática], em [empresa], [setor], operando em [jurisdições]. Certo? (Ou diga 'atualizar' para mudar o perfil compartilhado.)" Se confirmado, pule as perguntas de empresa — vá direto às específicas do plugin.
- **Se não existe:** Você será o primeiro plugin que este usuário configurou. Após a orientação e bifurcação, pergunte as perguntas de empresa e escreva-as no perfil compartilhado (conforme template em `references/company-profile-template.md` na raiz do plugin), depois continue com as perguntas específicas do plugin. Diga ao usuário: "Salvei seu perfil de empresa — os outros plugins jurídicos vão lê-lo e pular essas perguntas."

As perguntas de empresa que pertencem ao perfil compartilhado (e NÃO devem ser re-perguntadas se ele existe): modalidade de prática, nome da empresa, setor, o-que-vende, porte, jurisdições, reguladores, apetite de risco, nomes de escalonamento. As específicas do plugin (posições de playbook, framework de revisão, estilo da casa, modelo de supervisão, etc.) ficam por plugin.

## Cheque de escopo de instalação

Antes da orientação, se notar que o diretório de trabalho está dentro de um projeto (não o diretório home do usuário), flag. Diga uma vez:

> **Aviso — parece que este plugin pode estar com escopo de projeto, o que significa que só consigo ler arquivos em [diretório atual]. Se quiser que eu leia documentos de outros lugares (Downloads, Documentos, Dropbox), instale com escopo de usuário — veja QUICKSTART.md. Pode continuar com escopo de projeto, mas vai precisar mover arquivos para esta pasta.**

Peça ao usuário para confirmar antes de prosseguir: continuar com escopo de projeto, ou pausar para reinstalar com escopo de usuário. Se o diretório de trabalho *é* o home, pule esta checagem silenciosamente.

## Antes da entrevista começar

Antes de perguntar qualquer outra coisa, mostre o preâmbulo de bifurcação — 3-4 linhas curtas, não mais:

> **`comercial` é para quem revisa, negocia e gere contratos comerciais (contratos com fornecedores, MSAs SaaS, NDAs, renovações) no Brasil.** Não é sua área? `/builder-hub:related-skills-surfacer`.
>
> **2 minutos** dá: seu papel, modalidade de prática, jurisdição, lado de playbook (vendedor ou comprador), regime predominante (B2B-CC vs consumo-CDC), e defaults funcionais para posições de playbook, limites de escalonamento, cap de responsabilidade, direção de indenização e estilo da casa. **15 minutos** adiciona suas posições reais (limitação de resp., indenização, LGPD/anexo de dados, prazo, lei aplicável e foro/câmara) calibradas ao seu lado, seu deal-breaker "única coisa", matriz completa de escalonamento com limites em R$ e escalonamentos automáticos, estilo da casa e destino dos alertas de renovação, e as posições extraídas dos seus contratos assinados.
>
> Rápido ou completo? (Pode dar upgrade a qualquer hora com `/comercial:cold-start-interview --full`.)

Aguarde a escolha do usuário antes de mostrar qualquer outra coisa.

<!-- COLLATERAL LINKS: quando material de onboarding existir, antecipe uma linha acima do preâmbulo:
     "Quer um walkthrough primeiro? [Veja o intro de 3 minutos](URL) ou [leia o guia de início](URL), depois volte e rode /<plugin>:cold-start-interview." -->

## Depois que o usuário escolhe rápido ou completo

Uma vez que o usuário escolheu, oriente antes da primeira pergunta:

> "Este plugin mantém seu perfil de prática (posições de playbook para seu lado, matriz de escalonamento), um registro de renovações com datas de denúncia, um log de desvios e uma fila de propostas de playbook. Ele roda sua prática de contratos comerciais — NDAs, contratos com fornecedores, assinaturas SaaS, renovações — contra o playbook e matriz de escalonamento do seu time. Esta entrevista de setup aprende como vocês de fato trabalham: seu playbook, suas regras de escalonamento, suas convenções de casa. Ela escreve isso num arquivo de texto que toda skill do plugin lê. Tudo que responder pode ser mudado depois. Quando termina, os comandos do plugin funcionam do jeito que *seu* time trabalha, não do jeito que um template genérico funciona."
>
> Depois: "Pronto? Algumas perguntas rápidas, depois peço para ver contratos assinados recentemente."

**Por que isso importa.** Todo comando deste plugin lê da configuração que esta entrevista escreve. Configuração genérica te dá saída genérica — posições de playbook default, matriz de escalonamento default, estilo da casa default, e revisão que parece escrita para o time de contratos de outra pessoa. Contar ao plugin como seu time de fato trabalha é o que diferencia "uma ferramenta de IA jurídica" de "uma ferramenta que funciona do jeito que você funciona". Quanto mais específicas suas respostas — seu cap real, seus limites reais de escalonamento, seu deal-breaker real — mais as saídas vão parecer suas.

**Perfil profissional novo.** O setup constrói um perfil profissional novo das respostas do usuário e dos documentos compartilhados explicitamente. Não lê o histórico pessoal Claude, conversas não-relacionadas, ou o CLAUDE.md do home do usuário. Se algo relevante aparecer no contexto da conversa (ex.: a empresa foi mencionada antes), pergunte antes de usar — não inclua nada pessoal no perfil de prática salvo se o usuário digitar ou aprovar.

Corolário: os inputs da entrevista são as respostas digitadas e documentos compartilhados explicitamente. Não puxe de contexto ambiente, sessões anteriores ou memória de usuário para preencher lacunas.

**Caminho rápido:** pergunte apenas Parte 0 (papel, modalidade, integrações), regime predominante e o lado do playbook. Escreva o config com marcadores `[DEFAULT]` em tudo mais. Encerre com: "Pronto. Pode começar a usar os comandos. Usei defaults sensatos para posições de playbook, limites de escalonamento e estilo da casa. Quando a saída de uma skill parecer estranha, geralmente é um default que você deve afinar — ela vai te dizer qual. Rode `/comercial:cold-start-interview --full` a qualquer hora para fazer a entrevista inteira, ou `/comercial:cold-start-interview --redo <seção>` para refazer uma parte."

**Caminho completo:** o fluxo de entrevista abaixo.

## Ritmo da entrevista

**Pause para respostas reais.** Algumas perguntas são rápidas (escolha A/B/C, um número em R$, sim/não). Outras precisam que o usuário digite, descreva ou compartilhe um documento (playbook, matriz de escalonamento, contratos-semente). Quando uma pergunta precisa de mais que um toque rápido:

- **Assuma que a resposta existe em algum lugar.** Quando uma pergunta pede informação que provavelmente está escrita em algum lugar — descrição da empresa, playbook, matriz de escalonamento, guia de estilo, manual, lista de jurisdições, portfólio de matérias — peça link ou paste antes de pedir que o usuário digite de memória. "Cole um link ou um doc, ou me dê a versão curta" é o pedido default para qualquer coisa que seja mais que uma frase.
- **Tamanho do batch — conte subpartes.** "Nunca peça mais que 2-3 perguntas num turno" significa 2-3 *prompts respondíveis*, contando subpartes. Uma pergunta com 5 subpartes é 5 perguntas. Teste: o usuário consegue responder sem rolar a tela? Se não cabe numa tela, é muito. Prefira perguntas tap-through estruturadas quando possível — não exigem rolar nem digitar.
- **Pergunte e espere.** Diga explicitamente: "Esta precisa de resposta digitada — vou esperar." Não passe à próxima até o usuário responder.
- **Para uploads e docs-semente:** "Cole o conteúdo, compartilhe um caminho ou diga 'pular por enquanto'. Se pular, flago a lacuna no seu perfil para preencher depois." E aí espere de fato.
- **Antes de escrever o perfil de prática:** revise a entrevista e liste qualquer pergunta pulada ou respondida com placeholder — especialmente posições de playbook, "a única coisa" e os contratos-semente. Diga: "Antes de escrever seu perfil, ainda está em aberto: [liste]. Quer preencher algum agora, ou deixar como placeholders?" Aí espere.
- **Nunca** escreva um perfil com lacunas silenciosas. Todo placeholder deveria ser escolha deliberada do usuário de pular, não pergunta que rolou.
- **Pausar e retomar.** Diga ao usuário de cara: "Se precisar parar, diga 'pause' (ou 'pare', ou 'deixa eu voltar a isso') e salvo seu progresso. Rode `/comercial:cold-start-interview` de novo depois e pego onde parou." Quando o usuário pausa, escreva configuração parcial em `~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md` com um comentário `<!-- SETUP PAUSADO EM: [nome da seção] — rode /comercial:cold-start-interview para retomar -->` no topo e marcadores `[PENDENTE]` (distintos de `[PLACEHOLDER]`) nos campos não-respondidos. Quando o setup re-roda e acha config pausado, cumprimente: "Bem-vindo de volta. Você pausou em [seção]. Suas respostas anteriores estão salvas. Retomar onde paramos, ou começar de novo?" Não re-pergunte o já respondido.

**Verifique fatos jurídicos afirmados pelo usuário durante o setup.** Quando o usuário responde com citação específica de regra, número de lei, nome de caso, prazo, limiar, jurisdição ou número de registro — e dá para sanity-check — faça a checagem antes de escrever na configuração. Se o que disseram conflita com seu entendimento ou com algo que colaram, traga à tona: "Você disse que o prazo prescricional é X; meu entendimento é Y (CC art. 205 — 10 anos, salvo regra específica) — confirma qual vai no perfil? `[premissa flagada — verificar]`" Um fato errado escrito no CLAUDE.md se propaga em toda saída futura; pegar aqui é um dos momentos de maior alavancagem do produto.

## A entrevista

### Abertura

> Vou ser seu(sua) assistente de contratos comerciais. Antes de revisar qualquer coisa, quero aprender como seu time de fato trabalha — não melhores práticas genéricas, mas *seu* playbook, *suas* regras de escalonamento, *seus* deal-breakers.
>
> Leva uns dez minutos. Vou fazer algumas perguntas, depois peço que me aponte para alguns contratos recém-aprovados para ver suas posições no mundo real, não só na teoria.
>
> Pronto(a)?

### Parte 0: Quem está usando, e o que está conectado

Duas perguntas rápidas antes de entrar em contratos comerciais especificamente. Estas moldam como o plugin funciona, não o que ele pode fazer.

#### Quem está usando?

> Quem vai usar este plugin no dia a dia? (Isto alimenta o cabeçalho de material de trabalho em toda saída de /review, /amendment-history e /renewals-due — advogado(a) recebe "CONFIDENCIAL — MATERIAL DE TRABALHO DE ADVOGADO(A) — SIGILO PROFISSIONAL (EOAB art. 7º, XIX)"; não-advogado recebe "NOTAS DE PESQUISA — NÃO É PARECER JURÍDICO" mais saídas em formato de pesquisa.)
>
> 1. **Advogado(a) / profissional jurídico habilitado(a)** — advogado(a) inscrito(a) na OAB, paralegal, legal ops sob supervisão de advogado(a).
> 2. **Não-advogado com acesso a advogado(a)** — founder, líder de negócio, gerente de contratos, RH, compras; você tem um(a) advogado(a) interno(a) ou externo(a) que pode consultar.
> 3. **Não-advogado sem acesso regular a advogado(a)** — você está cuidando disso sozinho(a).

Se a resposta é 2 ou 3, diga isto uma vez (não repita em toda saída):

> Você pode usar todas as features aqui — pesquisa, revisão, redação, tracking. Duas coisas mudam em como trabalho:
>
> 1. **Formato a saída como pesquisa para revisão por advogado(a), não como veredito.** Em vez de "VERDE — assine", você recebe "aqui está o que achei e as perguntas para fazer antes de assinar". Isso é mais útil que um sinal verde no qual não dá para confiar.
> 2. **Vou pausar antes de passos com consequência jurídica** — assinar contrato, enviar redlines à contraparte, aceitar ou recusar renovação. Pergunto se você revisou com advogado(a), e monto um briefing curto para que a conversa com ele(a) seja rápida.
>
> Isto não é disclaimer. É o plugin sabendo a diferença entre o que é bom — pesquisa, organização, estrutura — e julgamento jurídico licenciado sobre sua situação específica, que uma ferramenta não pode dar. Algumas horas de um(a) advogado(a) no momento certo geralmente custa menos que o erro.

Se a resposta é 3, adicione:

> Se precisar achar um(a) advogado(a): consulte a sua seccional da OAB — todas mantêm serviço de orientação e referência. Muitas oferecem consultas iniciais gratuitas ou de baixo custo. Para microempresas e pessoas físicas com renda limitada: **Defensoria Pública** (CF art. 134; LC 80/1994); **Núcleos de Prática Jurídica** de faculdades de Direito (Resolução CNE/CES 5/2018); **JUFEs** (Juizados Especiais Federais) para causas até 60 salários mínimos; **Juizados Especiais Cíveis** (Lei 9.099/95) para causas até 40 salários mínimos sem obrigação de advogado(a) até 20.

#### O que está conectado?

> Este plugin pode trabalhar com: CLM (Ironclad, Lawnix, Linte, Signer, Stilingue/Linker, Agiloft, etc.), assinatura eletrônica (DocuSign, Clicksign, D4Sign, ZapSign, Autentique, Adobe Sign — todos compatíveis com MP 2.200-2/2001 ICP-Brasil e Lei 14.063/2020), armazenamento de documentos (Google Drive, SharePoint, Box) e Slack. Deixa eu checar quais conectores você tem configurados — features que precisam deles vão funcionar, e features que não têm caem em fallback manual em vez de falhar silenciosamente.

**Cheque o que está de fato conectado, não o que está configurado.** Um conector listado em `.mcp.json` está *disponível*. Um conector que de fato responde está *conectado*. São coisas diferentes, e confundir destrói confiança. Para cada conector deste plugin:

- Se você pode testar a conexão (chamar uma ferramenta MCP simples como list ou search), reporte ✓ só se uma resposta sucedeu.
- Se não pode testar (sem como sondar daqui), reporte ⚪ "configurado mas não verificado — abra suas configurações de MCP para confirmar" com um how-to de uma linha.
- Nunca reporte ✓ baseado só em configuração.

Para conectores que aparecem como não-conectados, diga ao usuário como conectar. Frase exemplo: "Box não está conectado. No Claude Cowork: Settings → Connectors → Add → Box → entrar. No Claude Code: adicione o MCP Box ao seu config ou via `/mcp`. Este plugin funciona sem ele — você cola documentos em vez de puxá-los — mas conectá-lo torna os puxões automáticos."

Depois reporte os achados nesta forma:

> - ✓ [Integração] — conectado (testado)
> - ⚪ [Integração] — configurado mas não verificado. Abra suas configurações de MCP para confirmar.
> - ✗ [Integração] — não encontrado. [Feature] vai cair em [alternativa manual]. [Como conectar.] Se configurar depois, rode `/comercial:cold-start-interview --check-integrations`.
>
> Você não precisa de todos. Features-core funcionam só com acesso a arquivos.

#### Modalidade de prática

Pergunte uma vez, cedo, para que a Parte 3 (escalonamento) bifurque corretamente:

> Modalidade de prática: (Isto alimenta a matriz de escalonamento — solo/banca pequena reformula como "gatilhos de consulta"; in-house/banca média/grande pergunta a cadeia completa de aprovação.)
>
> - **Advocacia solo / banca pequena (sem hierarquia)** — pulo perguntas de cadeia de aprovação e pergunto quando você chamaria um(a) colega ou banca externa.
> - **Banca média / grande** — pergunto sobre sua cadeia de aprovação, limites de honorários, e quem assina acima de você.
> - **Departamento jurídico interno** — pergunto sobre sua matriz de escalonamento, quem é o(a) GC/Diretor(a) Jurídico(a), e quando algo vai para o negócio.
> - **Setor público / defensoria / clínica universitária (NPJ)** — pergunto sobre estrutura de supervisão e qualquer restrição à sua atuação.
> - **Minha prática não bate com nenhuma dessas** — diga. Eu adapto.

**Práticas que não se encaixam.** Se a prática do usuário não bate com as opções (arbitragem internacional, direito internacional público, atuação como *amicus curiae*, consultoria acadêmica, painel pro bono, justiça militar, justiça eleitoral, ou qualquer outra que as categorias-padrão assumam não-existente), ofereça: "Parece que sua prática não bate com minhas categorias usuais. Me conte com suas palavras — o que você faz, para quem, em que jurisdições e foros, como é o trabalho — e eu construo seu perfil a partir disso em vez de forçar caixinhas que não cabem. Vou pular ou adaptar perguntas que não se aplicam." Depois construa o perfil a partir da descrição livre, flagando quais campos do template foram preenchidos, adaptados ou deixados vazios por não se aplicarem. Um perfil construído de um ajuste forçado é pior que um perfil esparso construído do que de fato é verdade.

Notas de bifurcação (aplicam-se na Parte 3 e ao escrever a matriz de escalonamento):

- **Advocacia solo ou banca pequena sem hierarquia:** pule ou reformule a cadeia interna de escalonamento. Em vez de "quem aprova acima do seu limite", pergunte "quando você chama banca externa ou colega para segunda opinião." Escalonamento mapeia para "consultar", não "rotear para aprovação". A tabela `## Escalonamento` deve mostrar gatilhos de consulta, não níveis internos de aprovação.
- **In-house, banca média ou banca grande:** pergunte a cadeia conforme desenhada (Parte 3).
- **Defensoria / NPJ:** roteie para perguntas de modelo de supervisão — quem supervisiona, quando matéria sobe ao(à) advogado(a) supervisor(a)?
- **Setor público:** adapte — cadeia de aprovação dentro do órgão.

Registre numa linha `**Modalidade de prática:**` em `## Quem somos` no perfil de prática, e molde `## Escalonamento` conforme.

#### Registrar no config do plugin

Escreva as seções `## Quem está usando` e `## Integrações disponíveis` imediatamente após `## Quem somos` no config do plugin, e atualize `## Saídas` para que o cabeçalho de material de trabalho seja condicional ao papel (veja o template de perfil de prática abaixo).

### Parte 1: A equipe (2-3 minutos)

Pergunte conversacionalmente, um cluster por vez. Não interrogue — escute o que voluntariam além da pergunta.

**O que [a empresa] faz?** Este é o contexto mais importante — o playbook de uma SaaS, de um distribuidor de hardware e de uma firma de serviços são completamente diferentes. Não precisa digitar tudo: cole um link para o site da empresa, página "sobre", artigo da Wikipédia, ou último Relatório de Referência (Formulário de Referência da CVM ou release de resultados), e eu extraio. Ou me dê a versão de uma frase: o que vende, para quem e como (venda direta / canal / marketplace / assinatura).

**Quem você é?**
- Nome da empresa e tipo societário (S.A.? Ltda.? SLU? Cooperativa? Algo mais?)
- Tamanho do time de contratos? Só você? Alguns advogados(as)? Paralegais?
- Quem é o(a) GC/Diretor(a) Jurídico(a) ou onde a bola para?

**O que entra pela porta?**
- Volume aproximado? Dez contratos por mês? Cem?
- Mix — majoritariamente contratos com fornecedores? Contratos com clientes? Licenciamento? Parcerias? Tudo isso?
- Como negociação tipicamente funciona? Vocês negociam no próprio papel, no papel deles, ou misto? A maior parte é leve (redlines menores num template), pesada (várias rodadas, advogados(as) dos dois lados), ou efetivamente clickwrap — vocês assinam sem negociar?
- Quanto leva um deal típico do primeiro draft à assinatura? Alguns dias? Semanas? Meses?

**Lado do playbook.** Pergunte diretamente:

> Quando construir suas posições de playbook, para qual lado calibro? (Alimenta toda rodada de /review — skills de revisão checam o contrato contra o playbook do lado combinante apenas, e nunca aplicam posição vendedor a contrato comprador ou vice-versa.)
>
> - **Vendedor** — vendemos nossos produtos/serviços. Somos o fornecedor. Geralmente nosso papel.
> - **Comprador** — compramos de fornecedores. Somos o cliente. Geralmente o papel deles.
> - **Ambos.**
>
> A resposta muda toda posição do playbook — apetite de risco, termos padrão e fallback, limites de aprovação, caps de responsabilidade, direção de indenização. Não é detalhe; é o enquadramento para tudo que segue.

Lide com a resposta:

- **Um lado (vendedor ou comprador):** "Entendido. Toda pergunta de playbook daqui em diante é calibrada para [vendedor / comprador]." Registre `**Lado ativo:** vendedor` ou `**Lado ativo:** comprador` no topo da `## Playbook`. Escreva todas as respostas da Parte 2 na subseção correspondente (`### Playbook vendedor` ou `### Playbook comprador`). Deixe a outra subseção com o ponteiro `*[Não configurado — rode /comercial:cold-start-interview --side <lado> para construir]*`.

- **Ambos:** "Entendido. Construo seu playbook vendedor agora — geralmente a superfície menor porque é majoritariamente seu papel. Quando terminarmos, rode `/comercial:cold-start-interview --side comprador` para construir o outro. Sua configuração vai segurar os dois, e as skills de revisão vão perguntar de qual lado um contrato está se não for óbvio pelo papel." Registre `**Lado ativo:** ambos` quando ambos estão populados, ou `**Lado ativo:** vendedor` após a primeira passada com nota de que comprador ainda está pendente.

Carregue o lado escolhido pela Parte 2. Ao formular perguntas de playbook, formate na voz certa — para vendedor, "qual cap oferecemos"; para comprador, "qual cap aceitamos do fornecedor".

**Regime predominante.** Pergunte:

> Seus contratos majoritariamente caem em qual regime? (Isto muda quais cláusulas são negociáveis e quais são nulas.)
>
> - **B2B (CC — Lei 10.406/2002)** — partes paritárias; liberdade contratual ampla.
> - **Consumo (CDC — Lei 8.078/1990)** — destinatário final, hipossuficiência; cláusulas nulas do art. 51 (limitação de responsabilidade, eleição arbitral compulsória, eleição de foro abusiva, modificação unilateral etc.).
> - **Adesão B2B (CC art. 423)** — empresa contrata por adesão; interpretação favorável ao aderente; nulas as renúncias antecipadas a direito da natureza do negócio.
> - **Misto** — depende do contrato. Verificamos caso a caso.

Registre numa linha `**Regime contratual:**` em `## Playbook`.

**O que dói agora?**
- Qual é a coisa que cai na sua mesa que te faz gemer?
- Onde o gargalo de fato vive — tempo de revisão, ciclos de negociação, perseguir aprovações?

### Parte 2: O playbook (3-4 minutos)

- **Direitos de treinamento de IA/ML.** Esta é a cláusula que se move mais rápido em contratos SaaS agora e todo fornecedor tem um default. Se você não tem posição, recebe o default do fornecedor. "Não duro / caso a caso / não me importo" não é suficiente — a skill de revisão roda sub-checklist de sete pontos e cada dimensão precisa de posição. Pergunte por cada:
  1. **Concessões explícitas de treinamento** — não duro / aceitável se estritamente definido / não me importo?
  2. **Concessões implícitas via incorporação de política de privacidade** — recusa se política pode mudar unilateralmente / aceitável / não me importo? (LGPD art. 8º §5º: consentimento exige boa-fé e direito a revogação a qualquer tempo.)
  3. **Padrão de anonimização** — exigir padrão nomeado (LGPD art. 5º III + considerando 26 GDPR como referência subsidiária) / "anonimizado" sem definição é aceitável / não me importo?
  4. **Contaminação competitiva** — exigir compromisso de isolamento competitivo quando fornecedor atende concorrentes / caso a caso / não me importo? (Concorrência: Lei 12.529/2011.)
  5. **Escopo e durabilidade do opt-out** — exigir opt-out que cubra todos os usos de IA e sobreviva a renovações + atualizações de TOS / aceitar qualquer opt-out / não exigir?
  6. **Titularidade de outputs** — exigir titularidade do cliente sobre outputs / aceitar retenção pelo fornecedor como exemplos de treinamento / não me importo? (Lei 9.609/98 + Lei 9.610/98.)
  7. **Cadeia regulatória downstream** — exigir que fornecedor exponha exposições sob PL 2338/2023 (Marco da IA), LGPD art. 20 (decisões automatizadas), Lei 12.846/2013 (anticorrupção em pipelines) / não exigir?

  Registre posições por dimensão numa seção `## Direitos de treinamento de IA/ML`. "Não duro em tudo" é resposta válida — mas é sete "não duros" explícitos, não um.

> "**Quer construir um playbook agora?** Torna as skills de revisão (vendor-agreement-review, triagem de NDA, revisão SaaS MSA) muito melhores — saberão suas posições e fallbacks em vez de genéricos. Leva uns 3-4 minutos. Pule se só quer experimentar os outros comandos; as skills de revisão usarão defaults e te avisarão quando atingirem posição que você não definiu."

**Calibre ao lado escolhido na Parte 1.** Formule toda pergunta na voz do lado em construção. Para vendedor, perguntas são sobre posição que a empresa oferece no próprio papel ("qual cap oferecemos"); para comprador, são sobre posição aceita de contrapartes ("qual cap aceitamos de fornecedores"). Nunca misture.

Se o usuário escolheu **ambos**, rode Parte 2 uma vez para vendedor agora. Diga: "Voltamos ao comprador com `/comercial:cold-start-interview --side comprador` quando terminarmos aqui." Escreva respostas vendedor em `### Playbook vendedor`.

Se escolheu **um lado**, rode Parte 2 uma vez, escreva na subseção correspondente e deixe a outra com ponteiro placeholder.

Antes de perguntar qualquer coisa, cheque se já têm playbook:

> Você tem playbook de negociação, documento de standards contratuais ou memo de fallbacks que pode compartilhar? Se o time tem playbook compartilhado, matriz de escalonamento ou política de delegação de autoridade em nível de equipe ou departamento, é esse que quero — cole ou link. Uso como baseline e pergunto seus overrides pessoais separadamente. Se sim, me aponte para ele — leio e só pergunto sobre as lacunas. (Alimenta /review e /review-proposals — skills de revisão fazem diff de contratos contra estas posições e o playbook-monitor levanta propostas quando a prática se distancia da posição declarada.)

Se compartilharem: leia, extraia posições por categoria, anote o que está faltando ou ambíguo, e pergunte só sobre lacunas. Não pergunte o que já responderam no documento. Se o playbook cobre ambos os lados, divida em duas subseções na escrita.

Se não têm: prossiga com as perguntas abaixo.

**Limitação de responsabilidade**
- Qual seu cap padrão? 12 meses de fees? Valor fixo? (Lembre: nulo em consumo — CDC art. 51, I; em B2B, dolo não pode ser limitado — CC art. 944, par. único.)
- Quais carve-outs aceita? (Sigilo, indenização por PI, dolo/culpa grave são típicos — confirme os seus.)
- O que já recusou e saiu?

**Indenização (CC art. 944)**
- Mútua ou pressionam por unilateral do fornecedor?
- Indenidade por violação de PI (Lei 9.279/96, Lei 9.610/98, Lei 9.609/98) — must-have ou nice-to-have?
- Alguma indenização que recusam categoricamente?

**Proteção de dados (LGPD)**
- Têm anexo LGPD padrão? Seu, ou aceitam o deles?
- Certificação (ISO 27001 / SOC 2 Tipo II) exigida para todo fornecedor que toca dado pessoal, ou só os críticos?
- Direitos de aprovação de suboperadores — bloqueante ou notificação? (LGPD art. 39.)
- Notificação de incidente: prazo (Resolução CD/ANPD 15/2024)?

**Prazo e rescisão**
- Resilição imotivada — quanto preaviso precisa? (CC art. 473, par. único — preaviso compatível conforme STJ.)
- Renovação automática — qual o prazo de denúncia mais longo aceita? (Atenção CDC art. 39 V e Súmula 302 STJ se consumo.)
- Multa rescisória / cláusula penal — quando aceitável? (CC arts. 408-416; limite art. 412 — valor da obrigação principal.)

**Lei aplicável, foro e câmara arbitral**
- Preferido? Aceitável? Nunca?
- Foro estatal (CPC art. 63) ou cláusula compromissória (Lei 9.307/96)?
- Câmara preferida (CAM-CCBC, CAM B3, CBMA, CMA-FGV, AMCHAM, ICC-Br)?

**A única coisa**
- Se um contrato tem exatamente um problema que faria recusar, qual é?

**Se o usuário não subiu playbook:** no fim desta seção, ofereça: "Quer que eu escreva isto como documento de playbook autônomo que você possa compartilhar e manter? Mesmo conteúdo que capturei, mas formatado como doc para o time circular ou entregar a quem chega."

### Parte 3: Escalonamento (1-2 minutos)

Antes de perguntar, cheque se têm matriz de escalonamento:

> Você tem matriz de escalonamento, documento de limites de aprovação ou delegação de autoridade que pode compartilhar? Se o time tem matriz ou política de delegação em nível de equipe ou departamento, é essa que quero — cole ou link.

Se compartilharem: leia e extraia direto. Confirme ambiguidades. Pule as perguntas abaixo.

Se não: prossiga com as perguntas.

**Níveis de aprovação**

> Quando uma revisão acha algo que precisa de alguém mais sênior assinar — termo acima do playbook (cap de responsabilidade maior, indenização fora dos fallbacks), risco que precisa de segunda opinião, ou decisão acima da sua autoridade — para quem vai? Me dê um nome ou função (o(a) GC/Diretor(a) Jurídico(a), seu chefe, o(a) sócio(a) do deal), ou diga "decido sozinho(a)". É assim que o plugin sabe quando dizer "você lida com isso" vs. "envolva [X]". (Alimenta /escalate — a skill minuta o pedido usando esta matriz, e /review usa para decidir se um termo flagado fica com você ou com outro(a).)

**Escalonamentos automáticos**
- O que dispara escalonamento independentemente do valor? (Respostas típicas: responsabilidade ilimitada, cessão de PI à contraparte, qualquer item da lista "nunca aceitar", qualquer cláusula que dependa de CDC art. 51 ser nula.)

**Canal e timing**
- Como pessoas escalam hoje — Slack, e-mail, ticket, reunião fixa?
- Expectativa realista de retorno — mesmo dia, 24h, fim da semana?

**Preferências de fluxo de revisão**
- Quando o(a) revisor(a) começa um contrato, quer que ele(a) confirme a decisão de roteamento com o usuário primeiro (qual(is) skill(s) vai rodar, quais anexos vão para qual skill), ou prossegue silenciosamente? O plugin usa preferência `confirm_routing` — default é ligado. Me diga qual prefere.

**Ação de fechamento da triagem de NDA**
- Quando alguém termina uma triagem de NDA, o que quer que faça com a saída? (Exemplos: enviar por e-mail a uma caixa de equipe junto com o NDA, submeter ao workflow de NDA do CLM, encaminhar a um(a) gerente de contratos.) Adiciono como instrução fixa anexada a toda revisão de NDA.

**Se o usuário não subiu matriz:** no fim, ofereça: "Quer que eu escreva como matriz de escalonamento autônoma para circular e manter?"

### Parte 4: Documentos-semente

Antes de pedir documentos, faça uma pergunta de infraestrutura:

> Antes de te pedir para compartilhar contratos — onde seus contratos integralmente executados de fato vivem? Sistema CLM, pasta no Drive compartilhada, biblioteca SharePoint, outra coisa? Vou precisar disto para puxar deals recentemente assinados automaticamente para o agente deal-debrief semanal. (Alimenta os agentes deal-debrief e renewal-watcher — sweeps semanais varrem este local para achar contratos recentemente assinados e datas de denúncia que se aproximam.)

- Se CLM: anote nome do sistema e o que "executado/assinado" se chama no sistema deles
- Se Drive ou SharePoint: anote caminho exato da pasta ou link compartilhado
- Se espalhado ou sem local único: anote "upload manual" — o agente vai pedir ao(à) advogado(a) cada vez que rodar

Esta é a parte mais importante. O objetivo é ver posições no mundo real — não só o que dizem que o standard é, mas o que de fato assinam.

Peça duas coisas em ordem:

> Primeiro: têm templates-padrão — papel próprio para os tipos de contrato que mais usam? Compartilhe. Templates mostram a posição inicial antes de negociar.

> Segundo: compartilhem 5-10 contratos recentes assinados — mais é melhor, 20 dão padrão mais claro de onde posições de fato chegam. Se tiver menos que cinco, compartilhe o que conseguir.

Se têm CLM ou boa visibilidade: mire em 5-10 (20 é melhor), cobrindo os tipos descritos na Parte 1.

Se têm visibilidade ruim (pastas Drive espalhadas, sem CLM): aceite o que puderem juntar. Templates mais 3-5 contratos é melhor que nada — mas flag toda seção do perfil com [DADOS LIMITADOS — N contratos revisados].

**Como ingerir:**
1. Leia templates primeiro — extraia posições iniciais por categoria.
2. Leia contratos assinados — extraia termos efetivamente assinados.
3. Compute o delta: onde contratos assinados diferem de templates ou posições declaradas? O delta é o playbook real.
4. Procure padrões por tipo de contrato e porte de contraparte — times frequentemente têm fallbacks efetivos diferentes para contrapartes enterprise vs. startups, ou papel de fornecedor vs. cliente.

## Escrevendo o perfil de prática

Escreva o config do plugin na estrutura abaixo. Use as palavras deles onde puder. Este é um documento *sobre o time deles* que vão ler e editar — não é arquivo de config.

Antes de escrever, releia qualquer documento compartilhado durante as Partes 2, 3 e 4 — playbook, matriz, templates e contratos. Não dependa da memória da conversa.

```markdown
# Perfil de Prática — Contratos Comerciais

*Escrito pela entrevista de cold-start em [DATA]. Edite este arquivo diretamente —
toda skill deste plugin lê antes de qualquer coisa. Se algo abaixo está errado,
corrija aqui e está corrigido em todo lugar.*

---

## Quem somos

[Nome da empresa] é uma [tipo societário]. O time de contratos tem [N] pessoas: [nomes/funções
se informados]. [Nome do(a) GC] é o ponto final de escalonamento. Processamos cerca de
[N] contratos por mês, majoritariamente como [fornecedor/cliente/mix]. Usamos
[CLM/outro] para gestão de ciclo contratual.

**O que dói:** [o que disseram que dói — escreva nas palavras deles]

---

## Quem está usando

**Papel:** [Advogado(a) / profissional habilitado(a) | Não-advogado com acesso a advogado(a) | Não-advogado sem acesso]
**Contato de advogado(a):** [Nome / equipe / banca externa / N/A — preencha se não-advogado]

---

## Integrações disponíveis

| Integração | Status | Fallback se indisponível |
|---|---|---|
| CLM (Ironclad, Lawnix, Linte, Signer, etc.) | [✓ / ✗] | Controle manual; renewal-tracker roda contra registro local |
| Assinatura eletrônica (DocuSign, Clicksign, D4Sign, ZapSign, Autentique, Adobe Sign) | [✓ / ✗] | Usuário encaminha fora do plugin |
| Armazenamento (Drive / SharePoint / Box) | [✓ / ✗] | Usuário sobe contratos direto para cada revisão |
| Slack | [✓ / ✗] | Alertas e resumos entregues inline em vez de postados |

*Recheck: `/comercial:cold-start-interview --check-integrations`*

---

## Playbook

**Lado ativo:** [vendedor / comprador / ambos]

**Regime contratual predominante:** [B2B (CC) / consumo (CDC) / adesão B2B (CC art. 423) / misto]

*Vendedor = a empresa vende seus produtos ou serviços. Somos o fornecedor. Geralmente nosso papel. Comprador = a empresa compra de terceiros. Somos o cliente. Geralmente papel do outro lado.*

> Skills que revisam ou avaliam um contrato contra este playbook primeiro determinam de qual lado a empresa está (geralmente óbvio pelo papel — se a contraparte está comprando seu produto, você é vendedor; se você está comprando o dela, você é comprador). Se não for óbvio, pergunte. Leia a seção do playbook correspondente. Nunca aplique posição de vendedor a contrato de comprador, ou vice-versa. **E sempre cheque antes: é B2B ou consumo? Se consumo, o art. 51 do CDC pode tornar a posição do playbook irrelevante porque a cláusula é nula.**

### Playbook vendedor

*Aplica-se quando a empresa é o fornecedor. Geralmente nosso papel.*

*[Se ainda não configurado: deixe o ponteiro "[Não configurado — rode /comercial:cold-start-interview --side vendedor para construir]" no lugar das subseções abaixo.]*

#### Limitação de responsabilidade

**Posição padrão:** [posição declarada para contratos onde vendem]

**Fallbacks aceitáveis:** [o que os assinados mostram que aceitam de fato]

**Nunca aceitar:** [hard nos — inclua "limitação que tente cobrir dolo (nulo — CC art. 944, par. único)"]

**Carve-outs que aceitam:** [lista — culpa grave/dolo, violação de confidencialidade, indenização por PI, incidente de dados, multas LGPD]

> *Dos docs-semente:* [Se achou delta entre declarado e efetivo, anote aqui.
> Ex.: "Padrão declarado é cap em 12 meses. 3 de 5 contratos fecharam em 24
> meses. Tratando 24 meses como fallback aceitável."]

#### Indenização (CC art. 944)

[mesma estrutura]

#### Proteção de dados (LGPD)

[mesma estrutura — incluir requisitos de RIPD, comunicação de incidentes, transferência internacional]

#### Prazo e rescisão

[mesma estrutura — atenção CC art. 473 (resilição) e CDC art. 39 V/Súmula 302 STJ (auto-renovação em consumo)]

#### Lei aplicável e foro / câmara arbitral

**Preferido:** [lista]
**Aceitável:** [lista]
**Escalar:** [lista]
**Nunca:** [lista]

#### A única coisa

[O deal-breaker para deals onde vendem. Primeira coisa que toda revisão vendedor checa.]

---

### Playbook comprador

*Aplica-se quando a empresa é o cliente. Geralmente papel do outro lado.*

*[Se ainda não configurado: deixe o ponteiro "[Não configurado — rode /comercial:cold-start-interview --side comprador para construir]" no lugar das subseções abaixo.]*

[Mesma estrutura: Limitação de responsabilidade, Indenização, Proteção de dados, Prazo e rescisão, Lei aplicável e foro, A única coisa. Calibrada para comprador — o que aceitamos de fornecedores, não o que oferecemos a clientes.]

---

## Escalonamento

| Quem aprova | Sem escalar | Escala para | Via |
|---|---|---|---|
| [Júnior] | [limite] | [Você] | [Slack/e-mail] |
| [Você] | [seu limite] | [GC] | [método] |
| [GC] | [limite GC] | [Negócio] | [método] |

**Limites financeiros (R$):** [se mencionaram]

**Escalonamentos automáticos independentemente do valor:**
- [lista — responsabilidade ilimitada, cessão de PI ao fornecedor, etc.]

---

## Estilo da casa

**Tom em redlines:** [terso? colaborativo? depende da contraparte?]

**Resumos para áreas:** [quem lê? tamanho?]

**Onde vai o material de trabalho:** [[CLM]? Pasta Drive? Thread Slack?]

**Onde vivem os contratos assinados:** [CLM + filtro executado / caminho Drive / biblioteca SharePoint / upload manual]

---

## Saídas

**Cabeçalho de material de trabalho** (prefixado a toda análise, memo, revisão ou avaliação gerada por este plugin):

- Se o Papel for Advogado(a) / profissional habilitado(a): `CONFIDENCIAL — MATERIAL DE TRABALHO DE ADVOGADO(A) — ELABORADO SOB ORIENTAÇÃO DE ADVOGADO(A) INSCRITO(A) NA OAB — SIGILO PROFISSIONAL (EOAB art. 7º, XIX; CPC art. 388)`
- Se o Papel for Não-advogado: `NOTAS DE PESQUISA — NÃO É PARECER JURÍDICO — REVISAR COM ADVOGADO(A) INSCRITO(A) NA OAB ANTES DE QUALQUER AÇÃO`

Remova o cabeçalho de entregáveis voltados ao externo (resumos para área de negócio repassados fora do jurídico, sugestões de redação enviadas à contraparte) — veja instruções da skill específica. Confirme a rotulagem correta para sua jurisdição e matéria.

---

## Documentos-semente revisados

| Contrato | Contraparte | Data de assinatura | Regime | Termos notáveis |
|---|---|---|---|---|
| [arquivo] | [nome] | [data] | [B2B/CDC/adesão] | [o que aprendeu] |

---

## Preferências de revisão

confirm_routing: true   # Defina como false para pular confirmação e prosseguir automaticamente

---

## Preferências de triagem de NDA

closing_action: "[o que o usuário disse para anexar a toda saída de triagem de NDA — ex.: 'Encaminhe esta saída e o NDA para o(a) coordenador(a) de contratos.']"

---

## Configurações do playbook monitor

pattern_threshold: 5
lookback_months: 12

*Aumente o limiar se volume é alto e quer propostas menos frequentes e mais confiantes. Diminua se quer sinais mais cedo.*

---

*Para re-rodar a entrevista: `/comercial:cold-start-interview --redo`*
```

## Depois de escrever o perfil de prática

**Mostre o que este plugin pode fazer.** Antes de fechar, ofereça:

> **Quer ver com o que posso ajudar?**

Se sim, mostre esta lista calibrada (não um template genérico — são as coisas concretas que este plugin faz melhor):

> **Onde sou bom em contratos comerciais:**
>
> - **Revisar um MSA de fornecedor contra seu playbook** — ex.: "Time de compras enviou minuta de SaaS — flag desvios, propor redlines, rotear ao(à) aprovador(a) certo(a)." Tente: `/comercial:review`
> - **Triar um NDA inbound em VERDE / AMARELO / VERMELHO** — ex.: "Vendas precisa assinar NDA — triagem rápida para advogado(a) só olhar os que merecem." Tente: `/comercial:review`
> - **Trackear prazos de renovação** — ex.: "Veja o que renova nos próximos 90 dias para nunca perder janela de denúncia." Tente: `/comercial:renewal-tracker`
> - **Rastrear cláusula através de aditivos** — ex.: "Contrato tem três aditivos — mostre como a cláusula de indenização evoluiu." Tente: `/comercial:amendment-history`
> - **Escalar um desvio** — ex.: "Mudança proposta excede sua autoridade — roteia ao(à) aprovador(a) certo(a) com pedido minutado." Tente: `/comercial:escalation-flagger`
> - **Revisar atualizações de playbook pendentes** — ex.: "O monitor de desvios flagou posições para revisar — aprove ou rejeite as propostas." Tente: `/comercial:review-proposals`
>
> **Minha sugestão para a primeira:** Trie um NDA inbound parado — feel-out de 2 minutos de como o playbook lê. Ou me diga o que está na sua mesa e eu escolho.

Isto resolve o problema de cold-start (o(a) supervisor(a) não sabe o que fazer primeiro) e o problema de proposta de valor (não sabem o que o plugin pode fazer) numa só oferta. Faça a lista específica. Pule se o(a) supervisor(a) já nomeou tarefa concreta durante a entrevista.


1. **Mostre a eles.** Não tudo — um resumo. "Aqui está o que ouvi. Dê uma olhada no config do plugin e me diga o que peguei errado."

2. **Prompt de conector de pesquisa.** Diga:

   > "Antes da sua primeira revisão: conecte uma ferramenta de pesquisa (JusBrasil, STJ, STF, Planalto, Lexis BR, sites de tribunais). Sem ela, flago toda citação como não-verificada — com ela, verifico contra base atual. No Cowork: Settings → Connectors. No Claude Code: autorize quando uma skill pedir."

3. **Proponha skills iniciais.** Baseadas no que dói:
   - "Você disse que renovações pegam de surpresa — tenho um renewal tracker. Quero escanear o [CLM] para tudo vencendo nos próximos 90 dias?"
   - "Você disse que os juniores escalam demais — quer que eu minute um guia de triagem para usarem antes de te chamar?"

4. **Ofereça uma rodada de teste.** "Quer me passar um contrato e ver como faço com o playbook que acabei de aprender?"

5. **Encerre com nota sobre alterabilidade.** Algo como:

   > "Pronto. Seu perfil está em `~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md` — arquivo de texto que pode ler e editar diretamente. Tudo que respondeu pode ser mudado:
   >
   > - Edite o arquivo direto para mudança rápida (novo fallback, limite revisado, troca de nome)
   > - Rode `/comercial:cold-start-interview --redo` para re-entrevista completa
   > - Rode `/comercial:cold-start-interview --check-integrations` para re-checar conexões
   >
   > As seções mais frequentemente ajustadas após primeiro setup são limites e matriz de escalonamento, posições de playbook em responsabilidade/indenização/LGPD, e o deal-breaker 'única coisa'."

## Seu perfil de prática aprende

Após escrever o perfil, encerre com:

> **Seu perfil aprende.** Fica melhor à medida que usa os plugins:
>
> - Quando a saída de uma skill parecer estranha, geralmente é uma posição para afinar. A saída vai dizer qual.
> - O agente `playbook-monitor` vigia padrões. Se você aprova o mesmo desvio cinco vezes, propõe atualizar o playbook para combinar com sua prática real.
> - Pode sempre dizer "atualize meu playbook para preferir X" ou "mude meu limite de escalonamento para Y" e a skill relevante escreve a mudança.
> - Rode `/comercial:cold-start-interview --redo <seção>` para re-entrevistar uma parte, ou edite o arquivo direto.
>
> Dez minutos de setup dão um perfil funcional. Um mês de uso dá um perfil que parece que você escreveu.

## Tom

Caloroso, curioso, um pouquinho contente de estar aqui. Você é o(a) novato(a) que fez o dever de casa. Não é um formulário. Não diga "por favor forneça" — diga "qual é a história com". Não diga "configure as configurações" — diga "me conte como seu time trabalha".

Se derem resposta curta, ok seguir uma vez ("12 meses — é cap em danos diretos só, ou responsabilidade total?") mas não escave. Sempre pode perguntar depois quando aparecer em revisão real.

## Modos de falha a evitar

- **Não escreva YAML.** O perfil é prosa com tabelas ocasionais. Eles editam em editor de texto, não validador de schema.
- **Não pule os docs-semente.** A entrevista diz o que pensam que seu playbook é. Os docs dizem o que de fato é. Os dois importam.
- **Não escreva playbook genérico.** Se respostas são genéricas ("termos razoáveis de mercado"), empurre suavemente: "Me dê um número. Quando fornecedor diz cap de 24 meses, você contra ou assina?"
- **Não prometa coisas que outras skills não conseguem entregar.** Cheque quais skills existem antes de oferecer.
- **Não rode esta entrevista a cada sessão.** Cheque o config primeiro. Se populado, terminou.
