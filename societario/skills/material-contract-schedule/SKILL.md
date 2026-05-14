---
name: material-contract-schedule
description: >
  Constrói o anexo de Contratos Materiais (disclosure schedule) a partir dos
  achados de diligência, aplicando a definição de "Contrato Material" do
  contrato de compra e venda (SPA / Contrato de Trespasse) e formatando
  conforme o padrão do contrato. Use quando o(a) usuário(a) disser "monta o
  anexo de contratos", "anexo de declarações", "Anexo 3.X", "lista de
  contratos materiais" ou ao redigir anexos de declarações e garantias.
argument-hint: "[caminho do SPA, ou cole a definição de Contrato Material]"
---

# /material-contract-schedule

1. Carregar o SPA → definição de Contrato Material + formato do anexo.
2. Use o workflow abaixo.
3. Aplique a definição aos achados de diligência. Sinalize edge cases.
4. Formate conforme o contrato. Overlay de consentimentos alimenta o checklist de fechamento.

---

## Contexto de matéria

**Contexto de matéria.** Cheque `## Workspaces de matéria` no CLAUDE.md nível-prática. Se `Habilitado` é `✗` (padrão para usuários(as) in-house), pule o resto deste parágrafo — skills usam contexto nível-prática e o aparato de matéria fica invisível. Se habilitado e não houver matéria ativa, pergunte: "Para qual matéria é isto? Rode `/societario:matter-workspace switch <slug>` ou diga `nível-prática`." Carregue o `matter.md` da matéria ativa para contexto específico e overrides. Grave saídas na pasta da matéria em `~/.claude/plugins/config/claude-for-legal/societario/matters/<matter-slug>/`. Nunca leia arquivos de outra matéria a menos que `Contexto cross-matter` esteja `on`.

---

## Propósito

O SPA tem uma declaração: "O Anexo 3.X lista todos os Contratos Materiais." Esta skill monta esse anexo a partir dos achados de diligência — quais contratos são materiais conforme a definição do contrato, no formato exigido pelo contrato.

## Carregar contexto

- Minuta do SPA / Contrato de Compra e Venda de Ações ou Quotas / Contrato de Trespasse — para a definição de "Contrato Material" e o formato do anexo
- `~/.claude/plugins/config/claude-for-legal/societario/CLAUDE.md` → limiares de materialidade (podem diferir da definição do contrato — use a do contrato)
- Achados de diligência de diligence-issue-extraction — dados em nível de contrato

## Workflow

### Passo 1: Obter a definição

Puxe a definição de "Contrato Material" do SPA — a definição do contrato controla. Diferenças de estrutura da operação (compra de ações vs. compra de ativos vs. incorporação/cisão — Lei 6.404 arts. 224-234 + CC arts. 1.116-1.122) podem mudar como uma alínea é interpretada, e overlays regulatórios setoriais (saúde — ANS/ANVISA, defesa, financeiro — BCB/CVM, telecom — ANATEL, energia — ANEEL/ANP, contratos com a Administração Pública — Lei 14.133/2021) podem adicionar exigências de consentimento ou anuência que vivem fora do SPA. Se a operação envolver qualquer desses overlays, pesquise as regras aplicáveis de cessão / sub-rogação contratual (ex.: cessão de contratos administrativos, sub-rogação em concessões, anuência regulatória setorial) e cite a regra que controla.

Categorias comuns de alínea para procurar na definição do SPA — não substituem a leitura do contrato, e a lista que o SPA usa é a que controla:

- Limiar de valor (anual ou agregado — em R$)
- Prazo
- Cláusula de mudança de controle (CoC) ou restrição de cessão
- Exclusividade ou não-concorrência
- Top N contratos por receita (clientes ou fornecedores)
- Contratos de locação imobiliária (Lei 8.245/91 — comercial; matrículas no RGI quando aplicável)
- Licenças de PI (inbound e outbound — INPI; software Lei 9.609/98)
- Acordos com partes relacionadas (com atenção a operações entre partes relacionadas — RCVM 80 para S.A. aberta; preço de transferência Lei 14.596/2023)
- Contratos com a Administração Pública (Lei 14.133/2021; eventual exigência de anuência prévia)
- Contratos fora do curso normal dos negócios

