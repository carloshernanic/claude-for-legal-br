---
name: entity-compliance
description: >
  Tracker de compliance de entidades — inicializar, reportar prazos próximos,
  atualizar status, rodar auditoria de saúde, exportar CSV. Mantém um
  compliance-tracker.yaml construído a partir da tabela de entidades, calcula
  prazos de protocolo por entidade e jurisdição, e surface o que vence nos
  próximos 30/60/90 dias. Use quando o(a) usuário(a) disser "compliance de
  entidades", "prazos de arquivamento", "balanços a publicar", "tracker de
  entidades", "o que vence", "saúde de entidades" ou "certidões de regularidade".
argument-hint: "[--init | --report [--days N] | --update [--from-report] | --sweep | --audit | --export [--format csv|table]]"
---

# /entity-compliance

> **Adaptação BR não oficial.** Calibrado para obrigações societárias e fiscais brasileiras — Lei 6.404/76, Código Civil, RFB, e-Social, BCB (RDE-IED), Juntas Comerciais. Veja `references/terminology-us-to-br.md`. Toda saída é rascunho para revisão por advogado(a) inscrito(a) na OAB.

1. Carregue `~/.claude/plugins/config/claude-for-legal/societario/CLAUDE.md` → `## Gestão de Entidades` (tabela de entidades, jurisdições, despachante).
2. Roteie pelo flag:
   - Sem flag ou `--init`: Modo 1 — inicializar tracker
   - `--report`: Modo 2 — prazos próximos e itens atrasados
   - `--update`: Modo 3a (manual) ou 3b (--from-report upload)
   - `--sweep`: Modo 3c — varredura de itens unknown/atrasados
   - `--audit`: Modo 4 — auditoria de saúde
   - `--export`: Modo 5 — CSV ou tabela
3. Leia/grave `~/.claude/plugins/config/claude-for-legal/societario/entities/compliance-tracker.yaml`.
4. Após qualquer atualização: mostre resumo das mudanças e próxima ação.

---

## Propósito

Atas anuais, balanços, ECD/ECF, e-Social, DEFIS, RAIS, declaração RDE-IED no BCB, certidões de regularidade —
cada entidade em cada jurisdição tem seu próprio calendário e suas próprias
consequências por perder o prazo. Esta skill mantém um YAML único que sabe
o que vence, quando e para qual entidade.

## Importante: caveat de prazo de referência

> Os prazos refletem requisitos publicamente disponíveis na data de build da
> skill. Exigências de protocolo (Junta Comercial, RFB, e-Social, BCB) podem
> mudar. **Sempre confirme prazos com seu(sua) despachante, contador(a) ou
> diretamente com a Junta Comercial e RFB antes de confiar para fins de
> compliance.** Se você usa despachante terceirizado, o calendário deles é
> autoritativo para suas entidades específicas — use este tracker para organizar
> e surface os dados, não para substituir.

## Premissa de jurisdição

> Este tracker computa prazos contra a Junta Comercial de constituição/registro de cada entidade. Regras, mecânica de prazos e estruturas de taxas variam por UF (cada Junta tem instrução normativa própria, ainda que o DREI uniformize muito via IN 81/2020 e IN 38/2017). Se a pegada real diferir do registrado (filial não declarada em outra UF, entidade dissolvida sem baixa, redomestilização), a saída pode não se aplicar — confirme com o(a) despachante.

## Desambiguação por tipo societário

