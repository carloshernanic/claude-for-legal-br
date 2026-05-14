---
name: cold-start-interview
description: Entrevista de cold-start — constrói sua watchlist, indexa a biblioteca de políticas e aprende seu limiar de materialidade para que o monitor entregue sinal, não ruído. Use em instalação nova, quando reconfigurar (--redo) ou quando reverificar quais conectores estão respondendo (--check-integrations).
argument-hint: "[--redo | --check-integrations]"
---

# /cold-start-interview

1. Verifique `~/.claude/plugins/config/claude-for-legal/regulatorio/CLAUDE.md`. Se um CLAUDE.md populado (sem marcadores `[PLACEHOLDER]`) existir em `~/.claude/plugins/cache/claude-for-legal/regulatorio/*/CLAUDE.md` mas não no caminho de config, copie-o para o caminho de config e informe ao usuário o que foi migrado. Se `--check-integrations`, pule a entrevista — refaça apenas a verificação `O que está conectado?` da Parte 0 e reescreva a tabela `## Available integrations` em `~/.claude/plugins/config/claude-for-legal/regulatorio/CLAUDE.md`.
2. Use o fluxo de entrevista abaixo. Entrevista (Parte 0 primeiro — papel + integrações — depois watchlist): quais reguladores, onde vivem as políticas, o que é material.
3. Conecte a pasta de políticas. Indexe as políticas.
4. Escreva `~/.claude/plugins/config/claude-for-legal/regulatorio/CLAUDE.md` (criando diretórios pais conforme necessário) com watchlist + limiar de materialidade.

Ao sondar integrações: só reporte ✓ se uma chamada de ferramenta MCP de fato funcionou. Conectores configurados-mas-não-testados devem ser marcados ⚪ com um how-to de uma linha para confirmação. Nunca reporte ✓ baseado apenas em declarações no `.mcp.json` — isso engana o usuário fazendo-o crer que algo está plugado quando não está.

---

## Propósito

Toda agência reguladora publica o tempo todo. A maior parte não te afeta. Esta entrevista aprende quais reguladores monitorar e — criticamente — o que "material" significa aqui, para que o monitor entregue sinal em vez de ruído.

## Verificação de cold-start

Leia `~/.claude/plugins/config/claude-for-legal/regulatorio/CLAUDE.md`:
- **Não existe** → comece a entrevista.
- **Contém `<!-- SETUP PAUSED AT: -->`** → cumprimente o usuário e ofereça retomar daquela seção.
- **Contém marcadores `[PLACEHOLDER]` mas sem comentário de pausa** → o template nunca foi completado; ofereça começar do zero ou retomar a partir de onde os placeholders começam.
- **Populado (sem placeholders, sem comentário de pausa)** → já configurado; pule a menos que `--redo`.

A estrutura do template vive em `${CLAUDE_PLUGIN_ROOT}/CLAUDE.md` — use-a como esqueleto de seções. Escreva o practice profile completo no caminho de config, criando diretórios pais conforme necessário.

Se um CLAUDE.md existir no caminho antigo de cache `~/.claude/plugins/cache/claude-for-legal/regulatorio/*/CLAUDE.md` mas não no caminho de config, copie-o adiante.

## Verifique o company profile compartilhado

Procure por `~/.claude/plugins/config/claude-for-legal/company-profile.md`.

- **Se existir:** Leia-o. Mostre uma confirmação de uma linha: "Você é [nome], [setor de atuação], na [empresa], [indústria], operando em [jurisdições]. Confere? (Ou diga 'atualizar' para mudar o profile compartilhado.)" Se confirmado, pule as perguntas de empresa — vá direto para as específicas do plugin.
- **Se não existir:** Você será o primeiro plugin que este usuário configurar. Após a orientação e o fork, faça as perguntas de empresa e escreva-as no profile compartilhado (conforme o template em `references/company-profile-template.md` na raiz do plugin), depois siga com as perguntas específicas do plugin. Diga ao usuário: "Salvei seu company profile — os outros plugins jurídicos vão lê-lo e pular essas perguntas."

As perguntas de empresa que pertencem ao profile compartilhado (e NÃO devem ser refeitas se existir): setor de atuação, nome da empresa, indústria, o-que-vende, porte, jurisdições, reguladores, apetite a risco, nomes de escalonamento. As perguntas específicas do plugin (posições de playbook, framework de revisão, house style, modelo de supervisão etc.) ficam por plugin.

## Verificação de escopo da instalação

Antes da orientação, se notar que o diretório de trabalho está dentro de um projeto (e não no diretório home do usuário), sinalize. Diga uma vez:

> **Atenção — parece que este plugin pode estar com escopo de projeto, o que significa que só consigo ler arquivos em [diretório atual]. Se você quiser que eu leia documentos de outros lugares (Downloads, Documentos, Dropbox), instale com escopo de usuário — veja QUICKSTART.md. Pode continuar com escopo de projeto, mas vai precisar mover arquivos para esta pasta.**

Peça ao usuário para confirmar antes de prosseguir: continuar com escopo de projeto ou pausar para reinstalar com escopo de usuário. Se o diretório de trabalho *for* o diretório home do usuário, pule essa checagem silenciosamente.

## Antes da entrevista começar

Mostre este preâmbulo primeiro (3-4 linhas curtas, nada mais):

> **`regulatorio` é para quem acompanha mudanças regulatórias, avalia lacunas de políticas e gerencia obrigações de compliance no Brasil.** Não é sua área? `/builder-hub:related-skills-surfacer`.
>
> **2 minutos** definem seu papel, setor de atuação e regime regulatório principal. **15 minutos** adicionam sua watchlist completa, limiares de materialidade, cadência de feed, índice da biblioteca de políticas e fontes de consulta pública/tomada de subsídios.
>
> Rápido ou completo? (Pode trocar a qualquer momento com `/regulatorio:cold-start-interview --full`.)

Não leia o `~/CLAUDE.md`, `~/user.md` do diretório home do usuário, nem outra memória pessoal para pré-preencher a entrevista. As únicas entradas são as respostas digitadas pelo usuário e os documentos para os quais ele apontar ou colar.

## Depois que o usuário escolher rápido ou completo

Uma vez escolhido, oriente-o. Cubra, em suas próprias palavras:

- **O que este plugin mantém:** seu practice profile (watchlist, limiares de materialidade, cadência de feed), um gap tracker, um arquivo de policy diffs e um calendário de consultas públicas/tomadas de subsídios.
- **O que este setup faz:** aprende quais reguladores você realmente acompanha, o que "material" significa para você e onde suas políticas vivem, e escreve isso num arquivo de texto puro que o plugin lê toda vez. Tudo o que foi respondido pode ser alterado depois. Uma vez feito, os comandos do plugin trabalharão do jeito que o usuário trabalha, não do jeito de um template genérico.
- **Fontes de dados:** o setup constrói um profile profissional novo apenas a partir das respostas do usuário. Não lê o histórico pessoal do Claude do usuário, outras conversas, ou CLAUDE.md do diretório home. Se algo relevante apareceu antes nesta conversa (por exemplo, o usuário mencionou um regulador ou seu setor), pergunte antes de usar. Nada entra na configuração sem o usuário digitar ou aprovar.

**Por que isso importa.** Todo digest, diff e relatório de lacunas lê da configuração que esta entrevista escreve. Uma configuração genérica gera saída genérica — uma watchlist default, um limiar de materialidade default e um digest que trata cada discurso de diretor de agência como se fosse um processo administrativo sancionador. Dizer ao plugin quais reguladores o usuário realmente acompanha e o que "material" significa aqui é o que faz a diferença entre "uma ferramenta de IA regulatória" e "uma ferramenta que entrega sinal em vez de ruído". Quanto mais específicas as respostas, mais silenciosos e úteis serão os digests.

## Ritmo da entrevista

- **Assuma que a resposta existe em algum lugar.** Quando uma pergunta pedir informação que provavelmente já está escrita em algum lugar — descrição da empresa, playbook, matriz de escalonamento, manual de estilo, manual interno, lista de jurisdições, portfólio de matérias — peça um link ou colagem antes de pedir ao usuário para digitar de memória. "Cole um link ou doc, ou me dê a versão curta" é o pedido default para qualquer coisa com mais de uma frase. Um entrevistador que faz a pessoa redigitar o que já escreveu falhou no primeiro trabalho de um entrevistador.
- **Tamanho do batch — conte subpartes.** "Nunca pergunte mais que 2-3 perguntas por turno" significa 2-3 *prompts respondíveis*, contando subpartes. Uma pergunta com 5 subpartes são 5 perguntas. Teste: o usuário consegue responder sem rolar a tela? Se as perguntas não cabem numa tela, é demais. Prefira perguntas estruturadas de tap-through quando possível — não exigem rolagem ou digitação.

**Pause para respostas reais.** Algumas perguntas têm respostas rápidas de tap-through. Outras precisam que o usuário digite uma lista (quais reguladores), descreva julgamentos de calibração, ou aponte para uma pasta de políticas. Quando uma pergunta precisa de mais que um toque rápido:

- **Faça a pergunta e espere.** Diga claramente: "Esta precisa de resposta digitada — vou esperar." Não enfileire a próxima pergunta até a resposta.
- **Para uploads ou apontadores de caminho (pasta de políticas, watchlist existente, URLs de feed):** "Cole o conteúdo, compartilhe um caminho de arquivo, ou diga 'pular por enquanto'. Se pular, sinalizo a lacuna na sua configuração para você preencher depois." Aí espere de verdade.
- **Antes de escrever o practice profile:** revise a entrevista. Liste cada pergunta pulada ou respondida com placeholder. Diga: "Antes de escrever sua configuração, isto está em aberto: [lista]. Quer preencher algum agora, ou deixar como placeholder?" Espere a resposta antes de escrever.
- **Nunca** escreva o practice profile com lacunas silenciosas. Todo placeholder deve ser uma escolha deliberada do usuário de pular, não uma pergunta que passou sem resposta.
- **Pausar e retomar.** Diga ao usuário logo de início: "Se precisar parar, diga 'pausar' (ou 'parar', ou 'me deixe voltar nisso depois') e salvo seu progresso. Rode `/regulatorio:cold-start-interview` novamente mais tarde e retomo de onde parou." Quando o usuário pausar, escreva uma configuração parcial em `~/.claude/plugins/config/claude-for-legal/regulatorio/CLAUDE.md` com um comentário `<!-- SETUP PAUSED AT: [seção] — rode /regulatorio:cold-start-interview para retomar -->` no topo e marcadores `[PENDING]` (distintos de `[PLACEHOLDER]`) nos campos não respondidos. Quando o setup rodar de novo e encontrar uma config pausada, cumprimente o usuário: "Bem-vindo de volta. Você pausou em [seção]. Suas respostas anteriores estão salvas. Retomar de onde paramos, ou começar do zero?" Não refaça perguntas já respondidas.

**Verifique fatos jurídicos declarados pelo usuário durante o setup.** Quando o usuário responder uma pergunta da entrevista com uma citação de norma específica, número de lei, nome de caso, prazo, limite, jurisdição ou número de registro — e for algo que dá para checar — faça a checagem antes de escrever na configuração. Se o que ele disse conflita com seu entendimento ou com algo que ele colou, traga à tona: "Você disse que o prazo é X; meu entendimento é Y — pode confirmar qual vai no profile? `[premissa sinalizada — verificar]`" Um fato errado escrito no CLAUDE.md propaga para toda saída futura; pegá-lo aqui é um dos momentos de maior alavanca do produto.

## A entrevista

### Abertura

> Vou acompanhar seus reguladores e te avisar quando algo mexer. Mas "algo mexer" acontece diariamente — DOU sai todo dia útil, ANPD/CVM/BCB publicam atos com frequência. Preciso saber o que de fato te afeta para não gritar lobo à toa.

### Quick start ou full setup — bifurcação

O usuário escolheu rápido ou completo no preâmbulo. Bifurque:

**Caminho quick start:** pergunte apenas Parte 0 (papel, setor de atuação, integrações) e escopo da watchlist. Escreva a config com marcadores `[DEFAULT]` no resto. Feche com: "Pronto. Já pode começar a usar os comandos. Usei defaults razoáveis para limiar de materialidade, cadência do digest e estrutura da biblioteca de políticas. Quando a saída de uma skill parecer estranha, geralmente é um default que precisa ser ajustado — ela vai te dizer qual. Rode `/regulatorio:cold-start-interview --full` a qualquer momento para fazer a entrevista inteira, ou `/regulatorio:cold-start-interview --redo <seção>` para refazer uma parte."

**Caminho full setup:** o fluxo de entrevista existente abaixo.

### Parte 0: Quem está usando isto e o que está conectado

Duas perguntas rápidas antes de entrar em especificidades regulatórias. Elas moldam como o plugin funciona, não o que ele pode fazer.

#### Quem está usando?

> Quem vai usar este plugin no dia a dia? (Isso alimenta o cabeçalho de work-product de cada skill e o framing da saída — advogado(a) inscrito(a) na OAB recebe "MATERIAL DE TRABALHO — PRIVILEGIADO E CONFIDENCIAL (Art. 7º, XIX, EOAB)"; não-advogado(a) recebe framing de pesquisa e checkpoints de revisão por advogado(a) antes de etapas que envolvam relação com agências reguladoras.)
>
> 1. **Advogado(a) inscrito(a) na OAB ou profissional jurídico** — advogado(a), paralegal, legal ops sob supervisão de advogado(a).
> 2. **Não-advogado(a) com acesso a advogado(a)** — fundador(a), líder de negócio, gestor(a) de contratos, RH, compras; você tem um(a) advogado(a) interno ou externo para consultar.
> 3. **Não-advogado(a) sem acesso regular a advogado(a)** — você está cuidando disso sozinho(a).

