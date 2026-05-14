---
name: chronology
description: Constrói ou atualiza cronologia a partir de fontes documentais declaradas e uploads — eventos datados extraídos, deduplicados e marcados por relevância conforme a teoria da matéria. Use quando o(a) usuário(a) pedir cronologia ou linha do tempo a partir de uma produção / pasta de matéria, disser "monte a cronologia" ou "o que aconteceu quando", ou precisar de linha do tempo de trabalho, de exposição dos fatos (CPC 319 III) ou específica de testemunha.
argument-hint: "[slug] [--format=working|sof|witness-[nome]]"
---

# /chronology

> **Adaptação BR não oficial.** A versão original (US/UK) deste skill apoia-se em FRCP discovery, Bates numbering, eDiscovery (Everlaw / Relativity / DISCO / Aurora), implied undertaking (CPR 31.22) e plataformas equivalentes. Nenhum desses institutos tem equivalente direto no direito brasileiro. Mapa de equivalência:
>
> | Original (US/UK) | Equivalente funcional BR |
> |---|---|
> | Discovery / eDiscovery production | Produção antecipada (CPC 381); exibição de documento (CPC 396-404); requisição judicial (CPC 438) |
> | Bates numbering | Numeração de fls. (papel) ou eventos / IDs no PJe / e-SAJ / e-Proc / Projudi |
> | CPR 31.22 / implied undertaking | Segredo de justiça (CPC 189); CF art. 5º LX; sigilo decretado caso a caso |
> | FRCP 26(b)(3) / work product | Sigilo profissional (EOAB Lei 8.906/1994 art. 7º XIX e II; CPC 388 II; CPP 207) — não é "work product" |
> | Attorney-client privilege | Sigilo profissional (EOAB art. 7º) |
> | Common-interest / joint-defense | Sem doutrina codificada; protege-se via NDA / cláusula de cooperação |
> | Everlaw / Relativity / DISCO / Aurora | Sem equivalente nativo dominante; jurídico BR usa Drive / SharePoint / GED próprio (Legal One, Astrea) + extração manual via PJe / e-SAJ |
> | Statement of Facts (US brief) | Exposição dos fatos (CPC 319 III na inicial; CPC 336 na contestação) |
> | Statute of limitations | Prescrição (CC arts. 189-206; CDC art. 27; CLT art. 11) e decadência |
>
> **Atenção:** no Brasil **não há discovery amplo**. Documentos da contraparte são acessados via exibição (CPC 396-404), com presunção de veracidade pela recusa (CPC 400). Material elaborado pelo(a) advogado(a) é protegido pelo sigilo profissional (EOAB 7º XIX) — invocação caso-a-caso, sem privilege log formal.

1. Carregar `~/.claude/plugins/config/claude-for-legal/contencioso/matters/[slug]/matter.md` → teoria, fato-pivô, fatos-chave.
2. Carregar `~/.claude/plugins/config/claude-for-legal/contencioso/CLAUDE.md` → fontes de armazenamento documental, padrão de pasta de matéria.
3. Seguir o workflow e referência abaixo.
4. Identificar fontes na ordem: caminhos fornecidos pelo(a) usuário(a) nesta sessão, pasta padrão da matéria, fontes declaradas em `CLAUDE.md`.
5. Para fontes legíveis: extrair eventos datados. Para fontes inacessíveis: anotar em Lacunas.
6. Deduplicar, mesclar com lista de fontes por evento.
7. Marcar relevância (🔴/🟡/⚪) conforme a teoria da matéria.
8. Gravar `~/.claude/plugins/config/claude-for-legal/contencioso/matters/[slug]/chronology.md` (ou variante de formato conforme flag).
9. Se houver versão anterior: incrementar número de versão; apresentar diff ao(à) usuário(a).
10. Confirmar antes de finalizar: "Aqui está o que montei. Passe os olhos nos 🔴 — algum mal-classificado?"

---

# Cronologia

## Restrições de uso a documentos sob segredo de justiça / sigilo decretado

Antes de trabalhar com um conjunto de documentos de contencioso, pergunte: "Algum desses documentos veio de processo sob segredo de justiça, sigilo decretado em incidente, ou produção antecipada com restrição de uso?" Se sim:

- **Segredo de justiça (CPC art. 189; CF art. 5º LX):** documentos provenientes de autos sob segredo só podem ser usados nos próprios autos. Uso para outra matéria, fim comercial ou divulgação a terceiros exige autorização judicial ou enquadramento legal. Quebra pode caracterizar improbidade processual (CPC 80) e infração ética (EOAB art. 34).
- **Tutela de urgência com sigilo / sigilo bancário-fiscal (LC 105/2001; CTN 198):** o sigilo bancário e fiscal segue a fonte do dado, não a posse — uso desviado pode caracterizar quebra de sigilo (Lei 4.595/1964; CP arts. 153/154).
- **Acordos com cláusula de confidencialidade (CPC 166; Lei 13.140/2015 art. 30):** propostas de mediação e conciliação têm sigilo legal — não podem ser juntadas em outra ação.
- **Dados pessoais sensíveis (LGPD art. 11; CDC):** processamento secundário precisa de base legal e finalidade compatível.

Confirme: "Este uso está dentro dos autos em que os documentos foram produzidos, ou tenho autorização judicial, ou os documentos são públicos." Se não confirmado, flage: "⚠️ Documentos podem estar sob segredo de justiça ou sigilo decretado. Confirme licitude do uso antes de prosseguir."

## Propósito

Fatos acontecem em ordem. A cronologia é a espinha de qualquer narrativa — exposição dos fatos na inicial (CPC 319 III) ou contestação (CPC 336), memo de provisão (CPC 25), memo de acordo, preparação de oitiva e instrução de testemunha. Montar cronologia à mão é lento; IA é boa em extração estruturada. O risco: lixo entra, lixo sai. Este skill puxa das fontes declaradas na configuração e do que o(a) usuário(a) subir.

## Modos

Este skill atende dois cenários de prática. Pegue o default em `## Papel na prática` no CLAUDE.md de configuração; o(a) usuário(a) pode sobrescrever por execução com flag.

- **Modo `--matter` (default para advocacia in-house).** Foco em histórico da matéria. Lê teoria do caso e fatos-chave do `matter.md`, puxa de fontes declaradas em `## Armazenamento documental` no CLAUDE.md (Google Drive, SharePoint, Gmail, GED, CLM — o que estiver declarado), e trata `history.md` como log interno (decisões, holds, memos de provisão — intencionalmente fora da cronologia). Saída é centrada na matéria: o que aconteceu na disputa, marcado para uso em advocacia.
- **Modo `--documents` (default para advogado(a) de escritório / paralegal).** Foco em documentos produzidos. Lê teoria do caso na configuração, depois extrai de exportação de eDiscovery (se a banca usa), de conjunto de arquivos custodiais, ou de set de PDFs / fls. produzidos. Saída é centrada em documento: o que os documentos mostram, com referência a fls. ou IDs PJe, marcado conforme teoria do caso.

Ambos modos convergem para mesma estrutura de saída (linha do tempo, tags 🔴/🟡/⚪, lacunas, variante exposição-de-fatos). A diferença é o perfil de fonte e o frame de relevância.

Se `## Papel na prática` é `solo` ou `outro`, default para `--matter` mas mencione ambos os modos na primeira rodada e deixe o(a) usuário(a) escolher.

## Enquadramento por polo (tags de relevância)

O mesmo evento é relevante de forma diferente conforme se está provando um pedido ou desconstruindo. Leia `## Polo` no perfil prático (e a postura por matéria se a matéria sobrescrever o default):

- **Autor (enquadramento ofensivo)** — 🔴 marca eventos que *estabelecem* elementos da causa de pedir (responsabilidade, nexo causal, dano, ciência), *fecham* lacunas que a contraparte tentará abrir, ou *deflagram* prazos prescricionais a favor do(a) autor. 🟡 marca eventos que apoiam o pedido mas estão sujeitos a impugnação. ⚪ é contexto de fundo.
- **Réu (enquadramento defensivo)** — 🔴 marca eventos que *quebram* elementos da causa de pedir (ausência de nexo, falta de ciência, ausência de dano), *abrem* prescrição (CC 189-206; CDC 27; CLT 11), *deflagram* preliminares (CPC 337), ou *sustentam* exceções (compensação, prescrição, ilegitimidade, conexão, litispendência). 🟡 marca eventos que desconstroem narrativa do(a) autor. ⚪ é fundo.
- **Ambos / varia** — perguntar ao(à) usuário(a) por cronologia qual enquadramento aplicar. A linha do tempo é neutra; só a leitura de relevância muda.

