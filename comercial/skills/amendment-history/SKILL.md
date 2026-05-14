---
name: amendment-history
description: >
  Rastreia como um contrato mudou ao longo do contrato base e todos os aditivos —
  seja um resumo de todas as mudanças ao longo do tempo, seja um trace de cláusula
  específica. Use quando o usuário disser "o que mudou neste contrato ao longo do
  tempo", "mostre o histórico de aditivos", "onde está o [cláusula] mais recente",
  "como [cláusula] evoluiu", ou subir múltiplas versões do contrato.
argument-hint: "[arquivo(s) | [ID do CLM (em breve)] | [link de repositório (em breve)]] [--clausula <nome da cláusula>]"
---

# /amendment-history

Carrega um contrato base e todos os aditivos, depois resume o que mudou ao longo do tempo ou rastreia uma cláusula específica até o texto controlador atual.

## Instruções

1. **Obtenha os documentos:** Por upload, [ID do CLM (em breve)] ou [link de repositório (em breve)]. Aceite múltiplos arquivos numa invocação. Se nenhum fornecido, pergunte.

2. **Detecte o modo** analisando o pedido conforme as regras de detecção de modo abaixo. Se um nome de cláusula é claramente declarado, vá direto ao Modo 2. Se nenhuma cláusula é mencionada, rode Modo 1. Pergunte só se for genuinamente ambíguo.

3. **Rode o workflow abaixo.** Siga-o integralmente.

4. **Ofereça follow-ups após a saída:**
   - "Quer que eu rastreie outra cláusula?"
   - "Quer revisão completa do contrato atual (já aditado) contra o playbook?"
     (roteia para vendor-agreement-review)
   - "Quer um resumo para áreas das mudanças-chave?"
     (roteia para stakeholder-summary)

## Exemplos

```
/comercial:amendment-history acme-msa.pdf aditivo-1.pdf aditivo-2.pdf
```

```
/comercial:amendment-history --clausula indenizacao
```

```
/comercial:amendment-history
[colar texto do contrato e aditivos]
```

---

## Contexto de matéria

**Contexto de matéria.** Cheque `## Workspaces de matéria` no CLAUDE.md de nível de prática. Se `Habilitado` for `✗` (padrão para usuários in-house), pule o resto deste parágrafo — skills usam contexto de nível de prática e a máquina de matérias é invisível. Se habilitado e não houver matéria ativa, pergunte: "Para qual matéria é isto? Rode `/comercial:matter-workspace switch <slug>` ou diga `nivel-de-pratica`." Carregue o `matter.md` da matéria ativa para contexto específico e overrides. Escreva saídas na pasta da matéria em `~/.claude/plugins/config/claude-for-legal/comercial/matters/<slug>/`. Nunca leia arquivos de outra matéria salvo se `Contexto cross-matter` estiver `on`.

---

## Propósito

Contratos acumulam aditivos. No terceiro aditivo, ninguém lembra o que o original dizia ou qual versão de uma cláusula controla. Esta skill lê o contrato base e todos os aditivos em ordem cronológica e ou resume o que mudou no contrato inteiro ou rastreia uma cláusula específica através de cada versão para achar o texto controlador atual.

## Detecção de modo

Parseie o pedido do usuário para determinar qual modo rodar. Não pergunte qual modo a menos que o pedido seja genuinamente ambíguo.

**Modo 1 — Resumo** (nenhuma cláusula específica mencionada)
Frases-gatilho: "o que mudou", "histórico de aditivos", "mudanças ao longo do tempo", "resumir aditivos", "como o contrato está agora"

**Modo 2 — Trace de cláusula** (cláusula ou tema específico nomeado)
Frases-gatilho: "onde está a [cláusula]", "[cláusula] mais recente", "como [tema] mudou", "achar a indenização", "o que diz agora sobre [tema]"

Mapeamentos comuns de cláusula:
- "indenização" / "indenidade" → cláusula de indenização (CC arts. 389, 402-405, 944)
- "responsabilidade" / "cap de responsabilidade" → limitação de responsabilidade (CC art. 944; CDC art. 51, I se consumo)
- "rescisão" / "resilição" → prazo e rescisão (CC art. 473 — resilição unilateral)
- "dados" / "privacidade" / "LGPD" / "DPA" → cláusulas de proteção de dados (LGPD)
- "PI" / "propriedade intelectual" → titularidade de PI e licenças (Leis 9.279/96, 9.610/98, 9.609/98)
- "preço" / "fees" / "pagamento" → condições de pagamento
- "renovação automática" / "renovação" → mecânica de renovação (atenção CDC art. 39 V e Súmula 302 STJ se consumo)
- "cláusula penal" → CC arts. 408-416 (limite art. 412)
- "caso fortuito" / "força maior" → CC art. 393
- "onerosidade excessiva" → CC arts. 478-480
- "foro" / "lei aplicável" / "arbitragem" → CPC art. 63; LINDB art. 9º; Lei 9.307/96
- "confidencialidade" / "NDA" → CC + Lei 9.279/96 art. 195 (segredo de empresa)

