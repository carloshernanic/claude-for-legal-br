---
name: expansion-update
description: >
  Atualiza o status de um projeto de expansão internacional em andamento —
  recalcula o que está agora desbloqueado, sinaliza itens em atraso e levanta
  as próximas prioridades. Use quando houve avanços desde a última sessão e o
  tracker de expansão precisa refletir o estado atual.
argument-hint: "[nome do país]"
---

# /expansion-update

Retorna a um tracker de expansão aberto e atualiza o status de itens com base
no que aconteceu desde a última sessão. Recalcula o que está agora desbloqueado,
sinaliza itens em atraso e levanta as próximas prioridades.

## Instruções

1. Carregue `~/.claude/plugins/config/claude-for-legal/trabalhista/CLAUDE.md`.

2. Identifique o arquivo do tracker: `~/.claude/plugins/config/claude-for-legal/trabalhista/expansion-[slug].yaml`. Se não
   existir, responda: "Não encontrei tracker de expansão para [país]. Rode
   `/trabalhista:expansion-kickoff [país]` para iniciar um."

3. Leia o tracker. Mostre o estado atual:

```
Expansão [País] — última atualização [data]
Em aberto: [N] | Em andamento: [N] | Concluído: [N] | Bloqueado: [N]

Próximas prioridades (itens em aberto com vencimento mais próximo ou maior dependência):
  [item] — responsável: [nome]
  [item] — responsável: [nome]
  [item] — responsável: [nome]
```

4. Peça atualizações em um único prompt — não pergunte item a item:

   > Quais itens avançaram desde a última conversa? Diga o que mudou
   > (ex.: "decisão de EOR feita — fechamos com Deel", "advocacia local
   > contratada — call agendado para quinta", "análise de EP ainda em aberto,
   > aguardando o tributário"). Você também pode adicionar novos itens ou
   > mudar prazos.

5. Aplique atualizações no tracker. Para cada item marcado `done`,
   verifique se desbloqueia outros e sinalize-os como agora acionáveis.

6. Se algum item tem prazo vencido e ainda está `open` ou
   `in-progress`, sinalize:

```
⚠️ Em atraso: [item] — vencimento [data], responsável: [nome]
```

7. Grave o tracker atualizado. Confirme:

```
Tracker atualizado — [N] itens fechados, [N] ainda em aberto.
Próxima prioridade: [item de maior prioridade].
```

## Exemplos

```
/trabalhista:expansion-update Alemanha
```

```
/trabalhista:expansion-update
(perguntará qual país se houver múltiplos trackers)
```

> Schema do tracker, regras de status e lógica de dependência vivem na skill
> de referência `international-expansion` — carregue antes de fazer trabalho
> substantivo.