Anotar o enquadramento aplicado no topo da saída: `Tags de relevância aplicadas no enquadramento [autor / réu].` Ao produzir variante de exposição de fatos, usar o default do polo salvo se o(a) usuário(a) especificar diferente.

## Carregar contexto

Comum:
- CLAUDE.md de configuração do plugin → contexto de teoria do caso (in-house: `## Armazenamento documental` para fontes; advogado(a) de escritório: `## Case theory` / `## Document review` para plataforma + custodiantes), `## Saídas` para cabeçalho de material de trabalho, `## Postura decisória` para regra de flag de sigilo.
- `chronology.md` anterior para esta matéria, se existir.
- Quaisquer arquivos que o(a) usuário(a) suba ou caminhos fornecidos na sessão.

Modo `--matter` também lê:
- `~/.claude/plugins/config/claude-for-legal/contencioso/matters/[slug]/matter.md` → teoria do caso, fatos-chave, fato-pivô (para tag de relevância), datas-chave.
- Padrão de pasta de matéria do CLAUDE.md → onde os documentos da matéria moram.

Modo `--documents` também lê:
- Metadados de plataforma de eDiscovery se houver conector (raro no BR — a maioria do trabalho é manual ou via GED local).
- Manifesto de fls. ou índice de produção se o(a) usuário(a) apontar.
- Exportação de PJe / e-SAJ / e-Proc se disponível.

**Gate de conflitos — não pulável (modo `--matter`).** Antes de montar a cronologia, conferir `~/.claude/plugins/config/claude-for-legal/contencioso/matters/_log.yaml` para o slug da matéria. Se a matéria não está no `_log.yaml`, recusar e redirecionar:

> "Não vejo [slug da matéria] no log. Rode `/contencioso:matter-intake` antes — é onde a conferência de conflitos roda e o workspace é montado. Não monto cronologia em matéria sem intake — a conferência de conflitos é o gate."

Não prosseguir em matéria sem intake. Intake é o que roda conflitos e grava a linha do `_log.yaml` que este skill lê. Modo `--documents` (rodando contra set ad hoc sem slug) é isento do gate, mas suas saídas devem ser tratadas como pesquisa pré-matéria, não como material de trabalho de matéria.

## Workflow

### Passo 0: Gate de sigilo profissional (roda primeiro, toda vez)

Trabalho de cronologia puxa documentos. Documentos frequentemente estão sob sigilo profissional (EOAB art. 7º XIX) — arquivos de matéria in-house quase sempre estão; conjuntos de produção, especialmente entregas parcelares ou de cooperação com co-réus, contêm material sob sigilo ou não revisado. Extrair conteúdo de documento sob sigilo para cronologia que depois é compartilhada *pode* enfraquecer a proteção, dependendo de quem recebe e sob que doutrina. Análise de quebra de sigilo é fato-dependente — peça aprovação do(a) advogado(a) antes de distribuir.

O skill não vai extrair até o(a) usuário(a) escolher uma postura de sigilo:

> Antes de extrair: como as fontes foram triadas quanto a sigilo?
>
> - **A. Todas as fontes liberadas** — você já triou. Extraio sem flag de sigilo. Saída é postura pronta para uso interno; ainda marcada como material de trabalho.
>
> - **B. Misto ou ainda não triado** — extraio e marco cada item com flag `priv`: `ok` (vinda de material claramente fora de sigilo), `flag` (vinda de material potencialmente sob sigilo profissional / contemplação de litígio), ou `review` (origem ambígua). Itens flagados são visualmente marcados na saída e a variante exposição-de-fatos os filtra por default.
>
> - **C. Abortar — triar primeiro** — pausar skill. Triar as fontes. Voltar e rodar de novo.

Registrar a escolha no cabeçalho da cronologia como `privilege_posture: A-cleared | B-mixed | C-aborted`. Se B ou C, registrar a justificativa brevemente.

