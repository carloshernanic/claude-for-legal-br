---
name: oss-review
description: >
  Compliance de licença open source para lista de dependências, biblioteca
  única ou código a ser publicado. Use ao revisar manifesto, SBOM ou repo
  por obrigações de copyleft e compatibilidade de licença, quando pedirem
  se uma biblioteca pode ir para produção, ou ao preparar código para
  open-sourcing.
argument-hint: "[caminho de arquivo do manifesto / SBOM | nome do pacote | caminho do repo | colar texto]"
---

# /oss-review

Roda compliance de licença open source contra o perfil de prática em
`~/.claude/plugins/config/claude-for-legal/propriedade-intelectual/CLAUDE.md`. Classifica
dependências por família de licença, mapeia obrigações ao modelo de
deployment, sinaliza pacotes de licença-desconhecida e licenças
não-OSI-disfarçadas-de-OSS, e recomenda ações — cumprir, substituir,
remover, buscar revisão jurídica, buscar licença comercial.

## Instruções

1. **Carregar `~/.claude/plugins/config/claude-for-legal/propriedade-intelectual/CLAUDE.md`.**
   Se houver placeholders, pare e oriente: "Rode
   `/propriedade-intelectual:cold-start-interview` primeiro — preciso aprender seu perfil
   de prática (e política de OSS, se houver) antes de revisar." Se o
   perfil aponta para política OSS carregada, leia também — é a fonte de
   verdade sobre licenças aceitas / a revisar / banidas neste time.

2. **Estabelecer o escopo:** lista de dependências (package.json,
   requirements.txt, go.mod, Gemfile, Cargo.toml, pom.xml, SBOM),
   biblioteca única, ou código a ser publicado pelo time. Se o usuário
   passou caminho, infira do arquivo; senão, pergunte.

3. **Estabelecer o modelo de deployment** antes de classificar
   obrigações — SaaS, binário distribuído, apenas interno, ou embarcado.
   A mesma lista de dependências dispara obrigações distintas conforme
   isto.

4. **Siga o workflow abaixo.** Em particular:
   - Leia o texto efetivo da licença, não só metadados — arquivos LICENSE
     podem estar errados; metadados de pacote podem estar desatualizados.
   - Classifique cada pacote em permissiva / copyleft fraco / copyleft
     forte / domínio público / não-OSI / desconhecida.
   - Sinalize licença-desconhecida como "precisa revisão", não permissiva
     por default.
   - Sinalize licenças source-available não-OSI (SSPL, BUSL, Commons
     Clause, Elastic License, fair-source) — não são open source.
   - Para código a ser publicado, cheque se a licença de saída escolhida é
     compatível com toda dependência embarcada.

5. **Produza o memorando** conforme o template — cabeçalho de
   work-product primeiro, bottom line, sinalizações de topo de memorando,
   blocos por pacote agrupados por severidade, nota de jurisdição, check
   de saída (se aplicável), roteamento de aprovação.

6. **Respeite a postura de decisão.** Quando análise de gatilho de
   copyleft depender de questão contestada (AGPL "interage por rede",
   GPL-3.0 "conveying", escopo de linking LGPL), sinalize para revisão de
   advogado(a) e exponha os fatores cortando para ambos os lados.
   Qualquer coisa sinalizada como copyleft forte ou licença-desconhecida
   vai a advogado(a) antes de a dependência ir a produção ou o código
   ser publicado.

## Exemplos

```
/propriedade-intelectual:oss-review ~/code/meu-projeto/package.json
/propriedade-intelectual:oss-review ~/code/meu-projeto/requirements.txt
/propriedade-intelectual:oss-review redis
/propriedade-intelectual:oss-review ~/code/meu-projeto  # raiz do repo — escaneia todos os manifestos
```

---

## Funciona melhor conectado

