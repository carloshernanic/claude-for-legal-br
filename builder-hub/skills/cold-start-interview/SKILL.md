---
name: cold-start-interview
description: >
  Entrevista de perfil de prática que recomenda e instala um starter pack de
  skills jurídicas comunitárias. É o cold start do ecossistema inteiro —
  pergunta que tipo de advogado(a)/profissional jurídico você é e recomenda
  o que instalar primeiro. Use em instalação fresca, quando o(a) usuário(a)
  disser "me ajuda a começar" ou "o que devo instalar", ou para re-rodar o
  check de disponibilidade de integrações após adicionar/remover um conector
  MCP.
argument-hint: "[--redo] [--check-integrations]"
---

# /cold-start-interview

> **Adaptação BR.** Plugin portado do contexto americano para o brasileiro. Todo skill instalado roda sob sigilo profissional do(a) advogado(a) responsável (art. 7º, XIX, EOAB — Lei 8.906/1994); conformidade com LGPD (Lei 13.709/2018), Provimentos OAB (CFOAB 205/2021, 188/2018, 218/2023) e Resoluções CNJ 332/2020 e 615/2025 sobre IA no Judiciário. Para pesquisa em fontes primárias, priorize Planalto, Lexml, STF, STJ, TST, CNJ, JusBrasil, DataJud, PJe, INPI, CVM, BCB, ANPD, CADE.

1. Cheque `~/.claude/plugins/config/claude-for-legal/builder-hub/CLAUDE.md`. Se um CLAUDE.md populado (sem marcadores `[PLACEHOLDER]`) existe em `~/.claude/plugins/cache/claude-for-legal/builder-hub/*/CLAUDE.md` mas não no caminho de config, copie para o caminho de config e diga ao(à) usuário(a) o que foi migrado.
2. Rode a Parte 0 (função + check de integração), depois as cinco perguntas (tipo de prática, setor, time, conforto com ferramentas), conforme o workflow abaixo.
3. Case o perfil às skills do registry. Recomende starter pack.
4. Mostre o resumo do SKILL.md de cada skill recomendada. O(a) usuário(a) escolhe.
5. Instale as escolhidas. Escreva `~/.claude/plugins/config/claude-for-legal/builder-hub/CLAUDE.md` (criando diretórios-pai conforme necessário) com `## Quem está usando`, `## Integrações disponíveis`, perfil + lista de instaladas.

**`--check-integrations`:** Re-roda apenas o check de disponibilidade de integrações da Parte 0. Atualiza a tabela `## Integrações disponíveis` em `~/.claude/plugins/config/claude-for-legal/builder-hub/CLAUDE.md` sem tocar na função ou perfil de prática. Use após adicionar/remover um conector MCP.

Ao sondar: só reporte ✓ se uma chamada de ferramenta MCP de fato teve sucesso. Conectores configurados-mas-não-testados marcam ⚪ com how-to de uma linha para confirmar. Nunca reporte ✓ baseado em declaração de `.mcp.json` apenas — isso ilude o(a) usuário(a) achando que algo está conectado quando não está.

---

## Check de cold-start

Leia `~/.claude/plugins/config/claude-for-legal/builder-hub/CLAUDE.md`:
- **Não existe** → comece a entrevista.
- **Contém `<!-- SETUP PAUSED AT: -->`** → cumprimente e ofereça retomar da seção.
- **Contém marcadores `[PLACEHOLDER]` mas sem comentário de pausa** → template nunca completado; ofereça começar do zero ou retomar de onde começam os placeholders.
- **Populado (sem placeholders, sem comentário de pausa)** → já configurado; pule a menos que `--redo`.

A estrutura do template vive em `${CLAUDE_PLUGIN_ROOT}/CLAUDE.md` — use como scaffold de seção. Escreva o perfil de prática completo no caminho de config, criando diretórios-pai conforme necessário. Se um CLAUDE.md existe no caminho de cache antigo `~/.claude/plugins/cache/claude-for-legal/builder-hub/*/CLAUDE.md` mas não aqui, copie para frente.

## Checar o perfil de empresa compartilhado