> O calendário depende **do tipo societário**, não só da jurisdição. Tratar "entidade em São Paulo" como balde único é erro comum. S.A., Ltda. e SLU têm obrigações distintas:
>
> **Tipos brasileiros — principais diferenças:**
>
> - **S.A. fechada:** AGO até 4 meses após o exercício social (art. 132 Lei 6.404). Aprovação de contas, eleição de administradores se vencido o mandato, destinação do lucro. Publicação de balanço em DOU + jornal exigida se receita bruta anual > R$ 78mi (art. 294 Lei 6.404). Arquivamento da ata em Junta Comercial em 30 dias (art. 289). ECD/ECF na RFB.
> - **S.A. aberta:** Tudo da fechada + obrigações CVM (RCVM 80) — Formulário de Referência anual + atualizações em 7 dias úteis em hipóteses específicas (art. 24 RCVM 80); DFP até 3 meses após o exercício; ITR até 45 dias após cada trimestre. Fato relevante imediatamente (Res. CVM 44). Comunicações via IPE (Empresas.NET).
> - **Ltda.:** Deliberação dos sócios sobre contas até 4 meses após o exercício (CC art. 1.078). Quórum simplificado em Ltdas. com até 10 sócios (CC art. 1.072 §3º — dispensa reunião se todos os sócios decidirem por escrito). Sem publicação obrigatória de balanço. Arquivamento de alterações contratuais na Junta — prazo de 30 dias para retroatividade (art. 1.151 CC). ECD/ECF aplicável conforme regime.
> - **SLU (sociedade limitada unipessoal):** Regida pelas regras de Ltda. (CC art. 1.052 §§1º-2º, redação da Lei 13.874/2019). Mesmas obrigações de Ltda. simplificadas.
> - **EIRELI:** Extinta pela Lei 14.195/2021 (Lei do Ambiente de Negócios) — entidades preexistentes foram automaticamente transformadas em SLU.
>
> Uma S.A. fechada NÃO está sujeita ao reporte CVM; uma Ltda. NÃO precisa publicar balanço (salvo grande porte conforme Lei 11.638/2007 e critérios contábeis). Misturar os regimes carrega risco real. Se a tabela registra entidade sem tipo, sinalize `type_unknown` e peça confirmação.

---

## Arquivo do tracker

Vive em `~/.claude/plugins/config/claude-for-legal/societario/entities/compliance-tracker.yaml`. Estrutura:

```yaml
# Tracker de Compliance de Entidades
# Gerado: [data]
# Última atualização: [data]
# Disclaimer: prazos são referência — confirme com despachante, contador(a), Junta Comercial ou RFB

metadata:
  company: "[Razão Social]"
  generated: "[data]"
  last_updated: "[data]"
  last_audit: "[data ou null]"

custom_jurisdictions:   # adicionadas manualmente — UFs ou países não cobertos
  []

entities:
  - name: "[Razão Social]"
    type: "[S.A. fechada / S.A. aberta / Ltda. / SLU / outros]"
    junta_estadual: "[UF — ex.: JUCESP, JUCERJA, JUCEMG]"
    cnpj: "[CNPJ]"
    nire: "[NIRE]"
    formation_date: "[data ou null]"
    status: "[ativa / dormente / em dissolução / baixada]"
    despachante: "[escritório / interno / outro]"
    notes: ""

    jurisdictions:
      - state: "[UF]"
        qualification: "[matriz / filial]"
        qualified_date: "[data ou null]"
        agent_managed: false   # true para entidades estrangeiras geridas por agente local
        local_agent: "[nome ou null]"
        filings:
          - type: "[AGO / DFP / ITR / ECD / ECF / e-Social / DEFIS / Formulário Referência / RDE-IED / outros]"
            due_date: "[YYYY-MM-DD]"
            due_basis: "[data fixa / mês aniversário / N dias após exercício social / outros]"
            last_filed: "[data ou null]"
            last_fee: "[valor ou null]"
            status: "[current / due_soon / overdue / unknown]"
            confirmed_good_standing: "[data ou null]"
            notes: ""
```

Status:
- `current` — protocolado/cumprido, nada vencendo em 90 dias
- `due_soon` — vence em 90 dias
- `overdue` — vencido sem cumprimento
- `unknown` — sem informação; precisa confirmação

---

## Modo 1: Inicializar

Quando não houver tracker, ou com `--rebuild`.

### Passo 1: Carregar tabela de entidades

Leia `CLAUDE.md` → `## Gestão de Entidades` → Tabela. Se populada (de organograma uploadado no cold-start), use direto. Se não, peça que rode o cold-start ou forneça a lista.

### Passo 2: Para cada entidade × jurisdição, confirme as exigências