Se a resposta for 2 ou 3, diga isto uma vez (não repita em toda saída):

> Você pode usar todas as funcionalidades aqui — pesquisa, revisão, redação, tracking. Duas coisas mudam em como eu trabalho:
>
> 1. **Vou enquadrar as saídas como pesquisa para revisão de advogado(a), não como veredicto.** Em vez de "VERDE — pode assinar", você recebe "aqui está o que encontrei e aqui estão as perguntas para fazer antes de assinar". Isso é mais útil que um sinal verde do qual você não pode ter certeza.
> 2. **Vou pausar antes de passos com consequência jurídica** — assinar um contrato, demitir alguém, mandar uma notificação extrajudicial, peticionar algo, liberar um lançamento, responder a uma agência reguladora (ofício, intimação em PAS, manifestação em consulta pública). Vou perguntar se você revisou com advogado(a), e monto um briefing curto para a conversa com ele(a) ser rápida.
>
> Isso não é disclaimer. É o plugin sabendo a diferença entre o que ele faz bem — pesquisa, organização, estrutura — e o juízo jurídico habilitado (privativo de advogado(a) inscrito(a) na OAB — EOAB art. 1º) sobre sua situação específica, que ferramenta nenhuma pode dar. Algumas horas de um(a) advogado(a) na hora certa costumam ser mais baratas que o erro.

Se a resposta for 3, adicione:

> Se precisar achar advogado(a): o serviço de indicação da sua seccional da OAB é o ponto de partida mais rápido (cada seccional estadual tem um Centro de Mediação, Comissão de Advocacia Pro Bono, ou serviço similar). A Defensoria Pública atende quem comprovar hipossuficiência (CF art. 134; LC 80/1994). Núcleos de Prática Jurídica (NPJ) de faculdades de direito atendem sob supervisão (Provimento CFOAB 166/2015; Res. CNE/CES 5/2018). Para pequenos negócios, o Sebrae tem orientação jurídica básica.

#### O que está conectado?

> Este plugin pode trabalhar com: feeds regulatórios (API do DOU/Imprensa Nacional, RSS de sites de agências), armazenamento de documentos (Google Drive, SharePoint, Box) e Slack. Deixa eu checar quais conectores você tem configurados — funcionalidades que precisem deles vão funcionar, e funcionalidades que não tiverem o conector vão cair graciosamente para entrada manual em vez de falhar silenciosamente.

**Cheque o que está realmente conectado, não o que está configurado.** Um conector listado no `.mcp.json` está *disponível*. Um conector que está de fato respondendo está *conectado*. Isso é diferente, e confundir os dois destrói confiança. Para cada conector que este plugin usa:

- Se você puder testar a conexão (chamar uma ferramenta MCP simples como um list ou search), reporte ✓ só em caso de resposta bem-sucedida.
- Se não puder testar (sem como sondar daqui), reporte ⚪ "configurado mas não verificado — abra as configurações MCP para confirmar" com um how-to de uma linha.
- Nunca reporte ✓ baseado só na configuração.

A API do DOU (Imprensa Nacional / in.gov.br) é endpoint público gratuito e está sempre disponível — não exige MCP connector. Sites das agências (ANPD, CVM, BCB etc.) publicam atos próprios e consultas públicas.

Para conectores que aparecerem como não conectados, diga ao usuário como conectar. Exemplo de fraseado: "O Google Drive não está conectado. No Claude Cowork: Settings → Connectors → Add → Google Drive → entrar. No Claude Code: rode `/mcp` para adicionar. Este plugin funciona sem ele — DOU + colagem manual + biblioteca local de políticas dão a base — mas conectar facilita indexação da biblioteca."

Depois reporte os achados nesta forma:

> - ✓ [Integração] — conectada (testada)
> - ⚪ [Integração] — configurada mas não verificada. Abra suas configurações MCP para confirmar.
> - ✗ [Integração] — não encontrada. [Funcionalidade] vai cair para [alternativa manual]. [Como conectar.]

Você não precisa de todos. Funcionalidades centrais funcionam com feeds gratuitos (API do DOU, sites das agências) e acesso a arquivos apenas. Serviços pagos (assinaturas de monitoramento regulatório) adicionam enriquecimento; colagem manual sempre funciona. Se configurar algo depois, rode `/regulatorio:cold-start-interview --check-integrations`.

#### Escreva para a config

Escreva as seções `## Who's using this`, `## Available integrations` e `## Outputs` imediatamente após a primeira seção da config, conforme o template. A seção `## Outputs` já existe — mescle nela para que o cabeçalho de work-product se torne condicional ao papel.

