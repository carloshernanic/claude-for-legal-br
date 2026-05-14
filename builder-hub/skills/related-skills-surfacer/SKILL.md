---
name: related-skills-surfacer
description: >
  Sugere skills comunitárias com base em atividade recente em outros plugins.
  Checa se a comunidade construiu algo relevante para uma tarefa e menciona
  uma vez, de forma não-intrusiva. Use quando o(a) usuário(a) diz "tem skill
  comunitária para isso", "o que mais existe", ou pede recomendação de skill;
  roda também passivamente como parte dos workflows de outros plugins.
---

# /related-skills-surfacer

1. Carregue `~/.claude/plugins/config/claude-for-legal/builder-hub/CLAUDE.md` → perfil de prática.
2. Use o workflow abaixo.
3. Cheque o que os outros plugins andaram fazendo. Faça match com o registry.
4. Sugira: "Você esteve fazendo X — a comunidade tem skill para Y, relacionada."

---

## Finalidade

A comunidade pode já ter construído o que você está prestes a construir. Esta skill percebe e menciona — uma vez, brevemente, sem irritar.

## Como roda

Esta skill sugere skills comunitárias relacionadas após uma tarefa. Pode ser invocada diretamente pelo(a) usuário(a) ("o que mais existe para X?") ou conectada a outros plugins via Stop hook — o padrão via hook exige que cada plugin irmão declare um Stop hook que chame esta skill, o que não é instalado por padrão. Sem o hook, invoque diretamente.

Outros plugins podem incluir checagem leve ao fim da tarefa:
> "O builder-hub encontrou skill comunitária que pode ajudar nesse tipo de coisa: [nome] — [uma linha]. Quer dar uma olhada?"

## Carregar contexto

`~/.claude/plugins/config/claude-for-legal/builder-hub/CLAUDE.md` → perfil de prática, skills instaladas (não sugira o que já está instalado).
Cache de registries do registry-browser.

## O match

Dada uma descrição da tarefa (o que o(a) usuário(a) acabou de fazer), encontre skills no registry que combinem:

- Sobreposição de palavras-chave entre tarefa e descrições de skill
- Encaixe no perfil de prática (não sugira skill de contencioso para advogado(a) transacional)
- Não está já instalada

**Limiar:** Só sugira se o match for forte. Match fraco é ruído. Melhor sugerir nada do que irritar.

## Output

Se houver match forte:
> 💡 A comunidade tem uma skill para isso: **[nome]** do [registry] — "[descrição]". `/builder-hub:skill-installer [nome]` para experimentar.

Se não houver match forte: silêncio. Sem output. Não anuncie "não achei nada".

## Limite de frequência

Não sugira a mesma skill duas vezes. Se o(a) usuário(a) não instalou da primeira, viu e decidiu não. Rastreie dispensas em `references/surfaced.json`.

## Controle do(a) usuário(a)

Conforme `~/.claude/plugins/config/claude-for-legal/builder-hub/CLAUDE.md` → notificações de skill nova:
- **Todas:** sugere qualquer match
- **Casando com perfil de prática:** filtra por perfil (padrão)
- **Nenhuma:** esta skill fica desligada

## Encerre com a árvore de decisão de próximos passos

Encerre com a árvore de decisão de próximos passos conforme `## Outputs` do CLAUDE.md. Customize as opções para o que esta skill acabou de produzir — os cinco ramos padrão (minutar o X, escalar, buscar mais fatos, acompanhar e aguardar, outra coisa) são ponto de partida, não trava. A árvore É o output; o(a) advogado(a) escolhe.

## O que esta skill NÃO faz

- Instalar qualquer coisa.
- Interromper tarefa em curso. A sugestão acontece ao *fim* da tarefa, não no meio.
- Importunar. Uma menção por skill, para sempre.