Pedidos de clearance de OSS normalmente chegam por sistema de tickets.
Conectado a Jira, Linear ou Asana, esta skill pode: monitorar pedidos
entrantes de OSS, responder com orientação direta no ticket (sinalizando
info incompleta, pedindo link do repo, retornando a classificação da
família de licença) e rastrear status de clearance entre pedidos.

Sem conector, cole o ticket ou descreva o pedido e eu trato um por vez.
Vide `CONNECTORS.md` na raiz do repo para como adicionar conector de
tickets.

## Contexto de matéria

**Contexto de matéria.** Cheque `## Workspaces de matéria` no CLAUDE.md
de nível de prática. Se `Habilitado` for `✗` (default para in-house),
pule o resto deste parágrafo — skills usam contexto de nível de prática e
a maquinaria de matéria fica invisível. Se habilitado e não houver
matéria ativa, pergunte: "De qual matéria é isto? Rode
`/propriedade-intelectual:matter-workspace switch <slug>` ou diga `nível de prática`."
Carregue o `matter.md` da matéria ativa. Grave saídas na pasta da matéria
em `~/.claude/plugins/config/claude-for-legal/propriedade-intelectual/matters/<matter-slug>/`.
Nunca leia arquivos de outra matéria salvo se `Contexto cross-matéria`
estiver `on`.

---

## Propósito

Diga ao usuário quais licenças estão na árvore de dependências, que
obrigações essas licenças disparam dado como o código vai ser
implantado, e o que fazer com cada uma. A saída é memorando em que o(a)
advogado(a) (ou engenheiro(a) com acesso a advogado(a)) pode agir —
cumprir, substituir, remover, buscar revisão jurídica, buscar licença
comercial.

**Esta é classificação de primeira passagem.** Análise de copyleft
depende do modelo de deployment, do grau de linking, da jurisdição, e às
vezes de questões jurídicas que não foram testadas em corte (notavelmente
"interage por rede" do AGPL, cláusula de patente do GPL-3.0). Para
qualquer coisa classificada como copyleft forte ou
licença-desconhecida, advogado(a) avalia antes de a dependência ir a
produção ou o código ser publicado. A skill reporta o que achou; o(a)
advogado(a) decide o que fazer.

## Pré-condição: carregar o perfil de prática

**Antes de escanear dependências, leia
`~/.claude/plugins/config/claude-for-legal/propriedade-intelectual/CLAUDE.md`.** Se
faltar ou ainda contiver placeholders, pare e rode
`/propriedade-intelectual:cold-start-interview`. O perfil diz:

- Quem é dono da revisão OSS neste time (frequentemente engenharia com
  sign-off do jurídico)
- Roteamento de escalada para obrigações de copyleft
- O cabeçalho de work-product a prefixar

Se o perfil tem política OSS carregada, leia também — é a fonte de verdade
para quais licenças o time aceita, quais disparam revisão e quais são
banidas.

## Workflow

### Passo 1: Qual é o escopo?

Pergunte (ou infira do que o usuário forneceu):

> O que estamos revisando?
>
> 1. **Lista de dependências** — `package.json`, `requirements.txt`,
>    `go.mod`, `Gemfile`, `Cargo.toml`, `pom.xml`, SBOM (SPDX /
>    CycloneDX), lockfile
> 2. **Biblioteca única** — um pacote específico que estão considerando
>    adicionar
> 3. **Nosso próprio código** — vamos publicar e precisamos checar o
>    que está embarcado

O caminho da análise difere:

- Lista de dependências → classifica toda entrada, agrega obrigações
- Biblioteca única → classifica um pacote e percorre as transitivas se
  disponíveis
- Código de saída → checa o que está embarcado (direto e transitivo);
  checa se a licença de saída escolhida é compatível com todas as
  embarcadas; checa se LICENSE / NOTICE estão corretos

### Passo 2: Qual é o modelo de deployment?

É o input mais importante depois da lista de licenças — a mesma
biblioteca carrega obrigações distintas dependendo de como o software é
entregue. Pergunte:

> Como isto será implantado?
>
> 1. **SaaS / serviço hospedado** — usuários acessam pela rede; nada
>    é enviado ao usuário
> 2. **Binário distribuído** — entregamos código compilado aos usuários
>    (app desktop, app móvel, servidor on-prem, ferramenta CLI)
> 3. **Apenas interno** — usado só dentro da empresa, não distribuído
>    para fora
> 4. **Embarcado / firmware** — entregue em hardware ou firmware de
>    sistema fechado

| Deployment | Licenças que importam materialmente |
|---|---|
| SaaS | AGPL (gatilho de rede), atribuição permissiva em qualquer UI, SSPL/BUSL/Elastic se reaproveitando como serviço concorrente |
| Binário distribuído | GPL, LGPL, MPL, EPL (todas disparam em distribuição), atribuição permissiva |
| Apenas interno | A maioria do copyleft não dispara — sem distribuição. Atribuição permissiva ainda boa higiene. AGPL ainda dispara se usuários fora da empresa interagem pela rede. |
| Embarcado / firmware | GPL é especialmente difícil de cumprir aqui (divulgação de fonte + build reproduzível + informação de instalação em alguns casos). Planeje antes de embarcar, não depois. |

Sinalize o modelo de deployment no memorando de saída — a mesma lista
revisada contra "SaaS" vs "binário distribuído" dá obrigações distintas.

### Passo 3: Classificar cada dependência

Para todo pacote, determine a licença. **Leia o texto efetivo da
licença, não só metadados** — arquivos LICENSE podem estar errados (o
arquivo diz MIT mas os cabeçalhos dizem GPL; o README alega Apache mas
não há arquivo de licença), e metadados de gerenciador de pacote podem
estar desatualizados.

Classifique em:

| Balde | Exemplos | Obrigações-chave |
|---|---|---|
| **Permissiva** | MIT, BSD-2-Clause, BSD-3-Clause, Apache-2.0, ISC, Zlib, Unlicense | Atribuição, preservar texto da licença, Apache-2.0 acrescenta patent grant + requisito NOTICE |
| **Copyleft fraco** | LGPL-2.1, LGPL-3.0, MPL-2.0, EPL-1.0, EPL-2.0, CDDL | Divulgação de fonte por arquivo ou por biblioteca; regras de linking variam |
| **Copyleft forte** | GPL-2.0, GPL-3.0, AGPL-3.0, OSL, EUPL (dependendo da versão) | Divulgação ampla de fonte; AGPL estende a uso por rede |
| **Domínio público / dedicação** | CC0, Unlicense, WTFPL | Tipicamente sem obrigações, mas alguns contestados em jurisdições que não reconhecem dedicação a domínio público — **Brasil é uma delas**: a Lei 9.610 exige cessão expressa e formal de patrimoniais; dedicação informal a domínio público pode ser interpretada como licença ampla, mas o autor pode revogar |
| **Source-available não-OSI** | SSPL, BUSL, Commons Clause, Elastic License, Confluent Community, família fair-source | Não são open source — restringem uso comercial, uso em serviço concorrente, ou ambos. Leia a licença específica. |
| **Outra / customizada / desconhecida** | específica de fornecedor, proprietária, arquivo de licença faltando, conflito entre arquivo e cabeçalhos | Pare — não trate como permissiva por default |

Sinalize:

- **Pacotes dual-licensed** — qual licença estamos usando? A escolha
  pode mudar obrigações.
- **Pacotes deprecated** — o pacote não é mais mantido; há substituto
  suportado?
- **Pacotes com dependência copyleft na própria árvore** — licença de
  topo é permissiva mas transitiva é copyleft.
- **Pacotes que mudaram de licença recentemente** — Redis, MongoDB,
  Elastic, HashiCorp — verifique que a versão pinada está sob a licença
  que você pensa.