Procure `~/.claude/plugins/config/claude-for-legal/company-profile.md`.

- **Se existe:** leia. Mostre confirmação de uma linha: "Você é [nome], [estrutura de prática], em [empresa/escritório], [setor], operando em [jurisdições — federal/estadual/municipal]. Correto? (Ou diga 'update' para mudar o perfil compartilhado.)" Se confirmar, pule perguntas de empresa — vá direto às específicas do plugin.
- **Se não existe:** você é o primeiro plugin que o(a) usuário(a) configurou. Após a orientação e bifurcação, faça as perguntas de empresa e escreva no perfil compartilhado (per template em `references/company-profile-template.md` na raiz do plugin), e continue com as perguntas específicas do plugin. Diga: "Salvei seu perfil de empresa — os outros plugins jurídicos vão ler dele e pular essas perguntas."

As perguntas de empresa que vão no perfil compartilhado (e NÃO devem ser re-perguntadas se ele existir): estrutura de prática, nome da empresa/escritório, setor, o-que-vende/o-que-faz, tamanho, jurisdições (federal/estadual/municipal/setorial), reguladores (ANPD, CVM, BCB, CADE, ANATEL, ANVISA, ANEEL, IBAMA, MPT etc.), apetite a risco, nomes de escalada. As perguntas específicas do plugin (posições de playbook, framework de revisão, estilo da casa, modelo de supervisão etc.) permanecem por plugin.

## Propósito

Este plugin é a "loja de aplicativos". A entrevista de cold-start é a engine de recomendação de onboarding — pergunta o que você faz, recomenda starter pack, instala o que você escolhe.

Diferente das outras cold-starts, esta é curta. Cinco perguntas, recomendação, pronto.

## Check de escopo de instalação

Antes da orientação, se notar que o working directory está dentro de um projeto (não home do(a) usuário(a)), sinalize. Diga uma vez:

> **Atenção — parece que este plugin pode estar com escopo de projeto, o que significa que só posso ler arquivos em [diretório atual]. Se quiser que eu leia documentos de outros lugares (Downloads, Documentos, Dropbox), instale com escopo do(a) usuário(a) — veja QUICKSTART.md. Pode continuar com escopo de projeto, mas precisará mover arquivos para esta pasta.**

Peça confirmação antes de prosseguir: continuar com escopo de projeto, ou pausar para reinstalar com escopo de usuário. Se o working directory *for* o home do(a) usuário(a), pule este check em silêncio.

## Antes da entrevista começar

Mostre este preâmbulo primeiro (3-4 linhas curtas, nada mais):

> **`builder-hub` é para encontrar, instalar e gerenciar skills jurídicas contribuídas pela comunidade.** Procurando um workflow de área de prática? Instale um dos plugins `legal-*` diretamente; rode `/builder-hub:registry-browser` para ver o que tem por aí.
>
> **2 minutos** te dão função e área(s) de prática — mais defaults funcionando para watchlist de registry, cadência de update, e allowlist permissiva por default. **15 minutos** adicionam starter pack calibrado para sua prática, uma política de fontes confiáveis escrita em `allowlist.yaml` (registries, publishers, licenças seedeadas do seu contexto de deploy), preferências de notificação de update, e seu sinal de setor/tamanho de time para recomendações.
>
> Rápido ou completo? (Pode upgrade a qualquer momento com `/builder-hub:cold-start-interview --full`.)

## Depois que o(a) usuário(a) escolheu rápido ou completo

Uma vez escolhido, oriente. Cubra, na sua voz:

- **O que este plugin mantém:** seu perfil de prática (fontes confiáveis, preferências de update, contexto de deploy), um `allowlist.yaml` que controla instalações, e um install log.
- **O que esta configuração faz:** ajuda o(a) usuário(a) a descobrir, instalar e avaliar skills jurídicas comunitárias — um starter pack guiado por perfil de prática mais um check de qualidade de design antes que qualquer coisa toque o workflow. Aprende o perfil de prática e preferências de update e escreve em arquivo texto que o plugin lê toda vez. Tudo pode ser mudado depois.
- **Fontes de dados:** o setup constrói perfil de prática novo apenas das respostas do(a) usuário(a). Não lê histórico pessoal do Claude, outras conversas ou o CLAUDE.md do home. Se algo relevante apareceu antes nesta conversa (ex.: o(a) usuário(a) mencionou seu escritório ou time), pergunte antes de incluir. Nada entra na configuração sem o(a) usuário(a) digitar ou aprovar.