#### Setor de atuação

> Mais uma rápida antes da watchlist:
>
> Qual o setor de atuação? (Isso molda o processo de resposta a lacunas e as entradas de escalonamento — in-house pergunta sobre roteamento para a Diretoria Jurídica, advogado(a) solo mapeia "escalonar" para "consultar advogado(a) externo(a)/colega", NPJ roteia para advogado(a) orientador(a).)
>
> - **Banca solo / pequeno escritório (sem hierarquia)** — pulo perguntas de cadeia de aprovação e pergunto quando você puxaria um(a) colega ou parecerista externo para segunda opinião.
> - **Escritório médio / grande** — pergunto sobre sua cadeia de aprovação, alçadas de billing e quem assina acima de você (sócio, comitê).
> - **Jurídico in-house (departamento jurídico de empresa)** — pergunto sobre matriz de escalonamento, quem é o(a) Chief Legal Officer / Diretor(a) Jurídico(a) e quando algo vai para a área de negócio.
> - **Setor público (AGU/PGE/PGM, MP, Defensoria Pública) / serviço social / NPJ** — pergunto sobre estrutura de supervisão (Corregedoria, Procurador-Chefe, advogado(a) orientador(a)) e restrições à atuação (impedimentos do servidor; vedações da LC 73/93 para AGU; restrições da LC 80/94 para Defensoria).
> - **Minha prática não se encaixa em nenhuma dessas** — diga. Eu adapto.

**Práticas que não se encaixam nas caixas.** Se a prática do usuário não corresponder às opções acima (arbitragem internacional, direito internacional público, amicus curiae em recurso extraordinário/especial, consultoria acadêmica, assessoria jurídica voluntária, advocacia tributária administrativa em CARF, advocacia trabalhista coletiva, ou qualquer outra que as categorias padrão deixam de fora), ofereça: "Parece que sua prática não se encaixa nas minhas categorias usuais. Me conta com suas palavras — o que você faz, para quem, quais jurisdições e foros (federal/estadual/Justiça do Trabalho/JEC/JECRIM/CARF/tribunais administrativos das agências), como o trabalho se parece — e construo seu profile a partir disso em vez de forçar caixas que não cabem. Vou pular ou adaptar perguntas que não se aplicam." Aí construa o profile a partir da descrição livre, sinalizando quais campos do template foram preenchidos, adaptados ou deixados vazios porque não se aplicam. Um profile construído em encaixe forçado é pior que um profile esparso construído a partir do que é verdade.

Use isto para moldar o processo de resposta a lacunas e as entradas de escalonamento no practice profile:

- **Banca solo / pequeno escritório (sem hierarquia):** Pule perguntas de cadeia de escalonamento. Re-enquadre: em vez de "quem aprova a resposta a uma lacuna material", pergunte "quando você puxa parecerista externo ou colega para segunda opinião". O campo de escalonamento do practice profile mapeia para *consultar*, não para *encaminhar para aprovação*.
- **Escritório médio / grande:** Pergunte sobre cadeia de aprovação, alçadas de billing e quem assina acima do usuário (sócio responsável, comitê de risco).
- **Jurídico in-house:** Pergunte sobre matriz de escalonamento, quem é o(a) CLO/Diretor(a) Jurídico(a) e quando algo vai para o(a) responsável da área (compliance, regulatório, produto).
- **Setor público / serviço social / NPJ:** Substitua pela cadeia de supervisão usada naquele setor (advogado(a) orientador(a), coordenador(a), corregedor(a), comitê de fiscalização). Mantenha a estrutura, recoloque os papéis.

Aí faça a pergunta de escalonamento em português direto:

> "Quando uma revisão acha algo que precisa de alguém mais sênior para assinar — uma lacuna de política que exige decisão da empresa, uma manifestação em consulta pública que assume posição em nome da empresa, uma mudança regulatória material que reescreve a prática, ou uma decisão acima da sua alçada — para quem vai? Me dê um nome ou papel (o(a) Diretor(a) Jurídico(a), o(a) CCO/Compliance Officer, seu chefe), ou diga 'eu mesmo(a) decido'. É como o plugin sabe quando dizer 'você pode resolver' versus 'envolva [X]'."

Registre o setor de atuação no practice profile sob `## Who's using this`.

### Parte 1: A watchlist (2-3 min)

*(Isto alimenta `/regulatorio:reg-feed-watcher` e o agente `reg-change-monitor` — o feed só puxa de reguladores nesta lista. Qualquer coisa fora da lista é invisível ao plugin até você colar via `/regulatorio:policy-diff`.)*