**Por que gate e não só aviso:** aviso é lido uma vez e esquecido. Gate força a decisão a entrar no registro, o que significa que cada arquivo de cronologia carrega sua própria proveniência — quem lê depois sabe se os itens vieram de material triado quanto a sigilo.

### Passo 1: Identificar fontes documentais

**Modo `--matter`:**

1. **Caminhos fornecidos pelo(a) usuário(a)** — qualquer coisa colada na sessão (caminhos de arquivo, links de drive, exports de e-mail).
2. **Pasta padrão da matéria** — do padrão de armazenamento do CLAUDE.md, expandido para este slug (ex. `G:/Juridico/Matérias/acme-vs-empresa-2026`).
3. **Fontes declaradas** — a tabela `Armazenamento documental` no CLAUDE.md, filtrada às que esta matéria possa tocar (ex. arquivo Gmail para comunicações da contraparte, pasta jurídica no SharePoint).
4. **Pedir** — se as fontes parecerem finas, pergunte: "Posso montar com o que tenho, mas a cronologia ficará incompleta. Mais alguma coisa para eu olhar? E-mails relevantes, contratos, memos internos, notificações extrajudiciais, autos do processo no PJe?"

**Modo `--documents`:**

1. **Set produzido / fls.** — o(a) usuário(a) aponta para diretório de produção ou manifesto; o skill lê por faixa de fls. ou ID + data.
2. **Conector eDiscovery** — raro no BR. Se houver, puxar por custodiante + faixa de data.
3. **Arquivos custodiais** — se o(a) usuário(a) fornecer caixas de e-mail brutas ou exports de drive, ler também.
4. **Exportação PJe / e-SAJ / e-Proc** — se o(a) usuário(a) baixou autos via certidão ou ZIP do tribunal, ler.
5. **Pedir** — se a cobertura parecer fina para custodiante-chave ou faixa de data, perguntar.

### Passo 2: Puxar + ler

Para cada fonte com arquivos legíveis:

- **PDFs, e-mails (.eml), .docx, .txt** — ler diretamente.
- **Arquivos de e-mail (Gmail, Outlook)** — se houver conector MCP autenticado, consultar por faixa de data + contraparte / termos-chave; caso contrário o(a) usuário(a) exporta os threads relevantes para uma pasta.
- **Autos eletrônicos (PJe / e-SAJ / e-Proc / Projudi)** — se houver conector, consultar por número do processo e listar peças / eventos. Caso contrário, o(a) usuário(a) baixa cópia integral.
- **eDiscovery (raro no BR)** — se houver conector, puxar por custodiante + data; caso contrário, o(a) usuário(a) fornece export.

Se o skill não conseguir acessar fonte declarada, nomeá-la explicitamente na seção Lacunas em vez de prosseguir silenciosamente.

**Sem suplementação silenciosa.** Se a cobertura de fontes em uma era da matéria é fina — menos documentos que o esperado para janela alegada, custodiante cuja caixa não é acessível, produção que ainda não chegou — reporte o que achou e pare. NÃO preencha lacunas com busca web, busca em registro público ou conhecimento do modelo sobre a matéria sem perguntar. Diga: "Fontes retornaram [N] eventos para [período / custodiante]. Cobertura parece fina. Opções: (1) me aponte mais fontes (fls., pasta, caixa), (2) tentar outro conector MCP se configurado, (3) buscar na web por eventos públicos nesta janela — resultados serão marcados `[busca web — verificar]` e devem ser conferidos contra fonte primária antes de uso, ou (4) parar aqui e anotar a lacuna. Qual prefere?" Advogado(a) decide se aceita fontes de menor confiança; o skill não decide pelo(a) advogado(a).

**Atribuição de fonte.** Marcar todo item da cronologia com a origem do evento: caminho do arquivo, fls., ID PJe, conector MCP ou fonte declarada (já capturada na coluna Fontes). Para qualquer evento ou data não rastreável a documento recuperado — ex. fato lembrado de treino do modelo, evento público achado via busca web — marcar inline: `[busca web — verificar]`, `[conhecimento do modelo — verificar]`, ou `[fornecido pelo(a) usuário(a)]` quando o(a) usuário(a) afirmou o fato na sessão. Itens marcados `verificar` carregam maior risco de fabricação do que itens documentados e devem ser conferidos primeiro. Nunca remova ou colapse as tags — são o sinal mais rápido para o(a) advogado(a) saber o que verificar antes de levar à inicial / contestação.

