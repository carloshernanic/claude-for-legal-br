---
name: cold-start-interview
description: >
  Roda a entrevista de cold-start — aprende sua prática de privacidade e escreve
  o CLAUDE.md a partir da sua política, do template de contrato de tratamento
  e de um RIPD de referência. Use na primeira execução, quando o CLAUDE.md está
  faltando ou tem placeholders, ou quando o(a) usuário(a) diz "configurar o
  plugin de privacidade", "fazer onboarding", "configurar privacidade", ou quer
  rerodar a entrevista ou reconferir integrações.
argument-hint: "[--redo para rerodar] [--check-integrations para só rechecar integrações]"
---

# /cold-start-interview

> **Adaptação BR.** Esta skill foi adaptada para o regime da **LGPD (Lei 13.709/2018)**. DPO → Encarregado(a) (art. 41); DSAR → requisição de titular (art. 18); PIA/DPIA → RIPD (art. 38); breach → comunicação de incidente (art. 48 + Resolução CD/ANPD 15/2024); supervisory authority → ANPD; "attorney work product" → sigilo profissional do(a) advogado(a) (EOAB art. 7º XIX). Sempre cite fontes brasileiras primárias.

1. Cheque `~/.claude/plugins/config/claude-for-legal/privacidade/CLAUDE.md` — se preenchido e sem `--redo`, confirme antes de sobrescrever.
2. Rode o workflow de entrevista abaixo.
3. Documentos-semente: política de privacidade (URL ou arquivo), template de contrato de tratamento de dados (acordo controlador-operador), um RIPD de referência. Leia os três.
4. Extraia: compromissos da política, posições do contrato (note divergências vs. afirmações), estrutura do RIPD.
5. Migração: se houver CLAUDE.md preenchido (sem marcadores `[PLACEHOLDER]`) em `~/.claude/plugins/cache/claude-for-legal/privacidade/*/CLAUDE.md` mas não no caminho de configuração, copie para o caminho de configuração e mostre ao(à) usuário(a) o que foi migrado.
6. Escreva `~/.claude/plugins/config/claude-for-legal/privacidade/CLAUDE.md` (criando diretórios-pais conforme necessário). Mostre resumo. Ofereça primeira tarefa.

## `--check-integrations`

Rerroda a checagem de disponibilidade de integrações (armazenamento de documentos, Slack, tarefas agendadas) e atualiza `## Integrações disponíveis` em `~/.claude/plugins/config/claude-for-legal/privacidade/CLAUDE.md`. Não reentrevista. Use quando conectar ou desconectar um MCP e quiser que o plugin perceba sem rerodar o setup completo.

Ao sondar: só reporte ✓ se a chamada de ferramenta MCP de fato teve sucesso. Conectores configurados-mas-não-testados devem ser marcados ⚪ com nota curta de como confirmar. Nunca reporte ✓ a partir de declarações em `.mcp.json` apenas — isso engana o(a) usuário(a) a pensar que algo está cabeado quando não está.

```
/privacidade:cold-start-interview
```

```
/privacidade:cold-start-interview --check-integrations
```

---

# Entrevista de Cold-Start: Privacidade & Proteção de Dados (LGPD)

## Propósito

Aprender como *este* time de privacidade trabalha — quais normas de fato se aplicam (LGPD necessariamente; setoriais conforme caso), o que aceita ou não num contrato de tratamento, como é um RIPD bom aqui. Escrever isso em `~/.claude/plugins/config/claude-for-legal/privacidade/CLAUDE.md` para toda outra skill ler do mesmo entendimento.

Práticas de privacidade variam muito por empresa. Um(a) SaaS B2B operador(a) tem quase nada em comum com um app de consumo controlador. A entrevista descobre qual é antes de qualquer coisa.

## Checagem de cold-start

Leia `~/.claude/plugins/config/claude-for-legal/privacidade/CLAUDE.md`:
- **Não existe** → iniciar a entrevista.
- **Contém `<!-- SETUP PAUSED AT: -->`** → cumprimente e ofereça retomar daquela seção.
- **Contém marcadores `[PLACEHOLDER]` mas sem comentário de pausa** → template nunca foi concluído; ofereça começar do zero ou retomar de onde começam os placeholders.
- **Preenchido (sem placeholders nem comentário de pausa)** → já configurado; pular salvo `--redo`.

A estrutura-template fica em `${CLAUDE_PLUGIN_ROOT}/CLAUDE.md` — use-a como esqueleto de seção. Escreva o perfil de prática concluído no caminho de configuração, criando diretórios-pais conforme necessário.

Se existir CLAUDE.md no caminho antigo de cache `~/.claude/plugins/cache/claude-for-legal/privacidade/*/CLAUDE.md` mas não no caminho de configuração, copie para frente.

## Checagem do perfil de empresa compartilhado

Procure `~/.claude/plugins/config/claude-for-legal/company-profile.md`.

- **Se existir:** Leia. Mostre confirmação de uma linha: "Você é [nome], [setting], na [empresa], [setor], operando em [jurisdições]. Certo? (Ou diga 'atualizar' para mudar o perfil compartilhado.)" Se confirmado, pule as perguntas de empresa — vá direto para as específicas do plugin.
- **Se não existir:** Você é o primeiro plugin que este(a) usuário(a) configura. Após a orientação e fork, faça as perguntas de empresa e escreva no perfil compartilhado (conforme template em `references/company-profile-template.md` na raiz do plugin), depois siga com as específicas. Diga: "Salvei seu perfil de empresa — os outros plugins jurídicos vão ler e pular essas perguntas."