**Por que isso importa.** A recomendação de starter pack do hub e o filtro do auto-updater leem do perfil que esta entrevista escreve. Perfil genérico vira starter pack genérico — skills plausivelmente úteis mas não casadas com a prática real. Dizer ao hub que tipo de profissional o(a) usuário(a) é e o que faz mais é o que separa "aqui estão todas as skills que outros(as) advogados(as) construíram" de "aqui está o conjunto que casa com seu trabalho". Quanto mais específicas as respostas, mais as recomendações vão parecer feitas pelo(a) próprio(a).

### Branching — quick start ou setup completo

O(a) usuário(a) escolheu rápido ou completo no preâmbulo. Bifurque:

**Caminho quick start:** pergunte apenas função e área(s) de prática. Escreva config com marcadores `[DEFAULT]` em todo o resto. Feche com: "Pronto. Pode começar a navegar e instalar. Usei defaults sensatos para watchlist de registry e cadência de update. Rode `/builder-hub:cold-start-interview --full` quando quiser fazer a entrevista inteira, ou `/builder-hub:cold-start-interview --redo <seção>` para refazer uma parte."

**Caminho setup completo:** o fluxo de entrevista abaixo.

## Ritmo da entrevista

- **Assuma que a resposta existe em algum lugar.** Quando uma pergunta pede informação que provavelmente está escrita em algum lugar — descrição da empresa, playbook, matriz de escalada, manual de estilo, manual de RH, lista de jurisdições, portfólio de matérias — peça link ou colagem antes de pedir para o(a) usuário(a) digitar de memória. "Cole um link ou um doc, ou me dê a versão curta" é o pedido default para qualquer coisa que passe de uma frase. Um(a) entrevistador(a) que faz a pessoa re-digitar o que já está escrito falhou no trabalho fundamental.

Curta como é a entrevista, as cinco perguntas variam — área de prática e setor são tap-through, mas "qual é a coisa que você mais faz" precisa de resposta real. Quando uma pergunta precisa de mais que um tap:

- **Faça a pergunta e espere.** Diga explicitamente: "Esta precisa de resposta digitada — vou esperar." Não passe para a próxima até o(a) usuário(a) responder.
- **Se algo for pulado:** "Pule por agora e sinalizo no seu perfil — você pode preencher com `--redo` depois." Então siga, mas registre o skip.
- **Antes de escrever o perfil e recomendar starter pack:** se qualquer resposta foi pulada ou ficou como placeholder, liste e pergunte: "Quer preencher alguma agora, ou deixar como placeholder? A recomendação do starter pack é só tão boa quanto o perfil." Então espere.
- **Nunca** escreva o perfil com lacunas silenciosas — todo placeholder deve ser skip deliberado que o(a) usuário(a) confirmou.
- **Tamanho de lote — conte subpartes.** "Nunca pergunte mais de 2-3 perguntas em um turno" significa 2-3 *prompts respondíveis*, contando subpartes. Uma pergunta com 5 subpartes são 5 perguntas. Teste: o(a) usuário(a) consegue responder sem rolar? Se as perguntas não cabem em uma tela, é demais. Prefira perguntas tap-through estruturadas — não exigem rolagem ou digitação.
- **Pause e retome.** Diga ao(à) usuário(a) de cara: "Se precisar parar, diga 'pausa' (ou 'pare', ou 'deixa pra depois') e eu salvo seu progresso. Rode `/builder-hub:cold-start-interview` de novo depois e eu retomo de onde parou." Quando pausar, escreva configuração parcial em `~/.claude/plugins/config/claude-for-legal/builder-hub/CLAUDE.md` com `<!-- SETUP PAUSED AT: [nome da seção] — rode /builder-hub:cold-start-interview para retomar -->` no topo e marcadores `[PENDING]` (distintos de `[PLACEHOLDER]`) em campos não-respondidos. Quando o setup re-rodar e achar config pausada, cumprimente: "Bem-vindo(a) de volta. Você pausou em [seção]. Suas respostas anteriores estão salvas. Retomar de onde parou, ou começar do zero?" Não re-pergunte o que já foi respondido.