**Marcação atinge toda seção que afirma conclusão jurídica, prazo ou data computada — não só linha do tempo.** A linha do tempo vem dos documentos. As seções Lacunas, Eventos-chave, Linhas de amarração com a teoria, e qualquer afirmação sobre prescrição, suspensão, prazo processual, prazo recursal ou determinação de sigilo são análise jurídica que o skill escreve com base em conhecimento do modelo, salvo se etiquetadas. Cada afirmação carrega tag de proveniência: `[calculado a partir de: <regra citada com tag>]`, `[conhecimento do modelo — verificar]`, `[fornecido pelo(a) usuário(a)]`, ou tag de conector de pesquisa se recuperado nesta sessão. Janela prescricional sem tag default é `[conhecimento do modelo — verificar]`. Linha de "evento-chave" que caracteriza relevância jurídica do fato é análise e precisa da tag. A regra é simples: se é afirmação sobre o direito, não afirmação sobre o que o documento diz, carrega a mesma tag de proveniência que os itens da linha do tempo. Quando nenhum conector de pesquisa estiver acessível e o skill está calculando prazos ou citando normas, registrar na linha **Fontes:** da nota ao revisor (ver `## Saídas` do CLAUDE.md) — não emitir banner separado.

### Passo 3: Extrair eventos

Para cada documento, identificar eventos datados:

- **E-mail:** `[data] [remetente] disse a [destinatário] [assunto/conteúdo]`
- **Reunião:** `[data] [participantes] reuniram-se sobre [tema]` (por ata, registro de calendário ou notas)
- **Decisão:** `[data] [responsável pela decisão] decidiu [o quê]` (por documento memorial)
- **Peça / ato processual:** `[data] [parte] protocolou [petição / inicial / contestação / agravo / apelação]` (com referência a fls. ou ID PJe)
- **Notificação extrajudicial:** `[data] [parte] notificou [destinatário] sobre [tema]`
- **Evento externo:** `[data] [coisa aconteceu]` (contrato firmado, produto lançado, regulador atuou — ANVISA, ANATEL, CADE, BCB, CVM, ANPD —, evento cruzou um limite)

Um evento por documento, normalmente. Eventualmente zero (sem data, ou sem evento estabelecido). Às vezes múltiplos (ata de reunião cobrindo várias decisões).

**Flag de sigilo por item (só quando privilege_posture == B-mixed). Regra de três estados — nunca decidir silenciosamente que um teste subjetivo de sigilo não foi atingido:**

- `priv: ok` — fonte é **confiavelmente** fora de sigilo (peças protocoladas, correspondência regulatória pública, documentos públicos, comunicações com a contraparte sem nosso(a) advogado(a)). Usar só quando não há teoria plausível de sigilo.
- `priv: flag` — fonte é confiavelmente ou provavelmente sob sigilo (comunicações com o(a) advogado(a), memos elaborados em contemplação de litígio, minutas internas com análise jurídica, material de cooperação com co-réu). **Default para qualquer coisa incerta** — se o propósito dominante é discutível, se contemplação de litígio é limítrofe, ou se o conteúdo é misto, fica aqui, não em `ok`.
- `priv: review` — fonte ambígua na face, mas o skill não conseguiu fazer a chamada (sem metadados de remetente / destinatário, ilegível etc.).

Quando `priv: flag` ou `priv: review`, adicionar `[SME VERIFICAR: status de sigilo profissional]` inline para que o(a) advogado(a) veja na revisão. Sub-flagar enfraquece sigilo (porta de uma via); super-flagar é corrigido pelo(a) advogado(a) em revisão (porta de duas vias). Preferir o erro recuperável.

### Passo 4: Deduplicar

O mesmo evento aparece em vários documentos: uma reunião está em três agendas e produz uma ata por e-mail — isso é **um evento com quatro fontes**, não quatro eventos. Mesclar. O item mesclado cita todas as fontes.

### Passo 5: Marcar relevância — pela teoria do caso