As perguntas de empresa que pertencem ao perfil compartilhado (e NÃO devem ser re-perguntadas se ele existe): configuração de prática, nome da empresa, setor, o que vende, porte, jurisdições, reguladores (ANPD obrigatoriamente para LGPD; setoriais quando aplicável), tolerância a risco, nomes de escalonamento. As específicas do plugin (posições de playbook, framework de revisão, estilo da casa, modelo de supervisão etc.) ficam por plugin.

## Checagem de escopo de instalação

Antes da orientação, se notar que o diretório de trabalho está dentro de um projeto (não no diretório home do(a) usuário(a)), sinalize. Diga uma vez:

> **Atenção — parece que este plugin pode estar com escopo de projeto, o que significa que só posso ler arquivos em [diretório atual]. Se você quer que eu leia documentos de outros lugares (Downloads, Documentos, Dropbox), instale com escopo de usuário — veja QUICKSTART.md. Pode seguir com escopo de projeto, mas vai precisar mover os arquivos para cá.**

Peça confirmação antes de prosseguir: seguir com escopo de projeto, ou pausar para reinstalar em escopo de usuário. Se o diretório de trabalho FOR o home do(a) usuário(a), pule esta checagem em silêncio.

## Antes da entrevista começar

Antes de perguntar qualquer outra coisa, mostre o preâmbulo de fork-first — 3-4 linhas curtas, não mais:

> **`privacidade` é para quem cuida do programa de privacidade: RIPDs, revisões de contrato de tratamento, requisições de titular, análise de gap regulatório.** Não é sua área? `/builder-hub:related-skills-surfacer`.
>
> **2 minutos** dão seu papel, qual lado do contrato de tratamento você está (operador/controlador/ambos) e jurisdições primárias, com defaults sensatos no resto. **15 minutos** acrescentam suas posições de playbook (lado operador e lado controlador), a estrutura do seu RIPD a partir de um RIPD de referência, seu footprint regulatório completo e suas atividades de tratamento iniciais.
>
> Rápido ou completo? (Pode subir para completo a qualquer momento com `/cold-start-interview --full`.)

Espere a escolha antes de mostrar qualquer outra coisa.

<!-- LINKS DE COLATERAL: quando houver colateral de onboarding, prepende uma linha acima do preâmbulo:
     "Quer um walkthrough primeiro? [Assistir intro de 3 minutos](URL) ou [ler o guia de início](URL), depois volte e rode /cold-start-interview." -->

## Depois que o(a) usuário(a) escolhe rápido ou completo

Uma vez escolhido, oriente antes da primeira pergunta:

> "Este plugin mantém seu perfil de prática (playbook de contrato de tratamento, estilo de RIPD, footprint regulatório), um registro de atividades de tratamento, e RIPDs e revisões de contrato por atividade. Esta entrevista aprende como você de fato trabalha — sua prática, suas posições, seu estilo — e escreve num arquivo texto que o plugin lê toda vez. Tudo o que você responder pode ser mudado depois. Quando estiver pronto, os comandos do plugin vão trabalhar do jeito que você trabalha, não do jeito de um template genérico."
>
> Depois: "O setup constrói perfil profissional fresco a partir das suas respostas. NÃO lê seu histórico pessoal do Claude, outras conversas, nem seu CLAUDE.md do home. Se eu notar informação relevante no contexto da conversa — ex., você mencionou sua empresa antes — eu pergunto antes de usar. Nada pessoal entra na sua configuração de prática sem você digitar ou aprovar."
>
> Depois: "Pronto? Algumas perguntas rápidas primeiro, depois aprofundamos."

**Por que isso importa.** Cada comando deste plugin lê da configuração que esta entrevista escreve. Configuração genérica = saída genérica — posição-padrão de contrato, formato-padrão de RIPD, workflow-padrão de requisição de titular, e uma revisão que trata seu contrato B2B operador igual a um de controlador-consumidor. Dizer ao plugin seu footprint real, suas posições reais, seu estilo real de RIPD é o que faz diferença entre "uma ferramenta de IA para privacidade" e "uma ferramenta que trabalha do jeito que seu programa trabalha." Quanto mais específica a resposta, mais a saída vai parecer sua.

Popule o perfil de prática SÓ a partir das respostas digitadas e dos três documentos-semente. Não leia `~/CLAUDE.md` nem puxe fatos de prática de contexto ambiente. Se algo relevante já estiver visível na conversa, pergunte antes de usar.

**Trilha rápida:** só Parte 0 (papel, configuração de prática, integrações) e footprint regulatório. Escreva a config com marcadores `[DEFAULT]` no resto. Encerre com: "Pronto. Já pode usar os comandos. Usei defaults sensatos para posições de contrato, prazos de requisição e gatilhos de RIPD. Quando a saída de uma skill parecer estranha, em geral é um default para tunar — vai te dizer qual. Rode `/privacidade:cold-start-interview --full` para fazer a entrevista inteira, ou `/privacidade:cold-start-interview --redo <seção>` para refazer uma parte."

**Trilha completa:** o fluxo abaixo.

## Pacing da entrevista

- **Assuma que a resposta existe em algum lugar.** Quando uma pergunta pede informação provavelmente já escrita — descrição da empresa, playbook, matriz de escalonamento, guia de estilo, manual, lista de jurisdições, portfólio — peça link ou um colar antes de pedir para digitar de memória. "Cole link ou doc, ou dê a versão curta" é a forma padrão para qualquer coisa de mais de uma frase. Entrevistador(a) que faz redigitar o que já está escrito falhou no primeiro trabalho.
- **Tamanho do lote — conte subpartes.** "Nunca mais de 2-3 perguntas por turno" significa 2-3 *prompts respondíveis*, contando subpartes. Uma pergunta com 5 subpartes são 5 perguntas. Teste: a pessoa consegue responder sem rolar a tela? Se não cabe numa tela, é demais. Prefira perguntas estruturadas (tap-through).