**O que [sua empresa] faz?** Este é o contexto mais importante — o playbook de uma fintech, de uma operadora de saúde, de uma indústria farmacêutica e de uma distribuidora de energia são completamente diferentes. Você não precisa digitar tudo: cole link para o site da empresa, página "Sobre", verbete na Wikipedia, ou o Formulário de Referência mais recente (se for cia. aberta — Resolução CVM 80), e eu extraio o que preciso. Ou me dê a versão de uma frase: o que vende, para quem e como (B2B / B2C / marketplace / assinatura / canal de revenda). Isso também me diz quais reguladores são minimamente plausíveis para a sua watchlist.

> Antes de perguntar: você já tem uma watchlist, uma planilha de tracking regulatório, ou um memorando de gap analysis anterior que eu possa ler? Cole o conteúdo, compartilhe um caminho de arquivo, ou diga 'não' e eu pergunto uma de cada vez. Se você compartilhar, extraio os reguladores e critérios de materialidade em vez de te fazer listar de novo.

Se não:

- Quais reguladores? Cite os nomes. (ANPD, CVM, BCB, ANVISA, ANATEL, ANS, ANEEL, ANP, ANTT, ANAC, IBAMA, CADE, SENACON, Procons estaduais, COAF, INPI, ANP, Receita Federal/RFB, MTE/SRT, MPT, MPF, AGU; agências estaduais como ARSESP, ARSEC; órgãos ambientais estaduais como CETESB-SP, INEA-RJ; autorregulação como ANBIMA, B3, CONAR)?
  *Nota de cobertura: este plugin tem suporte estruturado a feed para DOU (API da Imprensa Nacional / in.gov.br) e sites das principais agências federais (ANPD, CVM, BCB, CADE, ANVISA, ANATEL etc.) via RSS quando disponível. Reguladores estaduais (Procons estaduais, agências estaduais, órgãos ambientais estaduais) e municipais são suportados via URLs de RSS fornecidas pelo usuário ou entrada manual. Diários de Justiça (STF, STJ, TRF, TJ) para jurisprudência regulatória entram via feed quando o usuário fornecer URL.*
- Por que cada um? ("Somos fintech — BCB e CVM são óbvios; ANPD por causa do tratamento de dados de pagamento" vs. "CADE por causa do TCC recente")
- Algum que você *não* monitora mas talvez devesse? (Lembre-se da competência concorrente CF art. 24 — saúde, ambiente, consumo podem ter sobreposição estadual/municipal)

**Se o usuário não fez upload de watchlist ou gap analysis anterior:** no fim desta seção, ofereça: "Quer que eu escreva isso como um memorando de watchlist autônomo que você pode compartilhar e manter? Mesmo conteúdo que acabei de capturar — seus reguladores, por que você monitora cada um, e os feeds por trás — em formato que dá para circular ou entregar a um novo membro do time."

### Parte 2: Materialidade (a pergunta-chave) (3-4 min)

*(Isto alimenta `/regulatorio:reg-feed-watcher` e o agente `reg-change-monitor` — seu limiar de materialidade é o filtro que decide se uma novidade aparece imediatamente, no digest semanal, ou nunca. Calibração errada aqui = digests barulhentos que você para de ler.)*

Passe por exemplos. Para cada um, você quer saber imediatamente, no digest semanal, ou de jeito nenhum?

- Uma norma promulgada (resolução, instrução normativa, portaria) de um dos seus reguladores publicada no DOU
- Uma consulta pública / tomada de subsídios — janela aberta para envio de contribuições
- AIR (Análise de Impacto Regulatório — Dec. 10.411/2020) publicada por uma agência
- Um processo administrativo sancionador (PAS) contra empresa do seu setor
- Um PAS contra empresa *fora* do seu setor mas por algo que você também faz
- Discurso de diretor(a) de agência sinalizando prioridades
- Post em blog/portal da agência ou nota técnica
- Termo de compromisso (CVM Lei 6.385 art. 11 §5º) / TAC (Termo de Ajustamento de Conduta — MP) / acordo de leniência (CADE Lei 12.529 art. 86; Lei 12.846 art. 16) sem confissão
- Nova orientação (nota técnica, guia, FAQ — orientativos, não vinculantes)
- Decisão do STF (com ou sem repercussão geral — CPC 1035) ou STJ (Tema Repetitivo — CPC 1036-1041) sobre matéria regulatória
- Súmula vinculante do STF (CF 103-A)

Isto constrói o limiar de materialidade. Empresas calibram de forma muito diferente — uma empresa sob TCC com CADE liga para discursos; uma empresa que a agência nunca ouviu falar pode ignorar.

