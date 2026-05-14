---
name: expansion-kickoff
description: >
  Inicia o planejamento de expansão internacional para um novo país — coleta
  intake, roda framework EOR vs. entidade local, redige perguntas para áreas
  cross-funcionais, levanta bandeiras específicas do país e cria um tracker
  persistente. Use quando alguém disser "vamos contratar no [país]", "expansão
  para o [país]", ou "primeira contratação no [país]". Nota: para a saída
  inversa — empresa estrangeira contratando empregados no Brasil — use também
  esta skill, mas com Brasil como país-alvo e atenção a CLT/Reforma 13.467/2017.
argument-hint: "[nome do país]"
---

# /expansion-kickoff

Inicia um projeto de expansão internacional para um novo país — coleta intake,
roda framework EOR vs. entidade local, redige perguntas para áreas cross-
funcionais, levanta bandeiras específicas do país e cria um tracker persistente.

## Instruções

1. Carregue `~/.claude/plugins/config/claude-for-legal/trabalhista/CLAUDE.md` → footprint jurisdicional, tabela de escalação.
2. Carregue a skill de referência `international-expansion` e rode o workflow completo.
3. Se já existir tracker para este país (`~/.claude/plugins/config/claude-for-legal/trabalhista/expansion-[slug].yaml`),
   sinalize: "Já existe um tracker de expansão para [país]. Use
   `/trabalhista:expansion-update [país]` para atualizá-lo, ou confirme
   que quer começar do zero."
4. Crie `~/.claude/plugins/config/claude-for-legal/trabalhista/expansion-[slug].yaml` ao concluir.

## Exemplos

```
/trabalhista:expansion-kickoff Alemanha
```

```
/trabalhista:expansion-kickoff
(a skill perguntará qual país)
```

> Framework EOR vs. entidade local detalhado, perguntas cross-funcionais,
> templates de briefing e schema do tracker vivem na skill de referência
> `international-expansion` — carregue antes de fazer trabalho substantivo.
