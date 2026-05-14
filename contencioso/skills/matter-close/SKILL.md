---
name: matter-close
description: Encerra uma matéria — captura desfecho, exposição final e lições, depois arquiva fora do portfólio ativo sem apagar o registro. Use quando o(a) usuário(a) quer encerrar matéria, diz "[matéria] acabou", ou precisa registrar desfecho por acordo, extinção, sentença, desistência ou conexão.
argument-hint: "[slug]"
---

# /matter-close

1. Seguir o workflow e referência abaixo.
2. Confirmar slug e status atual.
3. Capturar desfecho: tipo de resolução (acordo, extinção CPC art. 485, sentença a favor/contra, desistência, conexão CPC art. 55), data, custo/exposição final, lições.
4. Atualizar `_log.yaml`: `status: closed`, adicionar `closed: AAAA-MM-DD` e campos `outcome:`.
5. Anexar entrada final a `~/.claude/plugins/config/claude-for-legal/contencioso/matters/[slug]/history.md`.
6. Matéria fica em `_log.yaml` e em `~/.claude/plugins/config/claude-for-legal/contencioso/matters/[slug]/` — não apagada. `/portfolio-status` filtra dos rollups ativos.

---

# Encerramento de Matéria

## Propósito

Matérias acabam. O desfecho é o data point mais valioso que o portfólio gera — calibra o framework de risco para matérias futuras. Encerrar matéria captura desfecho estruturalmente para o registro ser útil, não só arquivado.

## Carregar contexto

- `~/.claude/plugins/config/claude-for-legal/contencioso/matters/_log.yaml` — achar a linha
- `~/.claude/plugins/config/claude-for-legal/contencioso/matters/[slug]/matter.md` — referência (contexto de intake)
- `~/.claude/plugins/config/claude-for-legal/contencioso/matters/[slug]/history.md` — alvo de append

**Portão de conflitos — não-bypassável.** Antes de encerrar, conferir `_log.yaml` para o slug. Se não estiver em `_log.yaml`, recuse e roteie:

> "Não vejo [matter slug] no registro. Nada a encerrar — ou o slug está errado ou a matéria nunca passou por `/contencioso:matter-intake`. Confira o slug; se genuinamente nunca foi intaken, não há linha para atualizar nem estrutura de arquivo para encerrar."

## Input

Slug (obrigatório).

## O encerramento

### 1. Tipo de resolução

- `acordo` — com contraparte, valor, termos estruturais (com cláusula de quitação CC art. 320, confidencialidade, não-difamação, sucumbência CPC art. 90 §3º se houver)
- `extinto-com-merito` — CPC art. 487 (sentença de mérito, qual mecanismo — improcedência, transação homologada, prescrição/decadência)
- `extinto-sem-merito` — CPC art. 485 (qual inciso — perempção, abandono, ausência de pressuposto)
- `sentenca-a-favor` — em que estágio, exposição em recurso (apelação CPC art. 1009-1014; recurso especial CPC art. 1029 / Tema STJ; recurso extraordinário com repercussão geral CPC art. 1035)
- `sentenca-contra` — em que estágio, status de recurso, exposição cristalizada
- `desistencia` — pela contraparte ou por nós, circunstâncias (CPC art. 485, VIII)
- `conexao` — apensado a outra matéria (forneça slug do principal — CPC art. 55)
- `outro` — com explicação

### 2. Data de resolução

A data em que a matéria efetivamente terminou (acordo assinado/homologado, decisão proferida, desistência protocolizada).

### 3. Exposição final

- Custo real à empresa (valor de acordo + honorários + sucumbência CPC art. 85 + custo estrutural inibitório)
- vs. faixa de exposição inicial no intake (acertamos?)
- Acurácia da provisão (se reservada): provisionado vs. real (CPC 25)

### 4. Lições

Duas ou três frases. O que acertamos? O que erramos? Algo que o intake deveria ter flagado antes?

Esta é a parte que contencioso futuro vai reler. Seja honesto. "Errei a probabilidade — banca autora foi mais agressiva que esperado" vale mais que "resolvido favoravelmente".

### 5. Prompt de seed doc

Termo de acordo, sentença, despacho de extinção — caminho se disponível. Não obrigatório.

## Escrita

**Antes de encerrar a matéria (o ato consequente — a matéria é arquivada e o tracking ativo termina):** Ler `## Quem está usando` em `~/.claude/plugins/config/claude-for-legal/contencioso/CLAUDE.md`. Se o Papel for Não-advogado:

> Encerrar matéria tem consequências jurídicas — termina o tracking ativo, pode afetar preservação documental associada (rode `/legal-hold --release` separadamente se apropriado), e estabelece o registro final do qual a empresa depende. Você revisou isto com advogado(a)? Se sim, prossiga. Se não, eis um brief para levar:
>
> [Gerar resumo de 1 página: a matéria, tipo de resolução e termos, exposição final vs. inicial, acurácia da provisão, matérias relacionadas ou recursos ainda vivos, o que pode dar errado em encerramento prematuro, o que perguntar ao(à) advogado(a).]
>
> Se precisar achar advogado(a) habilitado(a) na sua jurisdição: o serviço de referência da OAB é o ponto de partida mais rápido.

Não escreva os campos de close nem anexe a entrada de close sem yes explícito.

### Atualizar `~/.claude/plugins/config/claude-for-legal/contencioso/matters/_log.yaml`

```yaml
status: closed
closed: [AAAA-MM-DD]
outcome: [tipo-resolucao]
final_cost: [valor em R$]
last_updated: [hoje]   # close é o último toque; registre
```

Manter todos os campos existentes. Não apague a linha.

### Anexar entrada final a `~/.claude/plugins/config/claude-for-legal/contencioso/matters/[slug]/history.md`

```markdown
## [AAAA-MM-DD] — Matéria encerrada: [tipo-resolucao]

**Resolução:** [narrativa — o que aconteceu, em que termos]
**Custo final:** [valor + termos estruturais se houver]
**vs. exposição inicial:** [comparar à faixa do intake em matter.md]
**Acurácia da provisão:** [se aplicável]

**Lições:**
[2-3 frases — retrospectiva honesta]

**Doc relacionado:** [termo de acordo / sentença / etc., se fornecido]
```

### Tocar `~/.claude/plugins/config/claude-for-legal/contencioso/matters/[slug]/matter.md`

Adicionar bloco de encerramento ao final (não modifique seções anteriores — são o intake histórico):

```markdown
---

## Encerrada em [AAAA-MM-DD]

[Resumo da resolução em um parágrafo. Pointer para a entrada final do histórico para detalhe.]
```

## Confirmar

Mostre ao(à) usuário(a) a entrada de close completa e as mudanças no yaml antes de escrever.

## O que esta skill NÃO faz

- Apagar matérias. Encerradas ficam em `_log.yaml` e em disco — são o training set para o julgamento do portfólio.
- Reabrir. Se matéria encerrada volta (recurso, contencioso relacionado), abra nova matéria que referencie a encerrada em `matter.md`.
- Resumir lições que o(a) usuário(a) não disse. Se pular a seção, deixe vazia em vez de inventar.
