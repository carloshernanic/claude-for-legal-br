---
name: investigation-query
description: >
  Pergunte ao log de uma apuração aberta — o que cada depoente disse, onde os
  relatos conflitam, que lacunas existem, qual a prova mais forte sobre cada
  ponto. Use quando o(a) advogado(a) precisa consultar o registro da apuração
  sem reler cada entrada.
argument-hint: "[nome da apuração] [pergunta]"
---

# /investigation-query

Responde perguntas contra o log da apuração — o que os depoentes disseram,
onde os relatos conflitam, quais as lacunas, qual a prova mais forte em cada
ponto.

## Instruções

1. Carregue a skill de referência `internal-investigation` e rode o Modo 3 (Query).
2. Sempre cite IDs de entradas do log na resposta.
3. Se o log não contém nada relevante para a pergunta, diga explicitamente —
   "Não vi nenhuma informação sobre [tópico] neste log de apuração
   ([N] entradas revisadas)" — e ofereça sinalizar como lacuna.

## Exemplos

```
/trabalhista:investigation-query [nome da apuração]
O que o(a) reclamado(a) disse sobre a confraternização de dezembro?
```

```
/trabalhista:investigation-query [nome da apuração]
Em que pontos os relatos do(a) reclamante e do(a) reclamado(a) conflitam?
```

```
/trabalhista:investigation-query [nome da apuração]
O que ainda falta levantar?
```

> Processo de query do log, regras de citação e templates de sinalização de
> lacunas vivem na skill de referência `internal-investigation` — carregue
> antes de fazer trabalho substantivo.