**Pause para respostas reais.** Algumas perguntas têm resposta de toque (controlador vs. operador, footprint). Outras precisam digitar, descrever, enviar documento (política, template de contrato, RIPD de referência, posições, lista de sistemas para requisições de titular). Quando precisa mais que um toque:

- **Faça a pergunta e espere.** Diga explicitamente: "Esta precisa resposta digitada — vou esperar." Não avance até a pessoa responder.
- **Para upload de doc-semente:** "Cole o conteúdo, compartilhe um caminho ou URL, ou diga 'pular por agora.' Se pular, eu sinalizo o gap no seu perfil para você preencher depois." Daí espere.
- **Antes de escrever o perfil:** revise a entrevista. Liste perguntas puladas ou com placeholder (especialmente os três docs-semente e as posições do contrato). Diga: "Antes de escrever seu perfil, eis o que está em aberto: [lista]. Quer preencher algo agora, ou deixar como placeholder?" Espere.
- **Nunca** escreva perfil com lacunas silenciosas. Todo `[PLACEHOLDER]` deve ser escolha deliberada de pular, não pergunta que passou. Se o template de contrato ou o RIPD de referência foi pulado, note `[POSITIONS UNTESTED]` para que as skills downstream saibam.
- **Pausar e retomar.** Diga de antemão: "Se precisar parar, diga 'pausa' (ou 'parar', ou 'deixa eu voltar nisso') e eu salvo o progresso. Rode `/privacidade:cold-start-interview` de novo depois e retomo de onde paramos." Quando pausa, escreva config parcial em `~/.claude/plugins/config/claude-for-legal/privacidade/CLAUDE.md` com comentário `<!-- SETUP PAUSED AT: [seção] — rode /privacidade:cold-start-interview para retomar -->` no topo e marcadores `[PENDING]` (distintos de `[PLACEHOLDER]`) nos campos não respondidos. Quando o setup rerodar e achar config pausada, cumprimente: "Bem-vindo(a) de volta. Você pausou em [seção]. Suas respostas anteriores estão salvas. Retomar ou começar de novo?" Não re-pergunte o já respondido.

**Verifique fatos jurídicos afirmados pelo(a) usuário(a).** Quando responde com citação de regra, número de lei, nome de caso, prazo, threshold, jurisdição ou número de registro — e é algo que dá para checar — confira antes de escrever na configuração. Se o que disse conflita com seu entendimento ou com algo enviado, traga à tona: "Você disse que o prazo é X; meu entendimento é Y — pode confirmar qual vai no perfil? `[premissa sinalizada — verificar]`". Um fato errado escrito no CLAUDE.md se propaga para cada saída futura; pegar aqui é dos momentos de maior alavancagem do produto.

## A entrevista

### Abertura

> Eu vou ajudar com contratos de tratamento, requisições de titular, RIPDs, e ficar de olho em quando as regras mudam debaixo de você. Antes de nada, preciso saber que tipo de programa de privacidade é este. Dez minutos.
>
> Depois vou pedir três coisas: sua política de privacidade, seu contrato de tratamento padrão, e um RIPD que você acha bom. Vou aprender mais com isso do que com qualquer coisa que você me conte.

### Parte 0: Quem está usando, e o que está conectado

Três perguntas rápidas antes do conteúdo específico de privacidade. Elas moldam COMO o plugin trabalha, não O QUE pode fazer.

#### Quem está usando?

> Quem vai usar este plugin no dia-a-dia? (Isso alimenta o cabeçalho de cada saída e o enquadramento — advogado(a) recebe "MATERIAL DE TRABALHO PRIVILEGIADO", não advogado(a) recebe enquadramento de pesquisa e checkpoints de revisão por advogado(a) antes de passos com consequência jurídica.)
>
> 1. **Advogado(a) ou profissional jurídico** — advogado(a) habilitado(a) na OAB, paralegal, equipe de privacy ops sob supervisão de advogado(a).
> 2. **Não advogado(a) com acesso a advogado(a)** — Encarregado(a) não-advogado(a), gerente de programa de privacidade, fundador(a) cuidando de privacidade com advogado(a) interno(a) ou externo(a) que pode consultar.
> 3. **Não advogado(a) sem acesso regular a advogado(a)** — você está conduzindo isto por conta própria.

Se a resposta for 2 ou 3, diga uma vez (não repita em cada saída):

> Você pode usar todos os recursos — triagem, revisão de contrato, RIPDs, requisições de titular, gap regulatório, monitor de política. Duas coisas mudam em como trabalho:
>
> 1. **Enquadro saídas como pesquisa para revisão por advogado(a), não como veredicto.** Em vez de "liberado para assinar", você recebe "eis o que encontrei e eis as perguntas para fazer ao(à) advogado(a) antes de assinar." Isso é mais útil que um sinal verde no qual você não pode confiar.
> 2. **Eu paro antes de passos com consequência jurídica** — enviar resposta a requisição de titular, assinar contrato de tratamento, submeter RIPD à ANPD, comunicar incidente. Pergunto se você revisou com advogado(a) e monto um brief curto para a conversa com ele(a) ser rápida.
>
> Não é disclaimer. É o plugin sabendo a diferença entre o que ele é bom (pesquisa, organização, estrutura) e o julgamento jurídico licenciado sobre sua situação específica, que ferramenta não dá. Algumas horas de advogado(a) no momento certo costuma sair mais barato do que o erro.

Se a resposta for 3, acrescente:

> Se precisa encontrar advogado(a) habilitado(a) na OAB: a **Seccional da OAB** do seu estado tem serviço de orientação e referência. Muitos(as) advogados(as) oferecem consulta inicial gratuita ou de baixo custo. Para pequenas empresas, **SEBRAE** e **Núcleos de Prática Jurídica (NPJ)** em faculdades locais podem orientar. Para pessoas físicas, a **Defensoria Pública** atende quando há hipossuficiência. Em privacidade, vale procurar advogado(a) com prática consolidada em LGPD/proteção de dados — há grupos especializados em várias seccionais (Comissões de Proteção de Dados da OAB-SP, OAB-RJ etc.).

#### Configuração de prática

> Qual destas melhor descreve onde você atua? (Isso alimenta a matriz de escalonamento — solo/pequena banca pula cadeia de aprovação e pergunta quando consultar colega ou advogado(a) externo(a); banca média/grande pergunta cadeia de aprovação, threshold de billing e quem assina acima; in-house pergunta matriz de escalonamento, quem é GC/Diretor(a) Jurídico(a), quando vai para o negócio; setor público / clínica universitária (NPJ) pergunta supervisão e restrições.)
>
> - **Solo / pequena banca (sem hierarquia)** — pulo perguntas de aprovação interna e pergunto quando você acionaria colega ou advogado(a) externo(a).
> - **Banca média / grande** — pergunto cadeia de aprovação, thresholds de billing, quem assina acima de você.
> - **In-house** — pergunto matriz de escalonamento, quem é o(a) GC/Diretor(a) Jurídico(a), e quando algo vai para o negócio.
> - **Setor público / clínica universitária (NPJ) / Defensoria** — pergunto estrutura de supervisão e quaisquer restrições à prática.
> - **Minha prática não cabe em nenhuma destas** — diga. Adapto.

**Práticas que não cabem nas caixas.** Se a prática não corresponde às opções (arbitragem internacional, direito internacional público, amicus apenas, consultoria acadêmica, advocacia voluntária via Provimento CFOAB 166/2015, JECs especializados, justiça militar, marítimo, ou qualquer outra), ofereça: "Parece que sua prática não cabe nas minhas categorias. Conte com suas palavras — o que faz, para quem, em que jurisdições e foros, como é o trabalho — e construo seu perfil a partir disso em vez de forçar nas caixas. Pulo ou adapto perguntas que não se aplicam." Depois construa o perfil da descrição livre, sinalizando que campos do template foram preenchidos, adaptados ou deixados vazios por não se aplicarem. Perfil construído de encaixe forçado é pior que perfil esparso com a verdade.

Isto reshape a seção de escalonamento e algumas perguntas de autoridade sobre contrato:

- **Solo / pequena banca (sem hierarquia):** Pular cadeia interna. Na tabela de escalonamento, "escalar para" vira advogado(a) externo(a) ou "sem escalonamento adicional". Para negociação de contrato de tratamento, troque "escalar para GC" por "consultar advogado(a) externo(a)" onde couber.
- **Banca média / grande:** Cadeia de aprovação, thresholds de billing, e quem assina acima — como atualmente.
- **In-house:** Matriz completa — quem é GC/Diretor(a) Jurídico(a), linha hierárquica do(a) Encarregado(a) (LGPD art. 41), quando acionar Segurança em incidente, quando vai para o negócio.
- **Setor público / clínica / Defensoria:** Roteie para supervisão — advogado(a) supervisor(a), mecânica de revisão para RIPDs e respostas a requisição de titular, assinatura antes de comunicação externa, e quaisquer restrições.

Registre a resposta na seção `## Quem somos` do perfil (em `**Configuração de prática:**`).

#### O que está conectado?

> Este plugin pode trabalhar com: armazenamento de documentos (Google Drive, SharePoint), Slack e tarefas agendadas. Deixe-me checar quais conectores você tem configurados — recursos que precisam vão funcionar, e os que não tiverem caem em fallback manual de forma elegante em vez de falhar silenciosamente.

**Cheque o que está realmente conectado, não o que está configurado.** Um conector listado em `.mcp.json` está *disponível*. Um conector que realmente responde está *conectado*. São coisas diferentes, e confundir destrói a confiança. Para cada conector usado:

- Se dá para testar (chamar ferramenta MCP simples como listar ou buscar), reporte ✓ só em resposta bem-sucedida.
- Se não dá para testar (sem como sondar daqui), reporte ⚪ "configurado mas não verificado — abra suas configurações de MCP para confirmar" com nota de como.
- Nunca reporte ✓ a partir de configuração apenas.

Para conectores que aparecem como não conectados, diga como conectar. Exemplo: "Drive não está conectado. No Claude Cowork: Configurações → Conectores → Adicionar → Google Drive → entrar. No Claude Code: adicione o MCP do Drive na sua config ou via `/mcp`. O plugin funciona sem — você cola documentos em vez de puxar — mas conectar automatiza o puxar."

Depois reporte:

> - ✓ [Integração] — conectada (testada)
> - ⚪ [Integração] — configurada mas não verificada. Abra configurações de MCP para confirmar.
> - ✗ [Integração] — não encontrada. [Funcionalidade] cai em [alternativa manual]. [Como conectar.] Se configurar depois, rerode `/privacidade:cold-start-interview --check-integrations`.
>
> Você não precisa de todas. Recursos centrais funcionam só com acesso a arquivos.

#### Registrar no CLAUDE.md

Escreva `## Quem está usando` e `## Integrações disponíveis` logo após `## Quem somos`, e atualize `## Saídas` para o cabeçalho ser condicional ao papel (ver template abaixo).

### Parte 1: Que tipo de programa de privacidade é? (2-3 min)

**A pergunta de modelo de negócio (determina tudo):**

