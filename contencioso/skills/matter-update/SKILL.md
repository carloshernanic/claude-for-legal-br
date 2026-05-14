---
name: matter-update
description: Anexa evento datado ao histórico de uma matéria e atualiza a linha do log — captura novos desenvolvimentos, mudanças de status, reavaliações de risco, deslocamentos de prazo e mudanças de alçada de acordo. Use quando o(a) usuário(a) quer logar update em uma matéria, anotar desenvolvimento, ou registrar mudança de status no portfólio.
argument-hint: "[slug] [descrição breve do evento]"
---

# /matter-update

1. Seguir o workflow e referência abaixo.
2. Confirmar que slug existe em `~/.claude/plugins/config/claude-for-legal/contencioso/matters/` e em `_log.yaml`.
3. Perguntar por tipo de evento, data (default hoje), resumo, e qualquer atualização de campo do log (mudança de risco, status, próximo prazo, reclassificação de materialidade).
4. Anexar entrada datada a `~/.claude/plugins/config/claude-for-legal/contencioso/matters/[slug]/history.md`.
5. Atualizar `_log.yaml` — setar `last_updated` para hoje, aplicar updates de campo.
6. Confirmar.

---

# Update de Matéria

## Propósito

O portfólio só fica útil se ficar atual. Esta skill torna logar update barato — dois minutos de captura estruturada, sem drift de forma livre.

## Carregar contexto

- `~/.claude/plugins/config/claude-for-legal/contencioso/matters/_log.yaml` — achar a linha
- `~/.claude/plugins/config/claude-for-legal/contencioso/matters/[slug]/history.md` — alvo de append
- `~/.claude/plugins/config/claude-for-legal/contencioso/matters/[slug]/matter.md` — referência (não reescreve)
- `~/.claude/plugins/config/claude-for-legal/contencioso/CLAUDE.md` — calibração de risco (se reavaliando risco)

**Portão de conflitos — não-bypassável.** Antes de logar update, conferir `_log.yaml` para o slug. Se a matéria não estiver em `_log.yaml`, recuse e roteie:

> "Não vejo [matter slug] no registro. Rode `/contencioso:matter-intake` primeiro para a conferência de conflito rodar e o workspace ser criado. Não anexo histórico a matéria não-gerenciada — a conferência de conflito é o portão, e não existe `history.md` para anexar até que a matéria seja intaken."

## Input

Slug (obrigatório). Se não fornecido, pergunte — com lista curta de matérias atualizadas recentemente.

## O update

### 1. Tipo de evento

Ofereça categorias:

- **Procedimental** — peça protocolizada/recebida, despacho/decisão proferida, audiência realizada, prazo designado
- **Provas** — produção feita/recebida (CPC arts. 320 / 336 — provas com a inicial / com a contestação; CPC art. 381 — produção antecipada; CPC art. 385 — depoimento pessoal; CPC art. 396-404 — exibição de documento), perícia, oficiamento
- **Substantivo** — novos fatos, documento-chave veio à tona, decisão de mérito
- **Estratégia** — mudança de postura, proposta de acordo feita/recebida, atualização de alçada
- **Reavaliação de risco** — severidade ou probabilidade mudaram
- **Stakeholder** — pessoa nova envolvida, troca de escritório externo
- **Administrativo** — contrato de prestação assinado, budget ajustado, preservação renovada

Ou livre se nenhum couber.

### 2. Data

Default hoje. Aceitar override (ex. capturando evento da semana passada).

### 3. Resumo

Narrativa de um parágrafo. O que aconteceu, o que significa, qualquer implicação imediata.

### 4. Mudanças de campo do log

Caminhe pelos campos potencialmente afetados:

- `status:` — o estágio se deslocou (ex. contestação → instrução)?
- `stage:` — atualização de subestágio
- `risk:` — reavaliação necessária?
- `materiality:` — alguma mudança (novos fatos podem disparar provisão ou divulgação)?
- `exposure_range:` — revisar se nova informação
- `next_deadline:` — nova data próxima, se houver
- `outside_counsel:` — mudança?
- `internal_owners:` — alguém novo ou removido?
- `legal_hold:` — renovada, expandida, liberada?

Só perguntar pelos campos provavelmente afetados pelo tipo de evento. Updates procedimentais usualmente tocam `stage` e `next_deadline` apenas; uma proposta de acordo pode tocar `materiality`, `exposure_range`, `status`.

### 4pre. Portão de aceite de acordo

Se o update Estratégia é **aceite de acordo** (a empresa está aceitando proposta de acordo, executando acordo, ou autorizando aceite em princípio — não meramente logando proposta feita/recebida): Ler `## Quem está usando` em `~/.claude/plugins/config/claude-for-legal/contencioso/CLAUDE.md`. Se o Papel for Não-advogado:

> Aceitar um acordo tem consequências jurídicas — resolve pretensões, tipicamente exige quitação (CC art. 320), e pode afetar seguro, fiscal e correlatos. Você revisou isto com advogado(a)? Se sim, prossiga. Se não, eis um brief para levar:
>
> [Gerar resumo de 1 página: a matéria, termos propostos de acordo (valor, estrutural, escopo de quitação CC art. 320, confidencialidade, não-difamação), exposição em jogo, status da alçada de acordo (ver `~/.claude/plugins/config/claude-for-legal/contencioso/CLAUDE.md` alçada de acordo), o que pode dar errado, o que perguntar ao(à) advogado(a) antes de aceitar.]
>
> Se precisar achar advogado(a) habilitado(a) na sua jurisdição: o serviço de referência do regulador profissional é o ponto de partida mais rápido (OAB no Brasil; equivalente no exterior).

Não logue o aceite nem flipe materialidade na base do aceite sem yes explícito. Logar propostas ou contraprostas não exige o portão — aceite exige.

### 4a. Gatilho de materialidade — prompt explícito

Certos tipos de evento forçam recheck de materialidade. Quando o tipo de evento estiver nesta lista, **sempre perguntar** — não deixe o(a) usuário(a) seguir sem resposta explícita:

| Tipo de evento | Prompt de gatilho de materialidade |
|---|---|
| Substantivo (novos fatos, documento-chave, decisão de mérito) | "Este evento é substantivo. Empurra `materiality`? Atual: `[atual]`. Opções: `reserved / disclosed / monitored / none`. Mudar?" |
| Estratégia (mudança de postura, proposta de acordo feita ou recebida) | "Atividade de acordo frequentemente dispara reclassificação de materialidade. Atual: `[atual]`. Se a proposta, contraproposta ou aceite move exposição ou desloca de contestado para provável-e-estimável, reclassifique." |
| Reavaliação de risco (severidade ou probabilidade mudaram) | "Risco mudou. Materialidade deveria acompanhar. Atual: `[atual]`. Reclassificar?" |
| Desenvolvimento regulatório / fiscalização | "Ação de regulador (intimação, ofício, notificação de fiscalização — ANPD, CVM, BCB, CADE, ANATEL, ANVISA) usualmente dispara análise de divulgação (DFP/ITR / Fato Relevante — Res. CVM 80; Res. CVM 44). Atual: `[atual]`. Mudar?" |

Respostas aceitáveis incluem `sem mudança` — mas `sem mudança` deve ser explícito, não implicado por silêncio. Capture na entrada de histórico:

```markdown
**Recheck de materialidade:** [sem mudança / mudou de X para Y]
**Racional:** [uma frase]
```

Se materialidade move para `reserved` ou `disclosed`, e a matéria antes não carregava provisão ou divulgação, flage o evento como exigindo notificação a finanças / comitê de auditoria conforme limites em `~/.claude/plugins/config/claude-for-legal/contencioso/CLAUDE.md`.

### 5. Prompt de seed doc (opcional)

Se o update referencia documento (despacho, peça, correspondência), pergunte se há caminho para linkar. Não force.

## Escrita

### Anexar a `~/.claude/plugins/config/claude-for-legal/contencioso/matters/[slug]/history.md`

Mais recente no topo, direto abaixo do `---` que segue o header.

```markdown
## [AAAA-MM-DD] — [Tipo de evento]: [título curto]

[Resumo em parágrafo.]

**Campos alterados:**
- [campo]: [antigo → novo]
- [campo]: [antigo → novo]

**Doc relacionado:** [caminho, se fornecido]
```

Se nenhum campo mudou, omitir o bloco "Campos alterados".

### Atualizar `~/.claude/plugins/config/claude-for-legal/contencioso/matters/_log.yaml`

- Aplicar mudanças de campo.
- Setar `last_updated: [hoje]` (ou data do evento se o(a) usuário(a) sobrescreveu — o log rastreia quando o registro foi tocado pela última vez).

## Confirmar

Mostre ao(à) usuário(a) a entrada e o diff do yaml antes de escrever:

> Eis o que vou anexar e atualizar. Pode commitar?

## O que esta skill NÃO faz

- Editar entradas passadas. Correções são novas entradas que referenciam e corrigem as anteriores.
- Mudar o log silenciosamente. Toda mudança de campo é mostrada antes do write.
- Decidir se novo desenvolvimento justifica provisão/divulgação. Traz a pergunta à tona ("isto pode empurrar materialidade — quer reclassificar?"), o(a) usuário(a) responde.