Para cada entidade, confirme o calendário atual com o(a) despachante ou contador(a). Calendários mudam (mudanças no DREI, na RFB, nas Juntas estaduais; alterações de IN). Não confie em calendário em cache. O tracker grava as datas que você confirma.

Para cada jurisdição onde a entidade está registrada:

1. Pergunte se há relatório atual do(a) despachante/contador(a) — fonte mais autoritativa.
2. Se não, pergunte o que sabe (tipo, base de prazo, último cumprimento, taxa típica). Grave.
3. Para o que o(a) usuário(a) não sabe, marque `unknown` — não popule de cache. Próximo passo é confirmar com despachante/RFB.

**Capture detalhes no tracker em vez de tabela de referência:**

> Não tenho exigências para [Jurisdição] na tabela de referência.
> Vou capturar para que rastreemos daqui em diante.
>
> Para [Entidade] em [Jurisdição]:
> 1. Que tipo de protocolo é exigido? (AGO, balanço, ECD, ECF, e-Social, DEFIS, alteração contratual, atualização Formulário de Referência ou outro?)
> 2. Quando vence? (Data fixa como 30/04, prazo em dias após exercício social, mês aniversário ou outro?)
> 3. Taxa típica? (Aproximada — ou "desconhecida".)
> 4. Quem é seu(sua) despachante ou contador(a) responsável?

Grave em `custom_jurisdictions`:

```yaml
custom_jurisdictions:
  - jurisdiction: "[UF / país]"
    jurisdiction_type: "[UF brasileira / país estrangeiro]"
    filings:
      - type: "[tipo]"
        due_basis: "[fixo: DD-MM / N dias após exercício / outros]"
        typical_fee: "[valor ou desconhecida]"
        notes: "[outras informações — ex.: agente local exigido, idioma local]"
    added_by: "manual"
    added_date: "[data]"
```

**Jurisdições estrangeiras especificamente:**

Filiais e investidas no exterior variam muito por jurisdição. Sempre passe pela definição customizada — confirme tipo, cadência e taxa com agente local. Para entidades estrangeiras de companhia brasileira, lembre da declaração de capitais brasileiros no exterior (CBE) ao BCB se aplicável (Circular BCB 3.624/2013 — patrimônio > USD 1 mi).

Para entidades estrangeiras:
- Há agente local? Note o nome — tracker sinaliza follow-up em vez de calcular prazo.
- Há reportes em nível de grupo (country-by-country reporting CbCR — IN RFB 1.681/2016; UBO/beneficiário final — IN RFB 1.863/2018)?

Marque entidades com agente como `agent_managed: true`. Modo report lista separado com nota para confirmar com agente local.

Para filings anniversary-based: calcule de formation_date. Se null: `unknown` e sinalize.

### Passo 3: Grave o tracker

Gere `compliance-tracker.yaml` com todas as entidades e exigências calculadas. Status inicial:
- `current` se last_filed dentro do período atual
- `due_soon` se vence em 90 dias e sem last_filed
- `overdue` se data passou sem last_filed
- `unknown` se formation_date ausente ou jurisdição não na tabela

Sumário:

```
Tracker inicializado.

Entidades: [N]
Jurisdições: [N]
Filings tracked: [N]

Status:
  ✅ Em dia:        [N]
  ⏰ Vence em breve: [N] (próximos 90 dias)
  🔴 Atrasado:      [N]
  ❓ Desconhecido:   [N] (confirme com despachante)

Rode /societario:entity-compliance --report para ver o que vence.
```

---

## Modo 2: Report

Surface prazos próximos e itens atrasados. Default: 90 dias.

```
/societario:entity-compliance --report [--days 30|60|90|180]
```

Formato:

```
RELATÓRIO DE COMPLIANCE — [data]
[Razão Social]

🔴 ATRASADOS ([N]):
  [Entidade] / [UF] / [Tipo] — venceu em [data]

⏰ VENCE EM [N] DIAS ([N]):
  [Entidade] / [UF] / [Tipo] — vence em [data]  [despachante]
  [Entidade] / [UF] / [Tipo] — vence em [data]

✅ RECENTES ([N] nos últimos 90 dias):
  [Entidade] / [UF] / [Tipo] — protocolado em [data]

❓ STATUS DESCONHECIDO ([N]):
  [Entidade] / [UF] / [Tipo] — sem informação; confirme com despachante

🌐 GERIDOS POR AGENTE ([N]):
  [Entidade] / [País] / [Tipo] — gerido por [agente local]; confirme direto
  [Entidade] / [País] — sem agente registrado; adicione com --update

REGULARIDADE FISCAL/TRABALHISTA:
  Última confirmação: [data]
  Entidades com certidões válidas: [N] de [total]
  (Certidões: CND federal RFB/PGFN; FGTS; trabalhista CNDT; cíveis e tributárias estaduais/municipais)
  Não confirmadas em 6 meses: [lista — note prazo curto de CND federal: 180 dias]
```

Se >10 entidades, ou se o(a) usuário(a) pedir: ofereça dashboard (veja CLAUDE.md `## Outputs → Oferta de dashboard`).

---

## Modo 3: Update

### Gate de ação consequente (protocolar)

**Antes de orientar ou confirmar protocolo:** Leia `## Quem está usando` em `CLAUDE.md`. Se Papel for **Não-advogado(a)**:

> Protocolar AGO, alteração contratual, balanço ou declarações fiscais (ECD, ECF, e-Social, DEFIS) tem consequências jurídicas — é representação formal da entidade, carrega taxas e custos, e descumprimento gera perda de regularidade, multas e até dissolução. Você revisou com advogado(a) (ou despachante/contador(a) qualificado(a)) antes de protocolar? Se sim, prossiga. Se não, eis briefing:
>
> - Entidade, jurisdição, tipo de protocolo e prazo
> - O que o tracker diz sobre último protocolo (data, valor, dados de administradores reportados)
> - Questões em aberto (dados de administradores ainda corretos; despachante mudou; endereço mudou; capital social atualizado)
> - O que pode dar errado (informação de administradores desatualizada — risco do art. 158 Lei 6.404; prazo perdido com multa; cálculo errado de tributos; quebra de regularidade que trava participação em licitação ou financiamento)
> - O que perguntar ao(à) advogado(a)/contador(a) (este protocolo é necessário este ano; há alterações estatutárias/contratuais a refletir; quem assina; qual a base legal — Lei 6.404 art. 132, CC art. 1.078, etc.)
>
> Para localizar advogado(a): consulte a OAB de sua seccional (CNA — Cadastro Nacional dos Advogados).

Não grave nova data `last_filed` após este gate sem "sim" explícito.

### 3a: Atualização manual

```
/societario:entity-compliance --update
```

> "Protocolamos a AGO da [Entidade] na JUCESP em 30/04. Taxa de R$ 450."

Atualiza:
- `last_filed` → data
- `last_fee` → R$ 450
- `status` → `current`

### 3b: Upload de relatório de despachante

```
/societario:entity-compliance --update --from-report
```

Upload de relatório (PDF, CSV ou Excel). Extrai por entidade:
- Tipo de protocolo e prazo
- Data do último protocolo
- Status de regularidade e data de confirmação
- Flags do despachante

Casa por nome (sinalize quase-matches — "Acme Holdings Ltda." vs. "Acme Holdings, Ltda.").

### 3c: Varredura em massa

```
/societario:entity-compliance --sweep
```

Caminha por cada entidade com `unknown` ou `overdue` perguntando uma a uma.

---

## Modo 4: Auditoria de saúde

```
/societario:entity-compliance --audit
```

**Compliance de protocolos:**
- Itens atrasados
- Itens unknown

**Saúde da entidade:**
- Entidades `dormente` — sinalize para revisão: dissolver e baixar? Carregar entidade dormente custa (taxas anuais, contador, custos de Junta) e cria obrigações continuadas. Dissolução: arts. 1.033-1.038 CC (Ltda.); arts. 206-218 Lei 6.404 (S.A.). Baixa na RFB e Junta Comercial requerem certidões de regularidade.
- Dormentes >5 anos — candidatas a dissolução
- Sem formation_date — lacuna de dado

