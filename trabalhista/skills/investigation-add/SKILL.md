---
name: investigation-add
description: >
  Adiciona dados a uma apuração interna aberta — documentos, anotações de
  oitiva ou observações. Processa lotes contra os critérios de pull
  documentados, levanta itens significativos e registra tudo o que foi
  revisado para verificação de cobertura. Use quando chegarem novas provas,
  anotações de oitiva ou lotes documentais em uma apuração aberta.
argument-hint: "[nome ou slug da apuração, depois cole ou anexe os dados]"
---

# /investigation-add

Adiciona dados ao log de uma apuração aberta. Processa lotes documentais
usando critérios de pull documentados, levanta itens significativos, registra
tudo o que foi revisado para verificação de cobertura.

## Instruções

1. Carregue `~/.claude/plugins/config/claude-for-legal/trabalhista/CLAUDE.md`.
2. Carregue a skill de referência `internal-investigation` e rode o Modo 2 (Adicionar dados).
3. Após processar, mostre a razão de surface e a lista de itens levantados.
4. Pergunte se deve atualizar o checklist de fontes se os dados cobrem algum item.

## Exemplos

```
/trabalhista:investigation-add [nome da apuração]
[cole as anotações de oitiva]
```

```
/trabalhista:investigation-add [nome da apuração]
[anexe export de e-mails]
```

> Processo de needle-finding detalhado, formato de entrada de log, regras de
> surface-ratio e rastreamento do checklist de fontes vivem na skill de
> referência `internal-investigation` — carregue antes de fazer trabalho
> substantivo.
