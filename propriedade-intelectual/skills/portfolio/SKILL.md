---
name: portfolio
description: >
  Acompanha o portfólio de PI — registros, renovações, anuidades, declarações
  de uso e prazos. Use para checar o que está vencendo, adicionar ou
  atualizar ativo, registrar protocolo de manutenção ou auditar o registro
  em busca de lacunas, caducidades e questões de uso. Recebe handoffs de
  prossecução e de clearance.
argument-hint: "[--report [--days N] | --add | --update | --audit]"
---

# /portfolio

> **Nota de adaptação BR.** Esta skill foi originalmente concebida para o
> sistema norte-americano (USPTO §8 / §9 / §71 / §15; maintenance fees a
> 3.5 / 7.5 / 11.5 anos). No Brasil, a manutenção é regida pela **LPI
> (Lei 9.279/1996)** e por normativos do INPI. Mecânicas-chave:
> - **Marca (LPI arts. 133–142):** vigência **decenal** a contar da
>   **concessão**, prorrogável por períodos iguais e sucessivos.
>   Requerimento de prorrogação **no último ano de vigência** ou nos **6
>   meses subsequentes** mediante retribuição adicional (LPI 133 §2º).
>   **Caducidade (LPI 143)** pode ser declarada se uso não comprovado nos
>   5 anos anteriores a requerimento de terceiro. Prova de uso é
>   provocada — não há "§8 declaration of use" automática.
> - **Patente PI (LPI arts. 40 e 84):** vigência **20 anos** do depósito
>   (parágrafo único declarado inconstitucional pelo STF — ADI 5529,
>   2021). **Anuidades anuais** devidas a partir do **3º ano** do
>   depósito. Falta de pagamento → arquivamento e, persistindo, extinção
>   (LPI 78 III, 87).
> - **Modelo de Utilidade (LPI art. 9º + 40):** vigência **15 anos** do
>   depósito. Anuidades a partir do 3º ano.
> - **Desenho Industrial (LPI arts. 108 e 120):** vigência **10 anos** do
>   depósito, prorrogável por **3 períodos sucessivos de 5 anos** (5+5+5).
>   Retribuição quinquenal a partir do 2º quinquênio (LPI 120).
> - **Programa de computador (Lei 9.609/98 art. 2º §2º):** proteção por
>   **50 anos** a contar de 1º de janeiro do ano subsequente à publicação
>   ou, na sua falta, da criação. Registro é facultativo no INPI; não
>   exige manutenção/anuidade.
> - **Obra autoral (Lei 9.610/98 art. 41):** **70 anos** contados de 1º
>   de janeiro do ano subsequente ao falecimento do autor (com regras
>   próprias para co-autoria, anônima/pseudônima e audiovisual). Registro
>   facultativo nos órgãos pertinentes (Biblioteca Nacional, EBA-UFRJ,
>   CBC, ANCINE); não há renovação.
> - **Indicação geográfica (LPI arts. 176–182):** sem vigência (perdura
>   enquanto subsiste a aptidão geográfica).
> - **Protocolo de Madri** (Brasil aderiu — Decreto 10.033/2019; vigor
>   02/10/2019): vigência decenal no WIPO; designações nacionais seguem
>   a regra de cada país.
> - **Domínios .br** (NIC.br): renovação anual; prazo de "limbo" e
>   recuperação conforme regulamento NIC.br.
> - **Reativação / restauração (LPI 87):** patente arquivada por falta de
>   anuidade pode ser restaurada em até **3 meses** com retribuição
>   adicional.

A skill identifica o que está vencendo, adiciona ativos, registra
protocolos e audita o registro.

## Instruções

1. **Siga o fluxo abaixo** e leia
   `~/.claude/plugins/config/claude-for-legal/propriedade-intelectual/portfolio.yaml`.

2. **Default (sem args):** equivalente a `--report` — mostra prazos nos
   próximos 90 dias agrupados por urgência (🔴 caducado/em retomada,
   ⏰ vencendo na janela, 🟡 próximo, 🌐 gerido por mandatário(a),
   ❓ desconhecido).