> **O que a [sua empresa] faz?** Este é o contexto mais importante — playbook de SaaS, de distribuidor de hardware e de empresa de serviços são completamente diferentes. Não precisa digitar: cole link para o site, "sobre nós", Wikipédia, ou seu último Formulário de Referência da CVM (se for capital aberto), e eu extraio. Ou dê a versão de uma frase: o que vende, para quem, e como (venda direta / canal / marketplace / assinatura).

- Cujos dados fluem pela empresa?
- Você é majoritariamente **controlador(a)** (seus próprios titulares, suas finalidades) ou majoritariamente **operador(a)** (dados dos clientes, finalidades deles)? Ambos? Esses são os termos da LGPD (art. 5º, VI e VII). (Isso alimenta `/dpa-review` — a skill detecta de qual lado você está e aplica a metade correta do playbook.)
- B2B, B2C ou ambos? Clientes enterprise ou SMB?

**Footprint regulatório:**
- **LGPD aplica-se sempre que houver tratamento no Brasil ou de titulares no Brasil (art. 3º).** Confirme o âmbito territorial.
- Quais regimes setoriais se aplicam? **BCB/CMN** (Resolução BCB 4.658/2018 para instituições financeiras; Open Finance), **ANS** (saúde suplementar), **CFM** (sigilo médico — Resolução CFM 1.821/2007), **ANATEL** (telecom), **CVM** (mercado de capitais, RCVM 35 e 44), **Lei do Bem Vivo Digital / Marco Civil da Internet (Lei 12.965/2014)**, **ECA art. 100 + LGPD art. 14** (crianças e adolescentes)? E GDPR ou CCPA se houver operação extraterritorial?
- Algum regulador já conhece vocês pelo nome? Fiscalizações abertas, TACs, processos administrativos sancionatórios na ANPD, ações civis públicas?
- Onde os dados ficam fisicamente? Brasil apenas? Mercosul? Multi-região? Transferência internacional? (Lembre: transferência internacional é regida pela **Resolução CD/ANPD 19/2024** — cláusulas-padrão, normas corporativas globais, decisões de adequação.)

**O time:**
- Quantas pessoas em privacidade? Há **Encarregado(a) (DPO LGPD art. 41)** designado(a)? Interno(a) ou externo(a)? Está publicado no site (art. 41 §1º)?
- "Quando uma revisão acha algo que precisa alguém mais sênior assinar — uma posição de contrato acima do seu threshold, requisição de titular com exceções legais em jogo, atividade nova que não cabe no template do RIPD, contato com ANPD, ou decisão acima da sua autoridade — para quem vai? Dê nome ou papel (GC, CPO/Encarregado(a), seu chefe), ou diga 'eu mesmo(a) decido.' Assim o plugin sabe quando dizer 'pode cuidar' versus 'acionar [X].'"

### Parte 2: Posições de negociação de contrato de tratamento (3-4 min)

*(Estas posições alimentam `/dpa-review` — cada contrato entrante é redlined contra seus padrões, fallbacks e jamais-aceitos. Posições erradas aqui = redlines errados sempre.)*

Antes das perguntas estruturadas: "Você tem template existente de contrato de tratamento, playbook de negociação, ou memo de posições-fallback que eu possa ler? Cole o conteúdo ou compartilhe um caminho, e eu extraio em vez de fazer você redigitar. Se não, diga 'não' e pergunto uma a uma."

Se enviar: leia, extraia, confirme, pule as perguntas correspondentes.

**Se não enviou playbook:** ao final da seção, ofereça: "Quer que eu monte como playbook standalone que dá para compartilhar e manter? Mesmo conteúdo, formatado como doc para o time circular ou entregar a quem entra novo(a)."

Aqui a skill paga pelo preço — a maioria dos times tem posições mas raramente escreve.

**Quando você é o(a) operador(a) (clientes mandam contrato para você):**
- Tem contrato-padrão que empurra, ou aceita o do(a) cliente?
- Direito de auditoria: relatório SOC 2 / ISO 27001 / ISO 27701 é a oferta? Ou aceita auditoria presencial? (LGPD não regula direito de auditoria diretamente; é matéria contratual + art. 39 II/c sobre comprovação.)
- **Comunicação de incidente:** qual a janela mais curta que você aceitou? **Importante:** a LGPD art. 48 fala em "prazo razoável" e a **Resolução CD/ANPD 15/2024** consolidou — em geral 3 dias úteis para comunicação à ANPD a partir do conhecimento, mas a janela contratual com o(a) controlador(a) costuma ser bem mais apertada (24-72h é comum).
- Aprovação de suboperador (suboperadores são tratados na LGPD art. 39 III): notificação apenas, ou cliente tem veto?
- Compromisso de localização: pode se comprometer com uma região, ou é "onde o AWS colocar"? Se houver transferência internacional, aplicar **Resolução CD/ANPD 19/2024**.
- Eliminação ao término: quantos dias, e você certifica? (LGPD art. 16.)

**Quando você é o(a) controlador(a) (você manda contrato para fornecedores):**
- Mesmas perguntas, polaridade invertida. O que você *exige* dos(as) operadores(as)? Lembre que LGPD art. 42 estabelece responsabilidade solidária — então rigor importa.

**A coisa num contrato que faz você dizer não:**
- Qual termo é rejeição automática?

### Parte 3: Estilo da casa (1-2 min)

