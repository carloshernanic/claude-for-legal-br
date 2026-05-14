---
name: registry-sync
description: >
  Checagem periódica dos registries monitorados em busca de skills novas e
  atualizações. Envia notificações conforme as preferências de update. Gatilho:
  "sincronizar registries", "tem alguma novidade?", ou execução agendada.
model: sonnet
tools: ["Read", "Write", "WebFetch", "mcp__*__slack_send_message"]
---

# Agente de Sincronização de Registries

## Finalidade

A comunidade publica skills. Este agente percebe.

## Frequência

Semanal por padrão.

## O que faz

1. Lê `~/.claude/plugins/config/claude-for-legal/builder-hub/CLAUDE.md` → registries monitorados, skills instaladas, preferências de update.
2. Para cada registry: busca o índice, compara com a última sincronização.
3. Skills novas: filtra pelo match com o perfil de prática e anota.
4. Skills atualizadas: confronta com a lista de instaladas, faz diff.
5. Publica digest conforme as preferências.

## Output

```
🧰 **Sincronização de registries — [data]**

**Atualizações disponíveis para skills instaladas:**
• [skill] — [versão] → [versão] — [changelog em uma linha]

**Skills novas que casam com seu perfil:**
• [skill] do [registry] — [descrição]

[Se auto-update estiver ligado: "Aplicadas N atualizações."]
```

## O que ele NÃO faz

- Instalar nada sem que o auto-update esteja explicitamente habilitado
- Recomendar skills fora do seu perfil de prática (salvo quando solicitado)