3. **`--report [--days N]`:** Modo 2. Mude a janela com `--days`
   (30 / 60 / 90 / 180 típicos). Sempre prefixe o cabeçalho de
   work-product do CLAUDE.md → Saídas. Sempre feche com a ressalva de
   verificação.

4. **`--add`:** Modo 3. Percorre novo ativo interativamente — tipo,
   jurisdição, número, datas, titular, sponsor de negócio. Capture regra
   custom se a jurisdição não estiver embutida.

5. **`--update`:** Modo 4. Registra protocolo de manutenção, pagamento
   de anuidade, sincroniza com sistema de gestão de PI ou muda status do
   ativo. Aplica o gate de ação consequencial antes de marcar qualquer
   prazo como `protocolado`.

6. **`--audit`:** Modo 5. Checagem ampla — higiene de prazos, lacunas de
   registro, questões de uso em marcas próximas dos 5 anos para
   declaração provocada de uso, inconsistências de titular, horizonte
   de expiração, marcas fora de watch.

7. **Se o registro está vazio e há sistema de gestão de PI conectado:**
   Ofereça Modo 1 — puxar o portfólio do sistema-fonte e inicializar.

8. **Lembrete de guardrail:** Prazos computados são apenas referência.
   Todo output fecha com linha direcionando verificação contra **busca.
   inpi.gov.br / RPI / e-INPI / WIPO Madrid Monitor / EPO Register**
   antes de protocolar ou pagar. Um prazo na agenda mas errado cria
   falsa confiança; não deixe o usuário tratar isso como fonte da
   verdade salvo se o sistema de gestão estiver em sync.

## Exemplos

```
/propriedade-intelectual:portfolio
```

```
/propriedade-intelectual:portfolio --report --days 180
```

```
/propriedade-intelectual:portfolio --add
```

```
/propriedade-intelectual:portfolio --update
```

```
/propriedade-intelectual:portfolio --audit
```

---

## Funciona melhor conectado

Esta skill rastreia prazos a partir do que você informa. Funciona muito
melhor conectada a:

- **Sistema de gestão de PI (IPMS) via MCP** — Anaqua, Clarivate IPfolio,
  AppColl, Patrix, Alt Legal, FoundationIP, ou ferramenta local
  brasileira. IPMS conectado dá o docket completo, cronograma de
  anuidades e correspondência em um lugar, em vez de o registro ser o
  que o(a) advogado(a) lembra de colar. Pergunte ao seu fornecedor
  IPMS sobre conector MCP, ou veja `CONNECTORS.md` na raiz do repo.
- **INPI via busca.inpi.gov.br** — puxa status, RPI e correspondência
  por processo. Não disponível como MCP oficial; lista de desejos em
  `CONNECTORS.md`.

Sem nenhum, cole o docket ou suba planilha e eu rastreio dali.

## Propósito

Marca não renovada na hora pode caducar. Patente sem anuidade paga é
extinta. Domínio que vence pode ser sniped na hora. Tudo isso é evitável,
e tudo depende de uma coisa: o prazo certo está na agenda de alguém,
amarrado ao processo certo, na jurisdição certa.

Esta skill mantém essa agenda.

## Importante: ressalva de prazo

> As regras de prazo aplicadas refletem requisitos publicamente
> disponíveis na data de construção da skill. Requisitos de oficinas de
> PI, períodos de retomada, estruturas de retribuição e cronogramas de
> manutenção mudam. **Sempre confirme prazos computados contra
> busca.inpi.gov.br / RPI / e-INPI, WIPO Madrid Monitor / Patentscope,
> EUIPO eSearch, UKIPO online records, USPTO TSDR/Patent Center, ou o
> registro nacional pertinente antes de agir.** Se você usa Anaqua, CPA
> Global, Clarivate, Alt Legal ou outro sistema IPMS, o docket dele é
> autoritativo para seus ativos — use este tracker para organizar e
> surface, não para substituir.
>
> Um prazo na agenda mas errado é pior que um prazo não-agendado: cria
> falsa confiança. Outputs "sem prazo iminente" merecem segunda olhada
> antes de você confiar.