**Verifique fatos jurídicos asseridos pelo(a) usuário(a) à medida que aparecem no setup.** Quando o(a) usuário(a) responde com citação específica de norma, lei, súmula, prazo, limiar, jurisdição ou número de registro — e é algo que você consegue conferir — faça o check antes de escrever na configuração. Se o que disseram conflita com seu entendimento ou com algo que colaram, sinalize: "Você disse que o limiar é X; meu entendimento é Y — pode confirmar qual vai no perfil? `[premissa sinalizada — verificar]`" Fato errado escrito no CLAUDE.md propaga para todo output futuro; pegar aqui é um dos momentos de maior alavancagem do produto.

## A entrevista

### Abertura

> Vou te ajudar a encontrar e instalar skills jurídicas comunitárias — coisas que outros(as) advogados(as) construíram e compartilharam. Primeiro, que tipo de profissional jurídico você é? Vou recomendar um pacote inicial.

### Parte 0: Quem está usando, e o que está conectado

Duas perguntas rápidas antes do perfil de prática. Moldam como o plugin funciona, não o que ele pode fazer.

#### Quem está usando?

> Quem vai usar este plugin no dia-a-dia? (Isso alimenta o sinal de Função carregado por todo plugin que você instalar — skills com modo não-advogado(a) leem daqui em vez de re-perguntar, e os outputs de `recommend` e `qa` se estruturam para leitor não-advogado(a) quando apropriado.)
>
> 1. **Advogado(a) ou profissional jurídico** — advogado(a) inscrito(a) na OAB, paralegal, legal ops trabalhando sob supervisão de advogado(a).
> 2. **Não-advogado(a) com acesso a advogado(a)** — fundador(a), gestor(a) de negócios, contracts manager, RH, compras; você tem advogado(a) interno(a) ou externo(a) que pode consultar.
> 3. **Não-advogado(a) sem acesso regular a advogado(a)** — está cuidando sozinho(a).

Se a resposta for 2 ou 3, diga isto uma vez:

> Este plugin descobre e instala skills. Skills que você instalar terão seus próprios guardrails baseados na sua função — vou carregar sua resposta aqui para frente para você não responder por plugin.

Se a resposta for 3, adicione:

> Se precisa encontrar advogado(a) inscrito(a) na OAB: o serviço de referência da seccional da OAB no seu estado é o ponto de partida mais rápido. Para questões trabalhistas, a Defensoria Pública e os sindicatos da categoria também orientam. Para empresas pequenas, escritórios de advocacia oferecem consultas iniciais frequentemente sem cobrança. Para indivíduos hipossuficientes, a Defensoria Pública cobre muitas áreas; NPJs (Núcleos de Prática Jurídica de faculdades — Resolução CNE/CES 5/2018) atendem causas de baixa complexidade. Para mediação extrajudicial, veja câmaras credenciadas pelo CNJ (Provimento CFOAB 188/2018).

#### O que está conectado?

> Este plugin pode trabalhar com: Slack (para notificações de skill nova / update). Deixe eu checar quais conectores estão configurados — features que precisam deles vão funcionar, e features que não têm vão cair em fallback manual com elegância em vez de falhar em silêncio.

**Cheque o que de fato está conectado, não o que está configurado.** Um conector listado em `.mcp.json` está *disponível*. Um conector que de fato responde está *conectado*. São coisas diferentes, e confundir destrói confiança. Para cada conector que este plugin usa:

- Se puder testar a conexão (chamar uma ferramenta MCP simples como list ou search), reporte ✓ apenas em resposta bem-sucedida.
- Se não puder testar (sem como sondar daqui), reporte ⚪ "configurado mas não verificado — abra as configurações de MCP para confirmar" com how-to de uma linha.
- Nunca reporte ✓ baseado em configuração apenas.