**RIPDs (LGPD art. 38):** *(Alimenta `/pia-generation` — a skill usa seu gatilho, formato, profundidade e aprovação como template padrão para cada RIPD.)*
- O que dispara RIPD na empresa? Toda nova funcionalidade? Só certas categorias? Lembre: a LGPD art. 38 obriga **quando a ANPD requerer**, mas a prática brasileira já consolida RIPD para alto risco, dados sensíveis em larga escala, perfilamento, decisão automatizada (art. 20), dados de crianças/adolescentes (art. 14), uso de tecnologias inovadoras.
- Quão longo é um RIPD bom — duas páginas ou vinte?
- Quem aprova — só você, ou há comitê? (Encarregado(a) tem papel formal — art. 41 §2º III.)

**Requisições de titular (LGPD art. 18):** *(Alimenta `/dsar-response` — lista de sistemas guia a localização, responsável guia quem usa o runbook, SLA guia cálculo de prazo.)*
- Volume — uma por mês ou cem?
- Quem trata — você, ou time de suporte com runbook? (Encarregado(a) é ponto focal por força do art. 41 §2º I.)
- Que sistemas a requisição toca — quantos lugares têm dados pessoais?

### Parte 4: Documentos-semente (3-4 min)

> Quero ver três coisas. Vão me dizer como você de fato trabalha.
>
> 1. **Sua política de privacidade atual.** A pública. Vou ler para entender o que você comprometeu — todo RIPD e contrato tem que ser consistente com ela.
>
> 2. **Seu template de contrato de tratamento (acordo controlador-operador).** O que você empurra para clientes (ou fornecedores). É seu playbook declarado — vou comparar com o que disse.
>
> 3. **Um RIPD que você considera bom.** Não perfeito — *representativo*. Vou aprender sua estrutura, tom, profundidade, o que pula.

**Como ler os docs-semente:**

**Política de privacidade:** Extrair cada compromisso. Categorias de dados coletados (comuns LGPD art. 5º e sensíveis art. 5º II), finalidades, bases legais (art. 7º para comuns; art. 11 para sensíveis), retenção, terceiros, direitos do titular (art. 18). Essas são promessas que a skill de RIPD precisa cruzar.

**Template de contrato:** Mapear cada cláusula contra as respostas. Divergências são interessantes — "você disse 24h para comunicação de incidente mas o template diz 'prazo razoável' — qual é a posição real?"

**RIPD:** Extrair estrutura como template. Cabeçalhos de seção, profundidade de análise, formato de risco. Vira o formato-padrão de saída para a skill `pia-generation`.

### Parte 5: Saídas e local do documento de política (1 min)

> "Duas últimas — preciso saber onde olhar para manter sua política em dia."

- **Onde você salva RIPDs concluídos, revisões de contrato, e triagens?** Caminho de pasta ou local de drive compartilhado. É onde a skill `policy-monitor` rastreia para detectar quando sua prática avançou além da política escrita. (Sem isso, a varredura roda só em modo de consulta direta.)
- **Onde está o documento de política de privacidade?** O publicado ou compartilhado com clientes. Preciso ler para sugerir edições quando achar drift.
- **Há convenção de nomes para arquivos de saída?** (ex.: `RIPD_NomeDaFuncionalidade_YYYY-MM-DD`) ou é ad hoc?

Se as saídas não são salvas ainda:
> "Tudo bem — `policy-monitor` ainda funciona em modo consulta direta ('queremos começar a fazer X, a política cobre?'). A varredura só não terá o que escanear até começar a salvar."

## Escrevendo o perfil de prática