## Premissas de jurisdição e tipo

Mecânicas de manutenção variam:

- **Marca BR (INPI):** decenal da concessão, prorrogação no último ano
  ou 6 meses após com retribuição adicional (LPI 133). **Caducidade**
  por desuso de 5 anos exige **requerimento de terceiro interessado**
  (LPI 143) — não há declaração obrigatória de uso a cada quinquênio.
  Mas é boa prática manter prova de uso em arquivo (notas fiscais,
  catálogos, embalagens, peças publicitárias com data).
- **Marca Madri (Brasil designado / brasileiro como base):** decenal no
  WIPO; designações nacionais seguem a regra de cada país. Brasil aderiu
  em 02/10/2019 (Decreto 10.033/2019).
- **Marca EUIPO:** renovação decenal; graça de 6 meses com sobretaxa.
- **Marca USPTO:** §8 entre 5º e 6º aniversário (ou §71 para Madri),
  combinado §8/§9 a cada 10 anos.
- **PI Brasil (LPI 84):** anuidades anuais a partir do 3º ano do
  depósito; pagamento até o último dia do mês correspondente ao depósito.
  Restauração em até 3 meses (LPI 87) com retribuição.
- **MU Brasil:** mesma mecânica de anuidades; vigência 15 anos.
- **DI Brasil (LPI 120):** retribuição quinquenal para prorrogação;
  período de graça 180 dias com sobretaxa.
- **Patentes US:** manutenção a 3.5 / 7.5 / 11.5 anos da concessão.
- **EPO / nacionais:** anuidades anuais; regras nacionais variam.
- **Direito autoral BR:** sem manutenção; vigência 70 anos pós-óbito do
  autor (Lei 9.610 art. 41).
- **Software BR:** sem manutenção; 50 anos.
- **Domínios .br:** renovação anual NIC.br.

Se o portfólio incluir ativos em jurisdições fora desta lista, capture
a mecânica no bloco `custom_rules` do registro; o report vai surface
como `agent_managed` — confirme status com o(a) correspondente local em
vez de computar data que a skill não conhece.

---

## O registro

Mora em `~/.claude/plugins/config/claude-for-legal/propriedade-intelectual/portfolio.yaml`.
Estrutura:

```yaml
# Registro de Portfólio de PI
# Gerado: [data]
# Última atualização: [data]
# Ressalva: prazos computados são apenas referência — confirme com
# busca.inpi.gov.br / RPI / WIPO / registro pertinente ou com o sistema
# de gestão de PI antes de agir.

metadata:
  empresa: "[Razão social]"
  gerado: "[data]"
  ultima_atualizacao: "[data]"
  ultima_auditoria: "[data ou null]"
  sistema_fonte: "[Anaqua / CPA Global / planilha / manual / nenhum]"

custom_rules:   # jurisdições não-embutidas capturadas manualmente
  []

assets:
  - id: "MARCA-BR-001"
    tipo: "marca"                              # marca / patente / mu / di / autoral / software / dominio / ig
    jurisdicao: "BR"
    sinal_ou_titulo: "[Marca ou título]"
    titular: "[Titular registrado — razão social]"
    status: "registrada"                       # depositada / em_exame / publicada / registrada / extinta / arquivada / caducada
    numero_processo: "[número INPI]"
    numero_registro: "[número da concessão ou null]"
    classes_ncl: ["9", "42"]                   # Nice para marca; CIP/CPC para patente; null para outros
    data_deposito: "[YYYY-MM-DD ou null]"
    data_concessao: "[YYYY-MM-DD ou null]"
    data_prioridade: "[YYYY-MM-DD ou null]"   # se reivindica prioridade unionista
    proximos_prazos:                           # computados; refrescados em --report e --audit
      - tipo: "Prorrogação decenal (LPI 133)"
        data_vencimento: "[YYYY-MM-DD]"
        retomada_ate: "[YYYY-MM-DD ou null]"   # 6 meses após com retribuição adicional
        base: "10 anos da concessão"
        acao: "Protocolar pedido de prorrogação"
        status: "proximo"                      # proximo / vence_em_breve / em_atraso / em_retomada / protocolado
    uso_comprovavel: true                      # marca — relevante para defesa contra caducidade (LPI 143)
    gerido_por_mandatario: false               # true para mandatário(a) / correspondente
    mandatario_local: null
    docket_id: "[ID IPMS ou null]"
    escritorio_externo: "[escritório ou null]"
    sponsor_negocio: "[email ou time]"
    notas: ""

  - id: "PAT-BR-001"
    tipo: "patente"                            # patente (PI) / mu / di
    jurisdicao: "BR"
    sinal_ou_titulo: "[Título da invenção]"
    titular: "[Titular]"
    status: "concedida"
    numero_processo: "[número INPI]"
    numero_carta_patente: "[número CP]"
    data_deposito: "[YYYY-MM-DD]"
    data_concessao: "[YYYY-MM-DD]"
    data_prioridade: "[YYYY-MM-DD ou null]"
    data_expiracao: "[YYYY-MM-DD]"             # 20 anos do depósito (PI); 15 (MU); 10 (DI) + prorrogações
    proximos_prazos:
      - tipo: "Anuidade 5ª (LPI 84)"
        data_vencimento: "[YYYY-MM-DD]"
        retomada_ate: "[YYYY-MM-DD]"           # 6 meses ordinária + 3 meses restauração (LPI 87)
        base: "anuidade anual, mês do depósito"
        acao: "Pagar anuidade (porte/MEI se aplicável)"
        status: "proximo"
    qtd_reivindicacoes: 20
    porte: "normal"                            # normal / pequeno (MEI/EPP/ME — descontos)
    docket_id: null
    escritorio_externo: null
    sponsor_negocio: null
    notas: ""
```

Valores de `status` em `proximos_prazos`:
- `proximo` — mais de 90 dias
- `vence_em_breve` — vence em ≤ 90 dias, ainda não protocolado
- `em_atraso` — passou do vencimento ordinário, dentro da janela de
  retomada (se houver)
- `em_retomada` — dentro do período com sobretaxa
- `extinto` / `caducado` — passou a retomada sem ato; ativo efetivamente
  perdido salvo se restaurável
- `protocolado` — ato concluído neste ciclo

---

## Modo 1: Inicializar

Rode quando não houver registro, ou com `--rebuild`.

### Passo 1: Determine a fonte

Leia `~/.claude/plugins/config/claude-for-legal/propriedade-intelectual/CLAUDE.md`:
- **Sistema de gestão de PI conectado** (Anaqua, CPA Global etc.): puxe
  o portfólio via integração. O sistema é a fonte autoritativa; este
  registro espelha e não adiciona prazos que o sistema não tenha.
- **Sem IPMS, mas planilha / export disponível:** peça ao usuário para
  compartilhar. Importe o que houver; sinalize ativo sem data de
  registro/concessão como `desconhecido` para cômputo de prazo.
- **Nada à mão:** percorra interativamente — tipo, jurisdição, número,
  datas-chave, titular.

### Passo 2: Para cada ativo, compute prazos

Aplique as regras no topo do arquivo. Popule `proximos_prazos` com os
dois ou três itens mais próximos — prazos distantes (prorrogação decenal
daqui a anos) são computados sob demanda no report, não armazenados
especulativamente.

**Para ativos que a skill não consegue agendar com confiança:**
- Regras de jurisdição desconhecidas → adicione stub em `custom_rules` e
  marque o ativo `gerido_por_mandatario: true` com TODO de confirmar
  com correspondente.
- Datas necessárias faltando (sem data de concessão para patente, sem
  data de registro para marca) → `proximos_prazos` vazio com nota em
  `notas`, e liste o ativo como `desconhecido` no resumo da
  inicialização.