Ler o fato-pivô e os fatos-chave do `matter.md` (modo `--matter`) ou da seção `## Case theory` da configuração (modo `--documents`). Marcar cada evento:

- 🔴 **Chave** — evento é parte do fato-pivô ou fato-chave a favor ou contra nós
- 🟡 **Relevante** — contexto, prova de padrão, sustenta argumento secundário
- ⚪ **Fundo** — útil para completude, não vai para a peça

**Disciplina:** uma cronologia de 300 itens com 300 tags 🔴 não tem tags. Reservar 🔴 para eventos que moveriam um juiz / desembargador / colegiado de verdade. Em dúvida, 🟡.

**Tag limítrofe:** quando um item está entre 🔴 e 🟡 (ou 🟡 e ⚪), tagear na relevância menor e adicionar `[SME VERIFICAR — relevância limítrofe]` inline. Julgamento do(a) advogado(a) sobrepõe a chamada do skill. Cronologia que sobre-marca com confiança é menos útil que cronologia que mostra sua incerteza.

### Passo 6: Gravar

Saída default é a cronologia de trabalho. Variantes sob pedido.

## Formatos de saída

### Cronologia de trabalho (default)

Local: `~/.claude/plugins/config/claude-for-legal/contencioso/matters/[slug]/chronology.md`. Completa, marcada, anotada. O documento de referência do(a) advogado(a).

```markdown
[CABEÇALHO DE MATERIAL DE TRABALHO — conforme `## Saídas` da configuração do plugin — varia por papel; ver `## Quem está usando`]

> **Herança de sigilo.** Esta cronologia deriva de documentos da matéria potencialmente cobertos por sigilo profissional (EOAB art. 7º XIX) e/ou segredo de justiça (CPC 189). Ela herda o status de proteção das fontes. Distribuição além do círculo do sigilo — a stakeholders fora do engagement, à contraparte / advogado(a) da contraparte, a regulador — pode enfraquecer a proteção tanto da cronologia quanto das fontes subjacentes. Armazene com o material protegido, marque conforme convenções da casa, e tome decisões de distribuição deliberadamente. A escolha de postura de sigilo registrada abaixo é o carimbo de proveniência para qualquer decisão posterior de distribuição.

# Cronologia — [Nome da matéria]

> Tags de relevância (🔴/🟡/⚪) e flags de sigilo (🔒) são leituras de primeira passada e exigem `[SME VERIFICAR]` antes de uso em peça externa (inicial, contestação, exposição de fatos, memo a Conselho, entregável a escritório externo).

**Matéria:** [slug]
**Modo:** matter | documents
**Montada:** [YYYY-MM-DD]
**Fontes:** [N] documentos em [tipos de fonte]
**Itens:** [N] ([N] 🔴 / [N] 🟡 / [N] ⚪)
**Fato-pivô:** [uma frase]
**Postura de sigilo:** A-cleared | B-mixed | C-aborted
**Itens flagados:** [N] 🔒 *(só presente quando postura == B-mixed)*

---

## Linha do tempo

| Data | Evento | Tag | 🔒 | Fontes |
|---|---|---|---|---|
| [YYYY-MM-DD] | [o que aconteceu, uma frase] | 🔴/🟡/⚪ | [vazio / 🔒-flag / 🔒-review] | [caminhos de arquivo / fls. / ID PJe] |

---

## Eventos-chave (só 🔴)

[Destacados, cada um com linha sobre por que importa à teoria.]

### [data] — [título do evento]
- O quê: [uma linha]
- Amarração à teoria: [por que importa]
- Fontes: [lista]

---

## Lacunas

**Faixas de data sem eventos:**
[faixas — onde estão documentos deste período?]

**Esperado e ausente:**
[eventos que esperaríamos ver documentados e não vemos — ex. "aditivos contratuais entre 2024-06 e 2025-03 — não localizados"]

**Fontes ilegíveis / inacessíveis:**
[fontes declaradas no CLAUDE.md mas não acessíveis nesta rodada — ex. "autos PJe-JT — sem conector; cópia integral precisa ser baixada"]

---

## Disciplina de marcadores