Para conectores que mostram como não conectados, diga ao(à) usuário(a) como conectar. Exemplo: "Slack não está conectado. Em Claude Cowork: Settings → Connectors → Add → Slack → faça login. Em Claude Code: adicione o MCP do Slack na config ou via `/mcp`. Este plugin funciona sem ele — notificações de update aparecem no próximo `/builder-hub:registry-browser` ou `/builder-hub:auto-updater` em vez de proativas — mas conectar torna notificações em tempo real."

Então reporte achados nesta forma:

> - ✓ [Integração] — conectada (testada)
> - ⚪ [Integração] — configurada mas não verificada. Abra as configurações de MCP para confirmar.
> - ✗ [Integração] — não encontrada. [Feature] cairá em [alternativa manual]. [Como conectar.]

Você não precisa disso. Features core — browse, install, QA, update — funcionam só com acesso a arquivo.

Escreva as respostas da Parte 0 na config do plugin sob `## Quem está usando` e `## Integrações disponíveis`. Este plugin escreve `## Quem está usando` para que outros plugins instalados depois leiam a função daqui em vez de re-perguntar.

Antes das cinco perguntas: "Você já tem lista de registries de skills comunitárias que acompanha, ou allowlist/blocklist de fontes que seu time usa? Cole o conteúdo, dê o caminho do arquivo, ou diga 'não' e eu coloco o default. Se compartilhar, eu leio e adiciono os registries e seu allowlist ao perfil em vez de você re-digitar. (Isso alimenta `/builder-hub:skill-installer` — o instalador lê `allowlist.yaml` antes de buscar qualquer coisa, e bloqueia qualquer fonte que não está na lista em modo restrictive.)"

**Contexto de deploy.** Após a pergunta de allowlist e antes de escrever o arquivo, pergunte:

> "Como você vai usar as skills que instalar — só pra você, compartilhadas no escritório/banca, ou embarcadas num produto/serviço que você entrega a terceiros? (Pessoal / Interno-escritório / Embarcado-em-produto.) (Isso alimenta `allowlist.yaml` — o contexto de deploy seedeia a lista `licenses:`, e `/builder-hub:skill-installer` recusa buscar skill com licença não listada.) Isso define seus defaults de licença. A maioria das licenças open source serve para uso pessoal. Interno-escritório adiciona copyleft de arquivo (LGPL, MPL — ok quando não há distribuição). Embarcado-em-produto é o estrito: copyleft forte (GPL, AGPL) cria obrigações que precisam revisão jurídica antes de embarcar, então são sinalizadas em vez de default."

Registre a resposta no perfil sob `## Fontes que eu confio` como `Contexto de deploy: [pessoal | interno-escritório | embarcado-em-produto]`. O seed de `licenses:` no allowlist lê daqui.

**Escreva o allowlist em `allowlist.yaml`, não só no perfil.** O gate do instalador lê de `~/.claude/plugins/config/claude-for-legal/builder-hub/allowlist.yaml`, não do CLAUDE.md. Se você só registrar no perfil, o instalador vê allowlist vazio e cai em permissive independentemente do que o(a) usuário(a) disse — derrotando em silêncio a defesa estrutural-chave. Após essa pergunta:

1. Escreva `allowlist.yaml` no caminho de config, seguindo o esquema em `skill-installer/references/allowlist.md`:
   - `mode:` — o default do template é `restrictive` (fail-closed). Ofereça `permissive` para Solo/escritório pequeno (não têm listas de publishers curadas por TI, então restrictive recusaria tudo). Mantenha `restrictive` para Médio/grande escritório, In-house, ou Setor Público (têm políticas de segurança que querem gate de firma). Sempre confirme: "Vou setar o allowlist para [modo]. Restrictive recusa fontes desconhecidas até você adicionar — mais seguro, mas precisa aprovar cada publisher novo. Permissive sinaliza fontes desconhecidas e pergunta antes de instalar — mais conveniente, menos estrito. Qual você quer?" Nunca escreva permissive sem consentimento explícito.
   - `registries:` — o que o(a) usuário(a) deu mais o default.
   - `publishers:` — owners/orgs do GitHub que o(a) usuário(a) nomeou ou que mantêm os registries confiáveis.
   - `connectors:` — vazio a menos que o(a) usuário(a) tenha dado lista; em modo restrictive, pergunte: "Modo restrictive precisa de allowlist de conectores — cole URLs de servidores MCP aprovados, ou deixo vazio e skills declarando qualquer conector serão recusadas."
   - `licenses:` — seed baseado em contexto de deploy acima:
     - **Pessoal** → `MIT`, `Apache-2.0`, `BSD-2-Clause`, `BSD-3-Clause`, `ISC`, `CC0-1.0`.
     - **Interno-escritório** → mesmo que Pessoal mais `LGPL-2.1-only`, `LGPL-3.0-only`, `MPL-2.0`.
     - **Embarcado-em-produto** → mesmo que Pessoal. Também escreva comentário no topo de `allowlist.yaml`: `## Revisão de licença obrigatória antes de embarcar — qualquer coisa fora desta lista precisa de sign-off jurídico.` Copyleft forte (GPL, AGPL) é deliberadamente excluído do default; adicionar requer edição deliberada.
2. Também sumarize no perfil em `## Fontes que eu confio` para que um(a) humano(a) veja a política.
3. Diga ao(à) usuário(a) onde fica: "Seu allowlist está em `~/.claude/plugins/config/claude-for-legal/builder-hub/allowlist.yaml`. O instalador lê antes de buscar qualquer coisa."

Se o(a) usuário(a) subir arquivo de registry/allowlist: leia, extraia URLs de registry e entradas de allowlist/blocklist, confirme o que achou, escreva `allowlist.yaml` per esquema e sumarize no perfil.

**Lembretes de frescor.** Após a pergunta de allowlist (contexto de deploy setado) e antes das cinco perguntas, pergunte:

> "Quando skill comunitária inclui material de referência — leis, normas, modelos processuais — por quanto tempo deve ser confiada antes de eu lembrar você de verificar atualidade? (6 meses é default comum para conteúdo regulatório. 12 meses para conteúdo procedimental/estilístico. Aperte se trabalha em área de mudança rápida — direito digital com decisões CD/ANPD e novidades de PL 2338/2023, tributário com EC 132/2023 em vigência, processual com mini-reformas CPC.)"

Aceite número único (aplica a regulatório; use defaults de categoria abaixo nos demais) ou respostas por categoria. Valide cada uma como `N days`, `N months` ou `N years` com `N` inteiro positivo ≤ 120 — rejeite prosa livre e re-pergunte.

Escreva a resposta em seção `## Lembretes de frescor` no perfil (insira depois de `## Fontes que eu confio` e antes de `## Starter pack instalado`):

```markdown
## Lembretes de frescor

| Categoria de conteúdo | Idade máxima antes do lembrete | Racional |
|---|---|---|
| regulatório | 6 meses | Reguladores (ANPD, CVM, BCB, CADE, ANATEL, ANVISA) atualizam frequentemente; prioridades de fiscalização mudam |
| procedimental | 12 meses | Resoluções CNJ, regimentos de tribunais e procedimentos mudam mais devagar |
| estilístico | 24 meses | Estilo da casa, templates de formatação |
| desconhecido | 3 meses | Skill que não declara frescor é tratada cautelosamente |

Quando `last_verified` + `freshness_window` da skill passou, ou o threshold do(a) usuário(a) (acima) passou — o que for mais apertado — o skill-installer mostra aviso antes de rodar.
```

Se o(a) usuário(a) deu números mais apertados, escreva no lugar dos defaults. Se disse "use defaults", escreva a tabela como mostrada.

**Se o(a) usuário(a) não subiu lista de registry:** após as cinco perguntas, ofereça: "Quer que eu escreva seus registries monitorados e preferências de update como nota de política autônoma que pode compartilhar com seu time? Mesmo conteúdo que salvo no perfil, formatado para que colegas de time ou novo(a) builder vejam quais fontes você confia e como quer updates tratados."

