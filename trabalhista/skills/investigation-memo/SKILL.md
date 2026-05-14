---
name: investigation-memo
description: >
  Redige ou atualiza o memorando privilegiado de apuração interna a partir do
  log da apuração. Use quando a apuração estiver em estágio suficiente para o
  primeiro corte do memo, ou quando novos dados foram adicionados e o rascunho
  existente precisa de atualização.
argument-hint: "[nome da apuração]"
---

# /investigation-memo

Redige o primeiro corte do memorando privilegiado a partir do log da apuração,
ou atualiza um rascunho existente quando novos dados foram adicionados.

## Instruções

1. Carregue a skill de referência `internal-investigation` e rode o Modo 4 (Redigir ou atualizar memo).
2. Se for a primeira versão, avise se ainda há fontes de alta prioridade
   em aberto no checklist.
3. Se for atualização, mostre o que mudou antes de reescrever.
4. Toda saída é marcada como PRIVILEGIADO E CONFIDENCIAL — MATERIAL DE TRABALHO
   PREPARADO SOB ORIENTAÇÃO DE ADVOGADO(A) (EOAB art. 7º XIX; CPC art. 388).

## Exemplos

```
/trabalhista:investigation-memo [nome da apuração]
```

```
/trabalhista:investigation-memo [nome da apuração]
(atualiza o memo existente, se houver)
```

> Estrutura do memo, framework de avaliação de credibilidade e regras de
> atualização vivem na skill de referência `internal-investigation` — carregue
> antes de fazer trabalho substantivo.