- `[VERIFICAR: afirmação factual — data, participantes, conteúdo]` — ainda não confirmado contra o documento
- `[UNCERTAIN: caracterização jurídica — ex. se um evento dispara gatilho regulatório]`
- `[CITE NEEDED: fls. / ID PJe / página da oitiva]`
- `[SME VERIFICAR: status de sigilo | relevância limítrofe]` — julgamento do(a) advogado(a) necessário

---

## Versão
- v[N] montada em [data] a partir de [resumo de fontes]
- v[N-1] montada em [data] (anterior, superada)
```

### Cronologia exposição-de-fatos (CPC 319 III / 336) (sob pedido)

Filtrar a 🔴 e 🟡 relevantes. Apresentar em prosa em ordem cronológica narrativa — esqueleto para seção de fatos da inicial ou contestação. Cada parágrafo é um evento ou cluster ligado, com citação aos autos (fls. / ID PJe).

**Filtro de sigilo default:** quando `privilege_posture == B-mixed`, itens 🔒-flag e 🔒-review ficam **excluídos** por default. A variante exposição-de-fatos serve para eventual uso externo (peças, manifestações, negociação com contraparte) — itens 🔒 não pertencem ali até o(a) advogado(a) confirmar status de sigilo. Se o(a) usuário(a) quiser incluir 🔒 mesmo assim, exigir `--include-flagged` explícito; capturar a autorização no cabeçalho da saída como registro permanente.

### Cronologia por testemunha (sob pedido)

Filtrar a eventos em que testemunha nomeada é remetente, destinatário, participante ou objeto. Alimenta preparação de oitiva (CPC 442) e ajuda a reconstruir o que a testemunha sabia em cada momento.

**Atenção — sistema presidencialista (CPC 459):** no Brasil, perguntas a testemunha são feitas pelas partes ao juiz, que pode reformular ou indeferir. A cronologia por testemunha não substitui roteiro de oitiva, mas alimenta as perguntas que o(a) advogado(a) requererá ao juízo.

## Builds incrementais

Se `chronology.md` existe:

- Ler versão anterior
- Montar nova cronologia das fontes atuais
- Diff: novos eventos (desde último build), itens modificados (novas fontes em eventos existentes), itens removidos (raro; anotar por quê)
- Preservar o número de versão anterior; gravar nova versão com `v[N+1]`
- Saída de resumo do que mudou

## Integração com matter.md / history.md

**Intencionalmente separadas** (in-house, modo `--matter`). `history.md` é o log corrido do(a) advogado(a) — decisões, atualizações, marcos processuais, notas internas de estratégia. `chronology.md` é a linha do tempo de fatos voltada à advocacia. Sobrepõem mas não mesclam:

- Foi emitida ordem de preservação documental → vai em history.md (ação interna). Em geral fora da cronologia (não é fato da disputa).
- A contraparte enviou notificação de inadimplemento em 14 de março → vai em chronology.md (🟡 — estabelece ciência dela). Também em history.md se o intake referenciou.
- Nosso memo de provisão (CPC 25) foi redigido → só em history.md.

Quando o(a) advogado(a) quiser eventos de history na cronologia, pode colar. Default é ficarem separadas.

## O que este skill NÃO faz

- **Resolver contradições.** Quando dois documentos dizem coisas diferentes sobre quando um evento aconteceu, ambos os itens entram com flag. Resolução é do(a) advogado(a); pode exigir oitiva de testemunha (CPC 442), depoimento pessoal (CPC 385) ou exibição (CPC 396).
- **Inventar eventos fora das fontes.** Se não está nos documentos (e não está em matter.md ou na configuração como fato capturado), não entra na cronologia — mas "Lacunas" pode chamar a ausência.
- **Garantir completude.** Cronologia é tão boa quanto as fontes. Se a produção de prova está em curso e só 20% chegou, a cronologia reflete isso. Nomeie a limitação.
- **Decidir status de sigilo pelo(a) advogado(a).** O gate do Passo 0 força a escolha de postura; a flag `priv` por item captura classificação de primeira passada. Determinações reais de sigilo são chamada do(a) advogado(a) por `[SME VERIFICAR]`.
- **Calcular prescrição / decadência sem flag.** Qualquer cálculo de janela de CC 189-206, CDC 27, CLT 11 ou prazo decadencial fica marcado `[conhecimento do modelo — verificar]` salvo se vier de conector configurado com fonte primária.