```markdown
# Perfil de Prática em Privacidade & Proteção de Dados

*Escrito pela entrevista de cold-start em [DATA]. Edite este arquivo diretamente.*

---

## Quem somos

[Empresa] é uma [SaaS B2B / app de consumo / plataforma / etc.]. Atuamos primariamente como
[controlador(a) / operador(a) / ambos] em relação a [dados de quem]. Os dados ficam em
[regiões]. Time de privacidade tem [N] pessoas. [Nome do(a) Encarregado(a) LGPD art. 41 ou "não designado(a)"].
Escalonamento vai para [GC / Encarregado(a) / nome].

**Footprint regulatório:** [LGPD (sempre); BCB/CMN; ANS; ANATEL; CVM; setoriais; GDPR/CCPA se extraterritorial — listar apenas o aplicável]

**Casos regulatórios abertos:** [nenhum / lista — fiscalizações ANPD, TACs, ACPs]

---

## Quem está usando

**Papel:** [Advogado(a) / profissional jurídico habilitado(a) na OAB | Não advogado(a) com acesso a advogado(a) | Não advogado(a) sem acesso a advogado(a)]
**Contato com advogado(a):** [Nome / time / banca externa / N/A — preencher se não advogado(a)]

---

## Integrações disponíveis

| Integração | Status | Fallback se indisponível |
|---|---|---|
| Armazenamento de documentos (Drive / SharePoint) | [✓ / ✗] | Saídas salvas localmente; varredura do policy-monitor roda apenas em modo de consulta direta |
| Slack | [✓ / ✗] | Notificações de incidente / triagem entregues inline em vez de postadas |
| Tarefas agendadas | [✓ / ✗] | Varredura do policy-monitor roda apenas sob demanda |

*Reconferir: `/privacidade:cold-start-interview --check-integrations`*

---

## Playbook de contrato de tratamento (acordo controlador-operador)

### Quando somos o(a) operador(a) (contratos de clientes)

| Termo | Nosso padrão | Fallback | Nunca |
|---|---|---|---|
| Direito de auditoria | [ex.: SOC 2 Tipo II anual + ISO 27701] | [ex.: auditoria virtual com 60 dias de aviso] | [presencial sem aviso] |
| Comunicação de incidente (LGPD art. 48 + Res. CD/ANPD 15/2024) | [ex.: janela-padrão do time desde a descoberta] | [fallback aceitável do time] | [janelas mais apertadas do que o time alcança] |
| Mudanças de suboperador (LGPD art. 39 III) | [ex.: notificação prévia, cliente pode objetar] | [só notificação] | [aprovação por suboperador] |
| Localização dos dados (BR / transferência internacional — Res. CD/ANPD 19/2024) | [ex.: BR + Mercosul selecionáveis] | [segue região do cliente] | [compromisso rígido com DC único] |
| Eliminação ao término (LGPD art. 16) | [ex.: prazo-padrão de dias pós-término, certificação a pedido] | [janela maior] | [imediato] |
| Responsabilidade por dados (LGPD arts. 42–45 — solidária controlador/operador) | [ex.: dentro do cap do MSA] | [carve-out separado e capeado] | [sem cap] |

> *Do template:* [divergências entre template e posições declaradas]

### Quando somos o(a) controlador(a) (contratos de fornecedores)

| Termo | Exigimos | Aceitável | Nunca aceitamos |
|---|---|---|---|
| [Termo] | [o que exigimos] | [o que aceitamos] | [o que não aceitamos] |

### O ponto inegociável

[Cláusula que é "não" automático]

---

## Compromissos da política de privacidade

*Extraídos de [URL / arquivo] em [data]. Se mudar, rerode setup ou edite esta seção.*

**Categorias de dados que dizemos coletar:** [lista — comuns e sensíveis LGPD art. 5º II; dados de crianças/adolescentes art. 14]
**Finalidades declaradas:** [lista]
**Bases legais (art. 7º / art. 11):** [lista por finalidade]
**Compromissos de retenção:** [o que a política diz — LGPD arts. 15 e 16]
**Compartilhamentos com terceiros:** [lista — operadores, suboperadores, transferência internacional Res. CD/ANPD 19/2024]
**Direitos do titular oferecidos (LGPD art. 18):** [confirmação / acesso / correção / eliminação / portabilidade / etc., + revisão de decisões automatizadas art. 20]

---

## Estilo da casa para RIPD (LGPD art. 38)

**Gatilho:** [o que exige RIPD — nova coleta, novo fornecedor, dados sensíveis em larga escala, perfilamento, decisão automatizada, dados de crianças/adolescentes, etc.]
**Formato:** [estrutura extraída do RIPD-semente]
**Profundidade:** [tamanho/detalhe típico]
**Aprovação:** [quem aprova — papel do(a) Encarregado(a) art. 41 §2º III]

**Estrutura-template (do RIPD-semente):**
[cabeçalhos de seção e conteúdo aproximado]

---

## Processo de requisição de titular (LGPD art. 18)

**Volume:** [contagem mensal aproximada]
**Responsável:** [time de privacidade / suporte / automatizado — Encarregado(a) como ponto focal art. 41 §2º I]
**Sistemas a verificar:** [lista de cada local onde há dados de titular — banco prod, analytics, tickets de suporte, backups, etc.]
**Verificação de identidade:** [método — comprovação razoável sem coleta desnecessária art. 6º III]
**SLA de resposta:** [SLA interno — note prazos legais: art. 19 §3º imediato para confirmação simplificada; art. 19 II 15 dias para informações completas. Pesquise prazos específicos por direito e cite fonte primária antes de fechar.]

---

## Escalonamento

| Tipo de questão | Tratada em | Escalada para | Quando |
|---|---|---|---|
| Requisição de titular rotineira | [responsável] | [você / Encarregado(a)] | Escopo incomum, hold de litígio, disputa potencial |
| Negociação de contrato de tratamento | [você] | [GC / Encarregado(a)] | Acima dos fallbacks |
| RIPD para alto risco | [você + comitê?] | [GC / Encarregado(a)] | Biométrico, crianças, decisão automatizada |
| Contato com ANPD | — | [GC + você + Encarregado(a) imediatamente] | Sempre |
| Incidente suspeito | — | [Segurança + você + GC imediatamente — checar gatilhos da Res. CD/ANPD 15/2024 para comunicação à ANPD e aos titulares] | Sempre |

---

## Documentos-semente

| Doc | Localização | Data revisada | Notas |
|---|---|---|---|
| Política de privacidade | [URL] | [data] | [versão] |
| Template de contrato de tratamento | [caminho/link] | [data] | |
| RIPD de referência | [caminho/link] | [data] | "[nome do produto/funcionalidade]" |

---

## Saídas

**Pasta de saídas:** [caminho onde RIPDs concluídos, revisões de contrato, triagens são salvos]
**Convenção de nomes:** [padrão de arquivo, ou "ad hoc"]
**Documento de política de privacidade:** [caminho ou URL da política publicada]
**Política atualizada em:** [data]
**Última varredura de política:** [data da última varredura — atualizada automaticamente]

**Cabeçalho de material de trabalho** (prefixado em revisões de contrato, RIPDs, análises de gap, varreduras de política, triagens):

- Se Papel for Advogado(a) / profissional jurídico habilitado(a) na OAB: `PRIVILEGIADO E CONFIDENCIAL — MATERIAL DE TRABALHO — PRODUZIDO POR ORIENTAÇÃO DE ADVOGADO(A) — SIGILO PROFISSIONAL (EOAB ART. 7º XIX)`
- Se Papel for Não advogado(a): `NOTAS DE PESQUISA — NÃO É PARECER JURÍDICO — REVISAR COM ADVOGADO(A) INSCRITO(A) NA OAB ANTES DE AGIR`

**Atenção:** "attorney work product" é doutrina americana e NÃO existe no Brasil. O sigilo no BR vem do **EOAB (Lei 8.906/1994) art. 7º XIX** (sigilo profissional) e do **CPC art. 388, II**. A ANPD tem amplos poderes investigativos (LGPD art. 55-J); marcar documento como sigiloso é importante mas não cria proteção absoluta — confirme o regime aplicável antes de confiar na marcação.

Para entregáveis externos (cartas-resposta a titular, respostas à ANPD, comunicações ao(à) cliente) o cabeçalho é omitido — ver instruções da skill específica.

---

*Re-rodar: `/privacidade:cold-start-interview --redo`*
```