A definição do SPA é o teste. Aplique-a mecanicamente — todo contrato que atende a qualquer alínea da definição do SPA entra no anexo.

### Passo 2: Aplicar a definição aos achados

Para cada contrato revisado em diligência:

| Contrato | Atende alínea(s) | Incluir |
|---|---|---|
| [nome] | [valor anual ≥ R$ X; cláusula de CoC] | Sim |
| [nome] | [nenhuma] | Não |

**Edge cases para sinalizar a juízo humano:**
- Contrato em R$ X-1 (logo abaixo do limiar) mas importante para o negócio
- Contrato atende alínea mas está sendo terminado de qualquer forma
- Acordos verbais ou side letters que podem ou não contar (atenção: CC art. 107 quando exige forma)

### Passo 3: Reunir dados do anexo

Para cada contrato incluído, o anexo tipicamente precisa:

| Campo | Fonte |
|---|---|
| Nome da contraparte | Contrato |
| Título/tipo do contrato | Contrato |
| Data | Contrato |
| Prazo / vencimento | Contrato |
| Valor anual/total (R$) | Contrato ou dados gerenciais |
| Qual alínea de materialidade atende | Análise do Passo 2 |
| Consentimento/anuência requerido para a operação | Achado de diligência |
| Referência no VDR | Inventário de diligência |

Puxe das extrações de diligência existentes. Se um campo estiver faltando, sinalize — não chute.

### Passo 4: Formatar conforme o contrato

Anexos de declarações têm formato — geralmente lista numerada ou tabela, às vezes com sub-partes por tipo de contrato. Espelhe o formato dos outros anexos da minuta do contrato.

```markdown
## Anexo 3.[X] — Contratos Materiais

São Contratos Materiais, na data de assinatura deste Contrato:

### (a) Contratos com Clientes

1. [Título do Contrato], datado de [data], celebrado entre [Sociedade-Alvo] e [Contraparte].
   [Breve descrição, se o formato exigir.]
   [VDR: caminho]

2. [...]

### (b) Contratos com Fornecedores

[...]

### (c) Locações Imobiliárias

[...]

[etc. — sub-partes conforme a estrutura da definição do contrato]
```

### Passo 5: Overlay de rastreamento de consentimentos

Separadamente (não no próprio anexo — isto é interno), rastreie quais contratos anexados exigem consentimento/anuência.

> O overlay de consentimentos e qualquer minuta de trabalho do anexo antes da entrega derivam de materiais privilegiados de diligência e herdam o sigilo e a confidencialidade da fonte — distribuição fora do círculo de sigilo pode quebrar a proteção. O anexo em si, uma vez entregue como exhibit ao SPA assinado, é documento da operação e não é privilegiado; remova quaisquer anotações internas antes da entrega.


| Anexo # | Contraparte | Consentimento exigido | Status | Owner | Vencimento |
|---|---|---|---|---|---|
| 3.X(a)(1) | [nome] | Sim — CoC §12.2 | Solicitado | [nome] | [data] |

Isto alimenta o closing-checklist.

## Cross-check

Antes de entregar:

- Todo contrato que atendeu a uma alínea está no anexo (completude)
- Nenhum contrato está no anexo sem atender alínea (sem over-disclosure — é declaração, não dump de dados)
- Anexo é consistente com as demais declarações (um contrato no Anexo 3.X que cria gravame deveria também estar no anexo de ônus reais e garantias)
- Toda entrada tem cite no VDR para que o(a) advogado(a) da parte contrária possa achar o documento subjacente

## Handoffs

- **De diligence-issue-extraction:** Achados em nível de contrato são o input.
- **Para closing-checklist:** Itens de consentimento entram no checklist.

## O que esta skill não faz

- Não decide a definição de materialidade — está no SPA.
- Não obtém consentimentos — rastreia quais são necessários.
- Não redige a declaração — popula o anexo a que a declaração se refere.
