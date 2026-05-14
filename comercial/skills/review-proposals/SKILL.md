---
name: review-proposals
description: >
  Review and approve (or reject) pending playbook update proposals from the
  playbook-monitor agent and apply approved changes to the practice profile. Use
  when the playbook-monitor agent has surfaced proposals, when the user says
  "review playbook proposals", "what playbook updates are pending", or wants to
  step through deviation-driven playbook changes.
argument-hint: "[no arguments needed — works from the pending proposals file]"
---

# /review-proposals

**Adaptação BR.** Plugin adaptado ao Brasil.

Passa por propostas pendentes de atualização do playbook geradas pelo agente monitor e aplica mudanças aprovadas em `~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md`.

## Instruções

1. **Carregue o agente playbook-monitor** e rode o Passo 5 (fluxo de revisão e aprovação).

2. **Se não existir arquivo de propostas** ou estiver vazio: responda *"Sem propostas pendentes. Playbook está em dia."* Não prossiga.

3. **Apresente propostas uma de cada vez.** Para cada uma, mostre o bloco completo e ofereça quatro opções: Aceitar, Rejeitar, Editar, Adiar.

4. **Para Aceitar ou Editar:** mostre o diff exato para `~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md` antes de escrever. Só aplique após confirmação explícita do(a) advogado(a).

5. **Para Rejeitar ou Adiar:** logue a decisão. Não modifique `~/.claude/plugins/config/claude-for-legal/comercial/CLAUDE.md`.

6. **Depois que todas as propostas forem resolvidas:** mostre resumo do que mudou e arquive o arquivo de propostas.

## Exemplos

```
/comercial:review-proposals
```

```
/comercial:review-proposals
(roda automaticamente depois que o playbook-monitor te notifica)
```