### Passo 4: Mapear obrigações ao modelo de deployment

Para cada dependência classificada, declare o que o modelo de deployment
dispara:

```markdown
### [pacote@versão] — [Licença]

**Classificação:** [Permissiva / Copyleft fraco / Copyleft forte /
Domínio público / Não-OSI / Desconhecida]

**Obrigações para nosso deployment ([SaaS / binário / interno / embarcado]):**

- [ ] [Obrigação específica — ex.: "Incluir atribuição em arquivo
  NOTICES enviado com o app"]
- [ ] [ex.: "Se modificarmos e distribuirmos, publicar fonte das nossas
  modificações"]
- [ ] [ex.: "Gatilho de rede AGPL — se usuários acessam nossa versão
  modificada pela rede, fonte deve ser oferecida a eles"]

**Risco:** 🔴 Crítico | 🟠 Alto | 🟡 Médio | 🟢 Baixo

**Recomendação:** [Cumprir obrigações | Substituir por [alternativa] |
Remover | Revisão de advogado(a) antes de embarcar | Buscar licença
comercial de [fornecedor]]
```

> **Como a dependência copyleft é consumida?** A relação de linking
> determina se copyleft efetivamente dispara. Pergunte ou determine:
> - **Linking estático / compilação junta:** Os trabalhos são combinados
>   em um binário. Sinal forte de que copyleft dispara (LGPL "work based
>   on the Library", GPL obra derivada).
> - **Linking dinâmico / biblioteca compartilhada:** Os trabalhos
>   permanecem separáveis em runtime. LGPL permite explicitamente
>   ("work that uses the Library"). Posição do GPL é contestada (FSF
>   diz derivada, outros discordam).
> - **Inclusão de header / funções inline:** Pode criar obra derivada
>   dependendo do quanto é incluído.
> - **Subprocesso / IPC:** Processos separados comunicando por
>   interfaces bem-definidas. Geralmente não derivada.
> - **Chamada de API por rede:** Para a maioria das licenças, não. Para
>   **AGPL**, a cláusula de interação por rede significa que servir o
>   software pela rede É distribuição. Em arquitetura de microsserviços,
>   componente AGPL atrás de uma API ainda dispara.
> - **Copyleft por arquivo (MPL):** Apenas arquivos modificados carregam
>   copyleft, não a obra inteira. Cheque se algum arquivo copyleft foi
>   modificado.
>
> **A classificação de severidade depende disto.** "LGPL — copyleft
> fraco, regras de linking variam" sem a análise de linking é a resposta
> que mete engenheiro em apuro. LGPL com linking estático em produto
> proprietário é 🔴 Crítico. LGPL com linking dinâmico é 🟢 Baixo. Mesma
> licença, severidade oposta.

**Calibração de severidade:**

| Nível | Significa |
|---|---|
| 🔴 Crítico | Copyleft forte em deployment que dispara (ex.: GPL em binário distribuído, AGPL em SaaS). Licença não-OSI que o modelo de negócio efetivamente conflita (ex.: SSPL enquanto montamos serviço gerenciado). Licença indeterminável e o pacote é load-bearing. |
| 🟠 Alto | Copyleft fraco com obrigações para as quais o time não se preparou (divulgação por arquivo, requisitos NOTICE). Dual-licensed com escolhida ambígua. Arquivo de licença diz uma coisa, cabeçalhos dizem outra. |
| 🟡 Médio | Permissiva com requisitos de atribuição que não foram cabeados no build (sem arquivo NOTICES, sem LICENSE na distribuição). Copyleft transitivo em posição que pode ou não disparar, dependendo de como a biblioteca é consumida. |
| 🟢 Baixo | Permissiva com obrigações já satisfeitas. Copyleft em modelo de deployment que não dispara (ex.: biblioteca GPL usada apenas internamente, sem redistribuição). |

### Passo 5: Sinalizar modos de falha

Aponte qualquer destes em seção de topo do memorando:

- **Licença desconhecida** — classifique como "precisa revisão", não
  permissiva. Dependência não-classificada deveria parar decisão de
  embarque, não passar batido.
- **Arquivo de licença conflita com cabeçalhos de arquivo** — leia os
  dois e reporte o conflito.
- **Combinações incompatíveis** — GPL-2.0 only + Apache-2.0
  historicamente conhecida como incompatível; cheque combinações MPL /
  EPL / GPL com cuidado.
- **Licenças não-OSI se passando por open source** — SSPL, BUSL,
  Commons Clause, Elastic License, Confluent Community. Leia a licença;
  não confie no badge "open source" do GitHub.
- **Mudanças de licença** — se versão anterior era permissiva e atual é
  source-available, o pin importa.

### Passo 6: Check de saída (se revisando nosso próprio código antes de open-sourcing)

Se o usuário está preparando para publicar código:

- Confirme que a licença de saída escolhida é compatível com licença de
  toda dependência embarcada (ex.: não pode publicar sob MIT se
  embarcou código GPL — a obra combinada deve ser GPL)
- Confirme que arquivo LICENSE está presente e correto
- Confirme que arquivo NOTICE está presente e lista atribuições
  requeridas (Apache-2.0 e outras)
- Confirme que textos de licença de terceiros estão pacotados onde
  requerido
- Confirme nenhum código proprietário ou confidencial, nenhum dado de
  cliente, nenhuma credencial embarcada na história do repo
- Confirme política de marca para qualquer nome de projeto (separada da
  licença de direito autoral) — registro de marca para nome de projeto
  via INPI (LPI 122+)

### Passo 7: Montar o memorando

Prefixe o cabeçalho de work-product de
`~/.claude/plugins/config/claude-for-legal/propriedade-intelectual/CLAUDE.md` →
`## Outputs` (varia por papel — vide `## Quem está usando`).

Este memorando e qualquer lista revisada podem estar sob sigilo
profissional, confidenciais, ou ambos. Distribua apenas dentro do
círculo do sigilo; remova o cabeçalho de work-product antes de qualquer
entrega externa (inclusive antes de anexar a ticket de engenharia fora
do círculo).

> **Sem suplementação silenciosa.** Se consulta a ferramenta de pesquisa
> retornar poucos ou nenhum resultado para regra que o memorando
> precisa (exequibilidade do gatilho de rede AGPL em jurisdição dada,
> escopo do patent grant do GPL-3.0, texto de licença mais recente para
> pacote recentemente re-licenciado), reporte o achado e pare. NÃO
> preencha a lacuna a partir de web search ou conhecimento do modelo sem
> perguntar. Diga: "A busca retornou [N] resultados de [ferramenta].
> Cobertura aparenta ser fina para [regra / licença / jurisdição].
> Opções: (1) ampliar a query, (2) tentar outra ferramenta, (3) buscar
> na web — resultados serão tagueados `[web search — verificar]` e
> devem ser checados contra fonte primária antes de confiar, ou (4)
> sinalizar como não-verificado e parar. Qual prefere?" Advogado(a)
> decide se aceita fontes de menor confiança.
>
> **Atribuição de fonte.** Onde o memorando citar texto de licença,
> decisão judicial interpretando licença, ou orientação de steward
> (FSF, OSI, SPDX, SFLC), tague: `[OSI]`, `[SPDX]`, `[FSF]`,
> `[SFC/SFLC]`, `[JusBrasil]`, `[STJ]`, ou o nome da ferramenta MCP
> para citações recuperadas por conector; `[web search — verificar]`
> para citações de busca web; `[conhecimento do modelo — verificar]`
> para citações recordadas de treinamento; `[fornecido pelo usuário]`
> para texto de licença lido direto do repo. Citações tagueadas
> `verificar` carregam risco de fabricação maior. Nunca remova ou
> colapse as tags.

```markdown
[CABEÇALHO DE WORK-PRODUCT — conforme config do plugin ## Outputs]

# Revisão OSS: [Projeto / Lista de Dependências / Pacote]

**Revisado:** [data]
**Escopo:** [Lista de dependências / Biblioteca única / Código de saída]
**Modelo de deployment:** [SaaS / Binário / Interno / Embarcado]

---

## Bottom line

[Duas frases. Pode ir a produção? O que tem de acontecer primeiro?]

**Pacotes revisados:** [N]
**Por classificação:** [N permissiva, N copyleft fraco, N copyleft forte,
N domínio público, N não-OSI, N desconhecida]
**Issues:** [N]🔴 [N]🟠 [N]🟡 [N]🟢

**Aprovação necessária de:** [nome, conforme perfil de prática]

---

## Sinalizações de topo

[Lista de licença-desconhecida, lista de conflito de licença, lista de
não-OSI-disfarçada-de-OSS, combinações incompatíveis]

---

## Por pacote

[Blocos do Passo 4, agrupados por severidade]

---

## Nota de jurisdição

Exequibilidade de licença OSS varia — gatilho de rede do AGPL não foi
amplamente testado em corte; cláusula de patente do GPL-3.0 lê
diferentemente sob regimes distintos; dedicações a domínio público não
são universalmente reconhecidas (**Brasil tipicamente exige cessão
expressa formal — Lei 9.610**). Indique a escolha de lei aplicável para
qualquer distribuição downstream (ex.: contratos de fornecedor
incorporando o código) e sinalize jurisdições que o perfil de prática
marca como escalar.

---

## Check de saída (se aplicável)

[Do Passo 6]

---

## Roteamento de aprovação

[Do perfil de prática — quem aprova, o que dispara escalada automática]
```

## Postura de decisão

Quando licença não pode ser classificada com confiança, sinalize como
**"precisa revisão"** — não chame de permissiva. Sub-classificar risco
de licença é porta de uma via: decisão de embarque feita sob suposição
de permissiva-por-default vira obrigação de divulgação de fonte ou
injunção meses depois. Sobre-sinalizar é porta de duas vias — o(a)
advogado(a) estreita a lista na revisão.

Igualmente, quando análise de gatilho de copyleft depende de questão
contestada (AGPL "interage por rede", GPL-3.0 "conveying", escopo de
linking LGPL), sinalize para revisão de advogado(a) e exponha fatores
cortando para ambos os lados.

## Checagens de qualidade antes de entregar

- [ ] Perfil de prática e qualquer política OSS foram carregados
- [ ] Modelo de deployment foi estabelecido antes de classificar
  obrigações
- [ ] Toda dependência tem classificação, inclusive transitivas onde
  disponíveis
- [ ] Pacotes de licença-desconhecida são sinalizados, não default a
  permissiva
- [ ] Texto da licença foi lido (não só metadados) para qualquer achado
  de copyleft ou não-OSI
- [ ] Tags de fonte aplicadas; nenhuma tag `verificar` removida
- [ ] Aprovador nomeado conforme perfil de prática
- [ ] Saída marcada com o cabeçalho de work-product

## Encerre com a árvore de decisão de próximos passos

Encerre com a árvore de decisão de próximos passos conforme CLAUDE.md
`## Outputs`. Customize as opções ao que esta skill acabou de produzir —
os cinco ramos default são ponto de partida, não trava. A árvore é a
saída; o(a) advogado(a) escolhe.

Se o scan trouxe mais de ~10 pacotes, ou sempre que o usuário pedir:
ofereça o dashboard (vide CLAUDE.md `## Outputs → Oferta de dashboard
para outputs com muitos dados`). Forme a oferta ao que é útil aqui —
contagens por família de licença (permissiva / copyleft fraco /
copyleft forte / AGPL / proprietária / desconhecida), distribuição de
risco, e tabela de achados com severidade e versão de pacote.