### Passo 3: Escreva o registro

Gere `portfolio.yaml` no caminho de config. Mostre resumo:

```
Registro de portfólio inicializado.

Ativos: [N]
  Marcas:    [N]   ([N registradas] / [N em exame])
  Patentes:  [N]   ([N concedidas] / [N em exame])
  MU:        [N]
  DI:        [N]
  Autoral:   [N]
  Software:  [N]
  Domínios:  [N]
  IG:        [N]

Prazos computados: [N]
Gerido por mandatário(a) / jurisdição a confirmar: [N] — confirme com correspondentes
Desconhecido (faltam datas-chave): [N] — preencha antes de confiar nos reports

Rode /propriedade-intelectual:portfolio --report para ver o que está vencendo.
```

---

## Modo 2: Report

```
/propriedade-intelectual:portfolio --report [--days 30|60|90|180]
```

Janela default: 90 dias. Refresque prazos computados para cada ativo
antes de produzir o report — não dependa apenas das datas armazenadas.

Output (prefixe o cabeçalho de work-product do
`~/.claude/plugins/config/claude-for-legal/propriedade-intelectual/CLAUDE.md` → Saídas):

```
REPORT DE PRAZOS — PORTFÓLIO PI — [data]
[Empresa] — janela: próximos [N] dias

🔴 CADUCADO / EM RETOMADA ([N])
  [ID] / [Jurisdição] / [Tipo] / [Marca ou título]
    [Ação] — vencimento original [data], retomada até [data]
    Status: [em_retomada / extinto / caducado]

⏰ VENCE EM ATÉ [N] DIAS ([N])
  [ID] / [Jurisdição] / [Tipo] / [Marca ou título]
    [Ação] — vence [data]
    Base: [ex.: "10 anos da concessão (LPI 133)"]
    [Mandatário(a): escritório / docket: id — se houver]

🟡 PRÓXIMO (janela > 30 dias e ≤ [N] dias)
  [lista]

🌐 GERIDO POR MANDATÁRIO(A) ([N])
  [ID] / [Jurisdição] — gerido por [mandatário(a) local]; confirme direto
  [ID] / [Jurisdição] — sem mandatário(a) registrado; adicione com --update

❓ DESCONHECIDO ([N])
  [ID] — falta [campo]; não é possível computar prazo
  Confirme com [sistema IPMS / busca.inpi.gov.br / RPI / registro pertinente] antes de confiar.

RESUMO
  Total de ativos: [N]
  Prazos na janela: [N]
  Última auditoria: [data]
```

Feche o report com a ressalva: *"Computado a partir do registro de
portfólio. Verifique cada prazo contra busca.inpi.gov.br / RPI / WIPO
/ registro pertinente antes de protocolar ou pagar."*

Se o report listar mais de ~10 ativos, ou sempre que o usuário pedir:
ofereça o dashboard (vide CLAUDE.md `## Saídas → Oferta de dashboard
para outputs com muitos dados`). Molde a oferta para esta saída —
contagens por status (vigente / em retomada / caducado / em exame),
linha do tempo de prazos, tabela ordenável com jurisdição, tipo e data
da próxima ação.

---

## Modo 3: Adicionar

```
/propriedade-intelectual:portfolio --add
```

Adição interativa de ativo único. Pergunte:
1. Tipo (marca / patente PI / MU / DI / autoral / software / domínio / IG)
2. Jurisdição
3. Marca ou título / nome da invenção
4. Titular (titular registrado — importa para prorrogação, cessões,
   averbação)
5. Datas-chave (por tipo: depósito, concessão, prioridade, expiração)
6. Número(s) (processo INPI / registro / CP)
7. Classes NCL / quantidade de reivindicações
8. Fonte — está sendo rastreado no IPMS sob docket ID?
9. Escritório externo / mandatário(a) local, se houver
10. Sponsor de negócio (a quem isto importa — linha de produto, gerente
    de marca)

Após a captura:
- Compute próximos prazos pelas regras no topo.
- Se a jurisdição não estiver embutida, percorra captura `custom_rules`
  (abaixo).