**Lacunas de regularidade:**
- Sem `confirmed_good_standing` — desconhecido se regular; risco se transação exigir certidão em prazo curto.
- `confirmed_good_standing` >180 dias — válidade típica de CND federal vence; refresque.

**Lacunas de registro em outras UFs:**
- Baseado em CLAUDE.md: há UFs na pegada operacional (filiais, empregados) onde entidades não estão registradas como filial? (Lei 6.404 art. 3º; CC art. 969; INs do DREI.) Inscrição estadual ICMS, inscrição municipal ISS quando aplicável.

**Lacunas de acordos intercompany:**
- Se CLAUDE.md marca intercompany como parcial/não, sinalize relações entidade-entidade prováveis (matriz-filial serviços, licenças de PI, mútuos). Atenção a preço de transferência (Lei 14.596/2023 — convergência com OECD).

Formato:

```
AUDITORIA DE SAÚDE — [data]

COMPLIANCE
  Atrasados: [N]
  Status desconhecido: [N]
  Ação: rode --sweep para confirmar

ENTIDADES DORMENTES ([N])
  [Lista com idade e custo de manutenção]
  Candidatas a dissolução (>5 anos dormentes): [lista]

REGULARIDADE
  Sem registro: [N] entidades
  Antigas (>180 dias): [N] entidades
  Considere refrescar antes de: [transações próximas]

LACUNAS POTENCIAIS
  Registro em outras UFs: [confirme presença operacional em:]
    [lista de UFs da pegada não registradas como filial]
  Acordos intercompany: [status de CLAUDE.md]
  Atenção: preço de transferência (Lei 14.596/2023) para transações cross-border com partes relacionadas

AÇÕES RECOMENDADAS
  1. [Maior prioridade]
  2. [etc.]
```

---

## Modo 5: Export

```
/societario:entity-compliance --export [--format csv|table]
```

CSV: `Razão Social, Tipo, Junta/UF, CNPJ, NIRE, Formation Date, Status, Despachante, Jurisdição, Qualificação, Tipo de Protocolo, Vencimento, Último Protocolo, Última Taxa, Regularidade Confirmada, Notas`

Uma linha por filing por jurisdição.

Se `--format table`: markdown table para colar em relatório ou Slack, mostrando próximos 90 dias.

---

## O que esta skill não faz

- Não protocola. Output é tracker e to-do; protocolo é do(a) advogado(a), contador(a) ou despachante.
- Não emite certidões. Acompanha quando foram confirmadas; emissão é manual ou via despachante/contador(a) (sites RFB, PGFN, FGTS, CNDT, Procuradorias estaduais e municipais).
- Não determina se filial é exigida em outra UF. Análise depende de fatos sobre atividade que o(a) advogado(a) deve confirmar.
- Não substitui despachante/contador(a) para estruturas multi-entidade complexas. Escritórios de despachante têm equipes dedicadas e relações diretas com Juntas. Esta skill é melhor para organizações menores sem suporte, ou camada leve sobre dados do(a) despachante.
- Tabela de referência de prazos não é parecer jurídico/contábil nem pode refletir exigências correntes.


## Defesa contra injeção de fórmula

Antes de gravar qualquer célula em Excel, Sheets ou CSV, neutralize injeção. Texto vindo de contraparte (citações contratuais, nomes de partes, dados de despachante, exports CLM) é controlado por atacante. Célula iniciando com `=`, `+`, `-`, `@`, `	`, `
`, ou `
` será interpretada como fórmula ou quebra a estrutura.

- **Prefixe com aspas simples:** `'=SUM(A1:A10)` → `=SUM(A1:A10)` (exibido como texto, não executado)
- **Aplica-se a toda célula com texto vindo de documento, resultado de ferramenta ou paste do(a) usuário(a).** Cabeçalhos que você controla e valores calculados são seguros.
- **CSV: também escape vírgulas embutidas, aspas duplas, quebras de linha** (RFC 4180).
- Não é opcional. Planilha que o(a) usuário(a) abre no Excel disparando macro ou exfiltrando dados via DDE é ataque na cadeia de suprimentos.