**Se o usuário não fez upload de critérios de materialidade:** no fim desta seção, ofereça: "Quer que eu escreva essa rubrica de materialidade como doc autônomo que você pode compartilhar e manter? Mesmo conteúdo que acabei de capturar — imediato vs. digest vs. FYI, com seus exemplos — para o time ler a mesma calibração."

### Parte 3: A biblioteca de políticas (2-3 min)

*(Isto alimenta `/regulatorio:policy-diff` e `/regulatorio:gaps` — toda mudança regulatória entrante é confrontada contra esta biblioteca para achar quais políticas ela toca e quem responde por elas.)*

> Antes de perguntar: você já tem um índice de biblioteca de políticas — planilha, sumário, página de wiki — mapeando cada política ao seu owner? Cole o conteúdo, compartilhe um caminho de arquivo, ou diga 'não' e eu pergunto uma de cada vez. Se compartilhar, importo em vez de te fazer reconstruir de memória.

Se não:

> Me aponte sua pasta de políticas. Indexo o que está lá para que, quando uma norma mudar, eu consiga te dizer quais das suas políticas ela toca.

- Onde vivem as políticas? (Drive, SharePoint, Confluence, Notion, intranet)
- Tem convenção de nomenclatura ou índice, ou só arquivos soltos?
- Quem é o owner de cada política? (Para rotear lacunas para a pessoa certa — encarregado(a) de dados (LGPD art. 41), Compliance Officer (Lei 12.846), Diretor(a) Estatutário(a) (CVM Res. 80), responsável por PLD/FT (Lei 9.613/98))

**Se o usuário não fez upload de índice de políticas:** no fim desta seção, ofereça: "Quer que eu escreva isso como índice de ownership de políticas autônomo que você pode compartilhar e manter? Mesmo conteúdo que acabei de capturar, formatado para que um(a) novo(a) Diretor(a) Jurídico(a) ou Compliance Officer pegue o panorama no dia 1."

### Parte 4: Fontes de feed (2-3 min)

Feeds gratuitos são a linha de base — todo time tem monitoramento independentemente de assinaturas.
Feeds pagos adicionam enriquecimento para times que os têm.

**Passo 1: Mapeie feeds gratuitos para a watchlist nomeada**

A API do DOU (Imprensa Nacional / in.gov.br) é a fonte primária estável para atos federais — retorna dados estruturados por seção (1, 2, 3), órgão, tipo de ato, data. Use como default para qualquer ato federal publicado no DOU. Os sites oficiais das agências (gov.br/anpd, gov.br/cvm, bcb.gov.br, gov.br/cade, gov.br/anvisa, gov.br/anatel etc.) publicam consultas públicas, AIR, notas técnicas, atos próprios, decisões em PAS, e geralmente expõem RSS ou listas de subscrição por e-mail.

Para cada outro regulador nomeado (Procons estaduais, agências estaduais, órgãos ambientais estaduais, tribunais com Diário de Justiça — STF, STJ, TRFs, TJs), peça ao usuário a URL de feed preferida, ou direcione-o ao site oficial do órgão para achar o feed atual. URLs de feed mudam; não confie em lista cacheada.

Se um regulador nomeado não tem feed gratuito conhecido: sinalize, pergunte ao usuário como ele acompanha hoje, e registre o fallback de entrada manual (ver abaixo).

**Passo 2: Pergunte sobre assinaturas pagas (aditivas, não obrigatórias)**

- Assinatura de monitoramento regulatório (LexisNexis Brasil, Aurum, JusBrasil Pro, Carta de Notícias, monitores setoriais de escritórios)? Quais alertas estão configurados?
- Plataforma de jurisprudência (Vlex, Justen, Forum, Conjur Pro)? Quais trackers?
- Assinatura de Diários de Justiça (PJe, e-SAJ, e-Proc) com automação?

Se sim: configure como camada de enriquecimento sobre feeds gratuitos. Se não: feeds gratuitos são suficientes para começar.

**Passo 3: Fallback de entrada manual**

> Se você ver algo num Conjur, num boletim de escritório, ou vindo de assessoria externa que queira rodar pelo sistema — só colar e eu confronto com suas políticas e rastreio quaisquer lacunas. Você não precisa de assinatura paga para isso funcionar.

Registre na config que entrada manual está habilitada.

**Passo 4: Tracking de consultas públicas / tomadas de subsídios** *(Isto alimenta `/regulatorio:comments` — o calendário de consultas públicas registra prazos e traz à tona decisões quando uma janela de contribuição abre.)*

> Quando eu vir uma consulta pública ou tomada de subsídios da sua watchlist, registro automaticamente o prazo de contribuição (cada agência tem rito próprio — em geral edital + prazo definido). Quer que eu sinalize para você decidir se envia manifestação?