- Acrescente em `assets:` no `portfolio.yaml`.

### Captura de regras custom

Quando a jurisdição não está na lista embutida:

> Não tenho regras de manutenção para [Jurisdição] / [Tipo] embutidas.
> Deixe-me capturá-las para que possamos rastrear adiante.
>
> 1. Que eventos de manutenção se aplicam? (Prorrogação a cada N anos?
>    Anuidades anuais? Declarações de uso? Outro?)
> 2. O que dispara a contagem — data de depósito, registro, concessão,
>    entrada em fase nacional, aniversário de outro evento?
> 3. Há período de retomada? A que custo?
> 4. Há mandatário(a)/correspondente local gerindo?

Armazene em `custom_rules:` e aplique a futuros ativos.

---

## Modo 4: Atualizar

```
/propriedade-intelectual:portfolio --update
```

### Gate de ação consequencial

**Antes de registrar protocolo de manutenção ou pagamento como
realizado:** Leia `## Quem está usando` em
`~/.claude/plugins/config/claude-for-legal/propriedade-intelectual/CLAUDE.md`. Se o
Papel for **Não-advogado(a)**:

> Registrar uma prorrogação de marca, anuidade de patente ou
> retribuição quinquenal de DI como "protocolada" tem consequências. Se
> o registro está errado — prazo perdido, porte errado, prova de uso
> insuficiente — o prazo não anda, e o ativo ainda pode ser perdido.
> Você confirmou isto com o(a) advogado(a)/mandatário(a) que
> efetivamente protocolou (ou no busca.inpi.gov.br / RPI / WIPO Madrid
> Monitor / registro pertinente)? Se sim, prossiga. Se não:
>
> - Não registre como protocolado ainda.
> - Eis o que levar ao(à) advogado(a): ID do ativo, jurisdição, tipo de
>   prazo, o que o IPMS mostra, o que você acredita ter sido
>   protocolado e quando, e a fonte dessa crença.
>
> Para encontrar profissional habilitado: **OAB-seccional** (Comissão
> de PI), **ABPI**, mandatários(as) registrados(as) no INPI. Para
> jurisdições estrangeiras, registro da oficina correspondente.

Não defina `status` de prazo como `protocolado` passado esse gate sem
"sim" explícito. Refresh de status, geração de report e surface de
prazos próximos não exigem o gate.

### Sub-modos

**Atualização manual:** "Protocolamos a prorrogação para MARCA-BR-001
em 4 de março, taxa GRU paga." Atualize o prazo correspondente:
`status: protocolado`, `data_protocolo`, e compute o próximo prazo no
ciclo (próxima prorrogação decenal).

**Sync do IPMS:** Se Anaqua / CPA Global / similar estiver conectado,
puxe o docket mais recente e reconcilie. Sinalize divergências entre o
registro e o sistema-fonte — sistema-fonte ganha; atualize o registro
para casar e surface qualquer item que o registro tinha e o sistema
não.

**Mudança de status:** "Marcar MARCA-BR-004 como caducada." Atualize
`status`, limpe `proximos_prazos`, anote a data.

---

## Modo 5: Auditar

```
/propriedade-intelectual:portfolio --audit
```

Checagem ampla além dos prazos do mês:

**Higiene de prazos**
- Algum prazo em `em_retomada` agora? (Em andamento com sobretaxa.)
- Algum ativo `caducado`/`extinto` não marcado como tal? Restaurar
  (se cabível) ou atualizar status.
- Algum ativo sem `proximos_prazos` computados? Falta dado ou
  jurisdição que a skill não conhece.

**Lacunas de registro**
- Marcas depositadas há mais de 18 meses ainda `em_exame`? Sinalize
  para checagem de status na RPI — pode precisar de resposta a
  exigência.
- Patentes depositadas há mais de 6 anos ainda `em_exame`? Sinalize
  para checagem de prossecução (atenção ao atraso histórico do INPI
  pós-ADI 5529).