### As cinco perguntas

1. **Área de prática** — In-house ou banca? Comercial, dados/privacidade, produto, trabalhista, contencioso, M&A, tributário, regulatório, outra? (Alimenta `/builder-hub:related-skills-surfacer` — a área de prática é a chave primária que mapeia para o starter pack.)

   **Práticas que não cabem nas caixas.** Se a prática do(a) usuário(a) não bate com as opções (arbitragem internacional — CAM-CCBC, CAM B3, CBMA; advocacia pública; advocacia consultiva legislativa; NPJ universitário; consultoria acadêmica; militar; portuário/marítimo; advocacia indígena; ou qualquer outra coisa que as categorias padrão assumem que não existem), ofereça: "Parece que sua prática não cabe nas minhas categorias usuais. Me conte com suas palavras — o que faz, para quem, em quais jurisdições e foros, como o trabalho se parece — e construo seu perfil a partir disso em vez de te forçar em caixas que não cabem. Vou pular ou adaptar as perguntas que não se aplicam." Construa o perfil a partir da descrição livre, sinalizando quais campos do template foram preenchidos, adaptados ou deixados vazios por não se aplicarem. Perfil construído de encaixe forçado é pior que perfil esparso construído do que de fato é verdade.

2. **Setor** — Tech, saúde, finanças, varejo, agro, energia, indústria, outro, não importa? (Alimenta `/builder-hub:related-skills-surfacer` e `/builder-hub:registry-browser` — setor estreita o starter pack e filtra resultados de registry.)

3. **Tamanho de time** — Solo, time pequeno (2-5), departamento jurídico grande? (Alimenta o default do `allowlist.yaml` — Solo/pequeno pega permissive, Médio/grande/In-house/Setor Público pega restrictive.)

4. **Qual é a coisa que você mais faz?** — Revisão de contrato, compliance, lançamento de produto, suporte a deal, peças contenciosas, due diligence, parecer regulatório etc. (Alimenta `/builder-hub:related-skills-surfacer` — o surfacer cutuca quando você está fazendo algo para o qual a comunidade tem skill.)

5. **Conforto com ferramentas** — Construtor(a) (escreve suas próprias skills), experimentador(a) (edita o que está instalado), só-faça-funcionar (quer funcionando out-of-the-box)? (Alimenta `/builder-hub:related-skills-surfacer` — construtores(as) pegam registries crus e framework `/builder-hub:skills-qa`; só-faça-funcionar pega pack curado e funcionando.)

### Recomendar

Mapeie o perfil às skills do registry:

| Perfil | Starter pack |
|---|---|
| In-house comercial, tech | comercial plugin + lpm-skills (intake de matéria, controle de escopo) |
| Privacy/dados (LGPD) | privacidade plugin + skills comunitárias de RIPD/DPA |
| Counsel de produto | produto plugin + skills comunitárias de revisão de marketing/publicidade (CONAR, CDC) |
| Contencioso de escritório | contencioso plugin + lpm-skills (planejamento de matéria, orçamento) |
| Trabalhista in-house | trabalhista plugin (CLT, e-Social, NRs) |
| Tributário | regulatório/tributário (Receita, EC 132/2023, ISS/ICMS) |
| Solo / time pequeno | Tudo leve — skills de triagem em vez de revisão completa |
| Construtor(a) | os registries crus e o framework skills-qa — vão construir e validar próprias |

Para cada skill recomendada: mostre a descrição do SKILL.md. Deixe escolherem — não instale nada sem um sim.

## Escrevendo o perfil de prática

Curto. Perfil + lista de instaladas + preferências de registry. Per template em `${CLAUDE_PLUGIN_ROOT}/CLAUDE.md`.

## Depois de escrever

**Mostre o que este plugin pode fazer.** Antes de fechar, ofereça:

> **Quer ver com o que posso ajudar?**

Se sim, mostre esta lista calibrada (não template genérico — estas são as coisas concretas que este plugin faz melhor):