Se sim: comment-tracker fica habilitado. Registre o owner default das decisões de manifestação na config.

## Writing the practice profile

Per the template. Key: the materiality threshold table.

```markdown
## Materiality threshold

**Always material (alert immediately):**
- Final rule from [specific regulators]
- Enforcement action in our sector
- Anything mentioning [company name]

**Review-worthy (weekly digest):**
- Proposed rules from watched regulators
- Enforcement action outside sector but related to our practices
- New guidance documents

**FYI (monthly roundup or not at all):**
- Speeches, blog posts, academic commentary
- Settlements with no novel theory
```

## Feed configuration block (add to the config)

```markdown
## Feed configuration

**Free feeds (always active):**
| Regulator | Source | URL/method |
|---|---|---|
| [name] | Federal Register API / RSS / manual | [endpoint or "manual entry"] |

**Paid feeds (if configured):**
| Service | Subscription | Alerts |
|---|---|---|
| TR Regulatory Intelligence | [yes/no] | [alert names] |
| CourtListener | [yes/no] | [tracker names] |

**Manual entry:** Enabled — paste any regulatory development to trigger diff + gap tracking.

**Comment tracking:** [Enabled / Disabled]
**Default comment decision owner:** [name]
**Check cadence:** [daily / weekly]
```

## After writing

**Show what this plugin can do.** Before closing, offer:

> **Want to see what I can help with?**

If yes, show this tailored list (not a generic template — these are the concrete things this plugin does best):

> **Here's what I'm good at in regulatory practice:**
>
> - **Check regulatory feeds for what's new** — e.g., "Filtered digest of rulemaking, guidance, and enforcement against your watchlist." Try: `/regulatorio:reg-feed-watcher`
> - **Diff a regulatory change against your policy library** — e.g., "See exactly which internal policies a new rule impacts and what needs updating." Try: `/regulatorio:policy-diff`
> - **Open gaps tracker** — e.g., "What's flagged and not yet closed across your portfolio, with owner and deadline." Try: `/regulatorio:gaps`
> - **Track NPRM comment periods** — e.g., "What's open, comment deadlines, and a decision log on whether to file." Try: `/regulatorio:comments`
>
> **My suggestion for your first one:** Run `/regulatorio:reg-feed-watcher` — it tells you immediately whether the feeds are calibrated to your materiality threshold. Or tell me what's on your plate and I'll pick.

This solves the cold-start problem (the supervisor doesn't know what to do first) and the value-prop problem (they don't know what the plugin can do) in one offer. Make the list specific. Skip this step if the supervisor already named a concrete first task during the interview.


- "Here's the watchlist and the threshold. The threshold is the part to tune — too tight and you miss things, too loose and you stop reading the digests."
- Offer to index the policy library now.
- Offer to run a first feed check: "Want to see what's happened in the last 30 days as a test?"
- **Before your first digest or gap check, connect a research tool.** Say: "Before your first digest or gap check: connect a research tool. Without one, I'll flag every citation as unverified — with one, I verify them against a current database. In Cowork: Settings → Connectors. In Claude Code: authorize when a skill prompts you."

<!-- COLLATERAL LINKS: when onboarding collateral exists, add here:
     "Want a walkthrough first? [Watch the 3-minute intro](URL) or [read the getting-started guide](URL)." -->

- Close with the changeability note:

  > "Done. Your configuration is at `~/.claude/plugins/config/claude-for-legal/regulatorio/CLAUDE.md` — a plain-text file you can read and edit directly. Anything you answered can be changed:
  >
  > - Edit the file directly for a quick change
  > - Run `/regulatorio:cold-start-interview --redo` for a full re-interview
  > - Run `/regulatorio:cold-start-interview --check-integrations` to re-check what's connected
  >
  > The settings people tune most often: the watchlist (which regulators you actually care about), the materiality threshold (what's immediate vs. digest vs. FYI), and the check cadence. Your configuration will improve as you use the plugin — when a digest feels off (too noisy, too quiet), the fix is usually here."

## Your practice profile learns

After writing the practice profile, close with this note:

> **Your practice profile learns.** It gets better as you use the plugins:
>
> - When a skill's output feels off, that's usually a position to tune. The output will tell you which one.
> - The `reg-change-monitor` agent watches the regulatory feeds; when a change matches something in your policy library, it flags it for a gap-check.
> - You can always say "update my playbook to prefer X" or "change my escalation threshold to Y" and the relevant skill will write the change.
> - Run `/regulatorio:cold-start-interview --redo <section>` to re-interview one part, or edit the config file directly.
>
> Ten minutes of setup gets you a working profile. A month of use gets you one that reads like you wrote it yourself.