**Uso de marca (caducidade — LPI 143)**
- Marca completando 5 anos de registro sem evidência de uso documentada?
  Caducidade pode ser provocada por terceiro — recomende auditoria de
  uso (notas fiscais, embalagens, publicidade datada). **Não** é
  declaração obrigatória ao INPI; é preparação defensiva.

**Higiene de titularidade**
- Algum ativo onde o `titular` não é entidade ativa no registro
  societário (se disponível)? Sinalize — pode precisar de averbação
  de cessão (LPI 134–137).
- Inconsistências no nome do titular entre ativos (mesma entidade,
  string diferente)? Sinalize para limpeza.

**Horizonte de expiração**
- Patentes/MU/DI expirando em 24 meses? Mesmo sem prazo de
  manutenção, o negócio pode querer saber — planejamento de produto,
  estratégia de continuação, janela de licenciamento.

**Ativos sem watch**
- Marcas registradas fora da watch list em CLAUDE.md → Proteção de
  marca? Sinalize como lacuna para o(a) advogado(a) decidir.

Formato de output:

```
AUDITORIA DE PORTFÓLIO PI — [data]

HIGIENE DE PRAZOS
  Em retomada: [N] — agir agora evita perda
  Caducados/extintos (sem marcação): [N] — confirmar status
  Sem cômputo de próximo prazo: [N] — preencher dado ou marcar gerido por mandatário(a)

LACUNAS DE REGISTRO
  Marcas em exame > 18 meses: [lista]
  Patentes em exame > 6 anos: [lista]

USO DE MARCA (CADUCIDADE — LPI 143)
  Marcas ≥ 5 anos sem evidência documentada de uso: [lista]

TITULARIDADE
  Ativos com titular não-reconhecido: [N]
  Inconsistências de nome: [lista]

HORIZONTE DE EXPIRAÇÃO (24 meses)
  Patentes/MU/DI: [lista]

WATCH DE MARCA
  Marcas registradas fora do watch: [lista]

AÇÕES RECOMENDADAS
  1. [maior prioridade]
  2. [etc.]
```

---

## Integração: agente ip-renewal-watcher

O agente `ip-renewal-watcher` deste plugin roda esta skill em horário
agendado (semanal por default, alinhado à publicação da RPI) e posta o
report do Modo 2 no canal nomeado em CLAUDE.md → Proteção de marca /
Alertas de renovação. Se aparecerem itens 🔴 (em retomada / extintos),
o agente posta imediatamente independente do agendamento.

## Handoffs

- Recebe: novos registros de ativos das skills de prossecução (quando
  um pedido é depositado ou marca é concedida), das skills de
  clearance (quando marca é adotada e depósito é enfileirado), e de
  averbações de cessão.
- Envia: triggers de "protocolar prorrogação agora" ao(à)
  advogado(a) — esta skill não protocola nada; diz o prazo e o que
  levar.

## O que esta skill NÃO faz

- Não protocola nada. Toda ação que surface é para advogado(a)/
  mandatário(a) executar.
- Não verifica prazos contra busca.inpi.gov.br, WIPO ou qualquer outro
  registro. Computa a partir das datas que você fornece. O registro é
  cópia de trabalho; o INPI/oficina é a fonte da verdade.
- Não decide se renovar. Renovação é decisão de negócio — a marca
  ainda está em uso? A patente ainda vale? O domínio ainda importa?
  Esta skill surface o prazo e o custo; negócio e advogado(a) decidem.
- Não substitui IPMS em portfólios de centenas de ativos. Anaqua, CPA
  Global, Clarivate, Alt Legal e similares têm feed direto de
  registros, automação de prazos e serviços de pagamento de anuidade.
  Esta skill é melhor para portfólios menores, ou como camada leve
  que surface o que o sistema-fonte mostra.
- Não lê registros de oficina para verificar status. Uma prorrogação
  marcada como "protocolada" aqui significa que alguém disse — não
  que o INPI aceitou. Confirme aceitação na RPI ou no IPMS.