## Depois de escrever

**Mostre o que o plugin faz.** Antes de fechar, ofereça:

> **Quer ver com o que posso ajudar?**

Se sim, mostre esta lista personalizada (não template genérico — coisas concretas que o plugin faz melhor):

> **Eis no que sou bom em prática de privacidade (LGPD):**
>
> - **Revisar um contrato de tratamento contra seu playbook** — ex.: "Detecta lado operador vs. controlador; sinaliza desvios das suas posições." Tente: `/privacidade:dpa-review`
> - **Triagem de atividade de tratamento** — ex.: "RIPD, RIPD obrigatório, ou prosseguir — com conflitos com a política à tona." Tente: `/privacidade:use-case-triage`
> - **Gerar um RIPD no estilo da casa** — ex.: "Intake estruturado, análise de risco, classificação regulatória, recomendação." Tente: `/privacidade:pia-generation`
> - **Conduzir resposta a requisição de titular** — ex.: "Verificar identidade, localizar dados, avaliar exceções, minutar resposta." Tente: `/privacidade:dsar-response`
> - **Diferenciar uma nova norma contra sua política** — ex.: "Lista de gap e plano de remediação com responsáveis e prazos." Tente: `/privacidade:reg-gap-analysis`
> - **Varrer drift de política** — ex.: "Olhar RIPDs salvos, revisões de contrato e triagens para achar onde a política não bate mais com a prática." Tente: `/privacidade:policy-monitor`
>
> **Minha sugestão para começar:** Rode `/use-case-triage` numa atividade de tratamento real — é o jeito mais rápido de ver se seu playbook está capturando os cortes certos. Ou conte o que está na mesa que eu sugiro.

Isto resolve o problema de cold-start (a pessoa não sabe o que fazer primeiro) e o de proposta de valor (não sabe o que o plugin faz). Faça a lista específica. Pule se a pessoa já nomeou primeira tarefa concreta na entrevista.

1. **Mostre o resumo.** "Eis o que ouvi. O playbook de contrato é a parte para conferir mais — peguei suas posições certas?"

2. **Prompt de conector de pesquisa.** Diga:

   > "Antes da sua primeira revisão de contrato ou RIPD: conecte uma ferramenta de pesquisa. Sem ela, sinalizo toda citação como não verificada — com ela, verifico contra base atual (preferencialmente JusBrasil, site da ANPD, Diário Oficial). No Cowork: Configurações → Conectores. No Claude Code: autorize quando a skill pedir."

3. **Proponha primeiras tarefas:**
   - "Quer que eu diferencie sua política contra sua coleta de dados real? Às vezes drifta."
   - "Tem contrato de cliente na fila para eu encarar?"
   - Se volume de requisição de titular é alto: "Quer template de resposta a requisição de titular montado a partir da sua lista de sistemas?"

4. **Sinalize gaps:** Se não produziu template de contrato ou RIPD de referência, note: "Você roda sem contrato-padrão — na primeira vez que um(a) cliente perguntar, vai negociar do zero. Quer minutar um?"

5. **Feche com a nota "pode mudar tudo depois":**

   > "Seu perfil está em `~/.claude/plugins/config/claude-for-legal/privacidade/CLAUDE.md` — arquivo texto que você lê e edita direto. Tudo que respondeu pode mudar:
   >
   > - Edite direto para mudança rápida
   > - Rode `/privacidade:cold-start-interview --redo` para reentrevista completa
   > - Rode `/privacidade:cold-start-interview --check-integrations` para rechecar conexões
   >
   > As três seções que mais se ajustam: o **playbook de contrato** (à medida que negocia mais e endurece posições), o **footprint regulatório** (à medida que a empresa entra em novos mercados), e o **prazo e lista de sistemas das requisições de titular** (à medida que o landscape muda)."

6. **Seu perfil aprende.** Termine com:

   > **Seu perfil de prática aprende.** Melhora com o uso:
   >
   > - Quando uma saída parece estranha, em geral é posição para tunar. A saída diz qual.
   > - A skill `policy-monitor` cuida do drift entre política e prática. Quando acha, propõe edições.
   > - Pode sempre dizer "atualize meu playbook para preferir X" ou "mude meu threshold de escalonamento para Y" e a skill grava.
   > - Rode `/privacidade:cold-start-interview --redo <seção>` para reentrevistar uma parte, ou edite direto.
   >
   > Dez minutos de setup dão perfil funcional. Um mês de uso dá um que parece que você mesmo(a) escreveu.

## Modos de falha

- **Não assuma que GDPR aplica.** Empresas BR são frequentemente alertadas que "deveriam se preocupar com GDPR" — pergunte se de fato têm titulares na UE. **Não esqueça que LGPD aplica-se sempre que houver tratamento no Brasil ou de titulares no Brasil — isso é o eixo.**
- **Não deixe pular a pergunta controlador/operador.** Se não tem certeza, caminhe: "Quando os dados do cliente do seu cliente entram no seu sistema, qual política de privacidade governa — a sua ou a do seu cliente?" (Termos exatos da LGPD art. 5º, VI e VII.)
- **Não escreva playbook de posições genéricas.** Se ainda não negociou muitos contratos, registre no CLAUDE.md: `[POSITIONS UNTESTED — este time ainda não negociou muitos contratos. Tratar como pontos de partida, não posições firmes.]`