Se o termo é ambíguo e mapeia para mais de uma cláusula, liste candidatos e pergunte qual:
> "Achei [N] cláusulas relacionadas a [termo] — [liste]. Qual delas?"

Se o pedido geral é ambíguo entre modos, pergunte uma coisa:
> "Resumo de todas as mudanças no contrato, ou trace de cláusula específica — como indenização, responsabilidade ou rescisão?"

---

## Etapa 1: Carregar e ordenar os documentos

Aceite documentos de qualquer destas fontes:

**[Integração CLM em breve] (se conectado):**
Busque por nome da contraparte ou título do contrato. Puxe o contrato base e todos os aditivos. Metadados tipicamente incluem datas de assinatura — use-os para estabelecer ordem cronológica.

**[Integração de repositório de documentos em breve] (se conectado):**
Busque por nome da contraparte ou nome de arquivo. Procure arquivos com padrões como "Aditivo", "Adendo", "Aditivo nº 1", "Primeiro Aditivo", "Termo Aditivo", ou sufixos numerados. Puxe todos e ordene por data ou numeração.

**Upload direto:**
Usuário fornece os arquivos. Na maior parte dos casos a ordenação é auto-explicativa pelo título (ex.: "Aditivo nº 1", "Segundo Aditivo", "Adendo A") ou datas visíveis no nome ou cabeçalho — prossiga sem perguntar.

Pergunte ao usuário para confirmar ordenação só se:
- Nomes de arquivo não indicam sequência (ex.: "acordo-final.pdf", "acordo-v2.pdf", "acordo-markup.pdf")
- Datas estão ausentes tanto dos nomes quanto dos cabeçalhos
- Dois documentos parecem ser a mesma versão de aditivo

Se a ordem foi inferida em vez de confirmada, note a confiança no topo da saída só onde houve incerteza:
> "Ordem inferida dos títulos — um item sobre o qual fiquei menos seguro: [documento específico]. Confirme se isso afeta sua revisão."

**Regras de ordenação:**
- Sempre estabeleça ordem cronológica antes de ler conteúdo.
- Se datas de assinatura estão disponíveis em metadados, use-as.
- Se não, procure datas no cabeçalho ou considerandos ("Este Termo Aditivo, datado de...").
- Aditivos costumam referenciar o contrato que modificam ("este Termo Aditivo ao Contrato Master de Prestação de Serviços datado de [X]") — use estas referências para confirmar a cadeia.

---

## Herança de sigilo

Esta skill lê o contrato base e aditivos — frequentemente sigilosos ou confidenciais por si só, e tipicamente usados em análise sigilosa. A saída herda o status de sigilo profissional e confidencialidade da fonte. Prefixe o cabeçalho de material de trabalho de `~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md` `## Saídas` a toda saída abaixo, distribua apenas dentro do círculo de sigilo profissional (EOAB art. 7º, XIX; CPC art. 388), e guarde onde materiais sob sigilo profissional vivem. Remova o cabeçalho antes de qualquer entrega externa.

## Etapa 2: Ler e indexar

Leia cada documento em ordem cronológica. Para cada, extraia:
- Tipo de documento (contrato base, número do aditivo, adendo, etc.)
- Data de assinatura
- Partes (confirme que combinam entre documentos — flag se uma nova parte foi adicionada ou nome de parte mudou — cessão CC arts. 286-303)
- Lista de cláusulas explicitamente modificadas, adicionadas ou deletadas

Construa um índice de trabalho antes de produzir a saída. Use-o internamente para guiar a saída — não mostre ao usuário.

---

## Modo 1: Resumo de todas as mudanças

### Regra de referência de seção

Toda conclusão deve incluir referência inline à seção/cláusula para que o leitor verifique contra o documento fonte sem buscar:

  "Resilição unilateral (Cláusula 12.3): Adicionada. Cliente pode resilir com aviso prévio escrito de 90 dias sem multa após o prazo inicial. (CC art. 473 — preaviso compatível.)"