> **Aqui está no que sou bom em gestão de skills jurídicas:**
>
> - **Navegar skills jurídicas comunitárias** — ex.: "Ver o que outros(as) praticantes construíram para sua área." Tente: `/builder-hub:registry-browser`
> - **Instalar skill de um registry** — ex.: "Adicionar skill comunitária ao ambiente — gated por licença e allowlist antes de rodar." Tente: `/builder-hub:skill-installer`
> - **Checar atualizações** — ex.: "Ver quais skills instaladas têm versão mais nova no registry de origem." Tente: `/builder-hub:auto-updater`
> - **Receber recomendações de skill** — ex.: "Baseado em atividade recente nos outros plugins, mostrar skills que valem testar." Tente: `/builder-hub:related-skills-surfacer`
> - **Avaliar skill contra o framework de design** — ex.: "Rodar o Legal Skill Design Framework — treze parâmetros de design, três modos de falha jurídica, check de trust-surface." Tente: `/builder-hub:skills-qa`
>
> **Minha sugestão para a primeira:** Navegue o registry e escolha uma skill que casa com projeto atual — instale e veja como o gate de allowlist se sente. Ou me diga o que está na sua mesa e eu escolho.

Isso resolve o problema de cold-start (o(a) supervisor(a) não sabe o que fazer primeiro) e o problema de proposta de valor (não sabem o que o plugin pode fazer) numa oferta só. Faça a lista específica. Pule esta etapa se o(a) supervisor(a) já nomeou primeira tarefa concreta durante a entrevista.


- "Aqui está o que instalei. Quer ver o que mais tem nos registries?"
- "O related-skills-surfacer vai cutucar quando você estiver fazendo algo para o qual a comunidade tem skill. Quer ligado ou desligado?"
- **Antes da primeira skill instalada que cita autoridade, conecte ferramenta de pesquisa.** Diga: "Antes da primeira skill instalada que cita autoridade: conecte ferramenta de pesquisa se algum dos plugins instalados precisar. Sem ela, skills vão sinalizar toda citação como não-verificada. Em Cowork: Settings → Connectors. Em Claude Code: autorize quando a skill pedir."

<!-- COLLATERAL LINKS: quando colateral de onboarding existir, adicione aqui:
     "Quer um walkthrough primeiro? [Veja a intro de 3 minutos](URL) ou [leia o guia de início](URL)." -->

Então feche com a nota "pode mudar qualquer coisa depois":

> Pronto. Sua configuração está em `~/.claude/plugins/config/claude-for-legal/builder-hub/CLAUDE.md` — arquivo texto que pode ler e editar direto. Qualquer coisa respondida pode ser mudada:
>
> - Edite o arquivo direto para mudança rápida
> - Rode `/builder-hub:cold-start-interview --redo` para re-entrevista completa
> - Rode `/builder-hub:cold-start-interview --check-integrations` para re-checar o que está conectado
>
> As coisas mais comumente ajustadas depois: seus registries monitorados (adiciona ou tira fontes), sua preferência de update (notificar vs. manual), e o escopo do perfil de prática (adiciona setor ou segunda prática à medida que o trabalho muda). Sua configuração melhora conforme você usa o plugin — se recomendações parecem off, o perfil normalmente é o fix.

## Seu perfil de prática aprende

Após escrever o perfil, feche com esta nota:

> **Seu perfil de prática aprende.** Melhora conforme você usa os plugins:
>
> - Quando output de skill sentir off, normalmente é posição para calibrar. O output vai dizer qual.
> - Pode sempre dizer "atualize meu playbook para preferir X" ou "mude meu threshold de escalada para Y" e a skill relevante escreve a mudança.
> - Rode `/builder-hub:cold-start-interview --redo <seção>` para re-entrevistar uma parte, ou edite o arquivo de config direto.
>
> Dez minutos de setup te dão perfil funcionando. Um mês de uso te dá um que parece que você escreveu.

## Registries monitorados por default

- **lpm-skills** (github.com/legalopsconsulting/lpm-skills) — legal project management, agnóstico de área
- Usuário(a) pode adicionar outros via `/builder-hub:registry-browser`
