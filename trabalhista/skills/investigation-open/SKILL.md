---
name: investigation-open
description: >
  Abre uma nova apuração interna — roda o intake, gera o checklist de fontes
  e cria o log persistente da apuração. Use quando uma denúncia ou alegação
  chega e o(a) advogado(a) precisa montar um workspace privilegiado de
  apuração.
argument-hint: "[breve descrição da alegação]"
---

# /investigation-open

Abre uma nova apuração — roda o intake, gera o checklist de fontes e cria o
log persistente da apuração.

## Instruções

1. Carregue `~/.claude/plugins/config/claude-for-legal/trabalhista/CLAUDE.md`.
2. Carregue a skill de referência `internal-investigation` e rode o Modo 1 (Abrir).
3. Se já existir apuração com o mesmo slug, avise antes de sobrescrever.

## Exemplos

```
/trabalhista:investigation-open
Denúncia de assédio moral contra gestor da filial de Belo Horizonte.
```

```
/trabalhista:investigation-open
(a skill perguntará os detalhes)
```

> Intake detalhado, requisitos de formação de sigilo profissional, checklist
> de fontes e templates de log vivem na skill de referência
> `internal-investigation` — carregue antes de fazer trabalho substantivo.