Se uma cláusula se estende por múltiplas seções ou o número mudou entre aditivos, cite todas:
  "Indenização (Cláusula 9.1 base; Cláusula 9.1 reescrita no Aditivo 5)"

### Formato de saída

```markdown
# Histórico de Aditivos: [Contraparte] — [Tipo de contrato]

**Contrato base:** [data]
**Aditivos:** [N] ([data do primeiro] → [data do último])
**Última alteração:** [data]
**Regime aplicado:** [B2B-CC / consumo-CDC / adesão B2B-CC art. 423 / misto]

---

## O que mudou — cronologicamente

### Aditivo 1 — [data]
**Propósito:** [uma frase — por que este aditivo existiu, vindo dos considerandos
ou claro pelo contexto. Se não declarado, omita em vez de adivinhar.]

**Mudanças materiais:**
- [Cláusula] (Cl. [X.X]): [o que dizia antes → o que diz agora,
  em português claro]
- [Nova cláusula adicionada] (Cl. [X.X]): [o que faz]
- [Cláusula deletada] (Cl. [X.X]): [o que foi removido e por que importa]

### Aditivo 2 — [data]
[mesma estrutura]

[repita para cada aditivo]

---

## Estado líquido atual

| Cláusula | Posição atual | Cl. | Última alteração |
|---|---|---|---|
| [cláusula] | [resumo em português claro] | Cl. [X.X] | Aditivo N, [data] |
| [cláusula] | [inalterada desde o base] | Cl. [X.X] | Contrato base |

---

## Itens de atenção
[Flag qualquer coisa que pareça inconsistente — ex.: um aditivo modificando
uma cláusula já deletada, texto contraditório entre aditivos, nome de parte
que mudou sem cessão formal (CC arts. 286-303), ou cláusula cujo número de
seção mudou entre documentos. Sinalize também se alguma cláusula é nula sob
o regime aplicado (ex.: limitação de responsabilidade em consumo — CDC art.
51, I; eleição de foro abusiva — CPC art. 63 §3º).
Inclua referências de seção em toda flag.]
```

---

## Modo 2: Trace de cláusula

### Formato de saída

Mostre só o que mudou. Não liste aditivos onde a cláusula ficou intocada — pule-os inteiramente.

```markdown
# Trace de Cláusula: [Nome da cláusula]
## [Contraparte] — [Tipo de contrato]

---

### Original — [Data do contrato base], Cl. [X.X]
> "[citação exata]"

*Português claro:* [uma frase]

---

### Aditivo [N] — [data], Cl. [X.X]

**Era:**
> "[citação exata do texto anterior]"

**Agora:**
> "[citação exata do texto substituto]"

*O que mudou:* [uma frase — efeito prático sobre as partes; cite a base legal aplicável quando relevante (CC, CDC, LGPD, Lei 9.279/96 etc.)]

---

[Apenas aditivos subsequentes que tocaram nesta cláusula aparecem aqui.
Todos os outros são omitidos.]

---

## Texto controlador atual

**Cl. [X.X] — [documento fonte, data]**
> "[citação exata]"

*Português claro:* [uma frase]

---

## Itens de atenção
[Flags, inconsistências, perguntas em aberto — com referências de seção.
Itens comuns para checar: se a cláusula está sujeita ou excepcionada do cap
de responsabilidade; se o número da seção mudou entre aditivos; se o texto
do aditivo conflita com outra cláusula; se a cláusula é nula sob CDC art. 51
(em consumo) ou CC arts. 423-424 (adesão).]
```

Se a cláusula nunca foi alterada após o contrato base:
> "Esta cláusula não foi modificada por nenhum aditivo. Texto original controla. Cl. [X.X], contrato base, [data]."

---

## Encerre com a árvore de decisão de próximos passos

Encerre com a árvore de decisão de próximos passos conforme CLAUDE.md `## Saídas`. Personalize as opções para o que esta skill acabou de produzir — os cinco ramos default (minutar X, escalar, conseguir mais fatos, observar e esperar, outra coisa) são ponto de partida, não trava. A árvore É a saída; o(a) advogado(a) escolhe.

## O que esta skill não faz

- Não determina qual documento controla em caso de conflito entre o contrato
  base e um aditivo — essa é questão de interpretação jurídica. Ela flaga
  conflitos e roteia para o(a) advogado(a).
- Não minuta novos aditivos.
- Não compara contra o playbook em `~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md` — esse é o trabalho da
  vendor-agreement-review. Esta skill é puramente histórica.
- Não infere o que um aditivo significa se o texto é ambíguo — cita exatamente
  e flaga ambiguidade para o(a) advogado(a).
