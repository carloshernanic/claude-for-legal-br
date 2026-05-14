---
name: registry-browser
description: >
  Pesquisa skills jurídicas comunitárias nos registries monitorados, mostrando
  os matches com descrição e oferecendo o SKILL.md completo antes de instalar.
  Use quando o(a) usuário(a) diz "navegar", "buscar skills", "achar uma skill
  para", "o que existe para", ou quer adicionar um novo registry à watchlist.
argument-hint: "[termo de busca]"
---

# /registry-browser

1. Carregue `~/.claude/plugins/config/claude-for-legal/builder-hub/CLAUDE.md` → registries monitorados.
2. Use o workflow abaixo.
3. Busque em cada registry. Mostre matches com descrição.
4. Ofereça mostrar SKILL.md completo para qualquer match.

---

## Finalidade

Encontrar skills entre os registries monitorados. Buscar, prever, decidir.

## Carregar contexto

`~/.claude/plugins/config/claude-for-legal/builder-hub/CLAUDE.md` → lista de registries monitorados.

## Workflow

### Passo 1: Buscar índices dos registries

Para cada registry monitorado:

- Repositórios GitHub: pegue a listagem do diretório `skills/` e o frontmatter de cada `SKILL.md` (nome + descrição).
- Registries estilo marketplace: pegue o índice.

Faça cache local do índice (`references/registry-cache.json`) para que a navegação seja rápida. Refresque o cache se tiver >7 dias ou sob solicitação.

### Passo 2: Buscar

Faça match do termo contra nomes e descrições de skill. Match por palavra-chave simples basta — o universo é pequeno o suficiente para que busca difusa seja exagero.

Também: navegação por categoria se o registry organizar assim.

### Passo 3: Apresentar matches

```markdown
## Busca: "[termo]"

**Encontradas [N] skills em [M] registries:**

### [nome-da-skill]
**De:** [nome do registry]
**Descrição:** [do frontmatter]
[Ver SKILL.md completo] [Instalar]

### [nome-da-skill]
[...]
```

### Passo 4: Prévia

Em "ver SKILL.md completo": busque e mostre o arquivo inteiro. O(a) usuário(a) lê antes de decidir instalar. Sem surpresas.

### Passo 5: Adicionar um registry

Se o(a) usuário(a) tem URL de um registry que não está na watchlist:

1. Busque, valide que é um repo de skills (tem `skills/` ou `.claude-plugin/`)
2. Mostre o que contém
3. Adicione a `~/.claude/plugins/config/claude-for-legal/builder-hub/CLAUDE.md` → registries monitorados após confirmação

## Registries monitorados por padrão

- **lpm-skills** — 14 skills de gestão de projetos jurídicos. Agnóstico de área de prática. Bom ponto de partida.
- Espaço para outros à medida que o ecossistema BR cresce — sugestões: registries da **AB2L** (associação brasileira de lawtechs), **ALAP-BR** (legal ops), **ABRADEP** (advocacia digital), **CESA**, e bibliotecas de skills curadas por legaltechs locais (Linte, NetLex, Doc9, Lawit, Contraktor, Tikal Tech, EasyJur, Astrea, JusBrasil, Aurum, ADVBOX, Themis).

## O que esta skill NÃO faz

- Instalar nada. Ela navega. O skill-installer instala.
- Pontuar ou avaliar skills. Mostra o SKILL.md; o(a) usuário(a) julga.
- Buscar na internet inteira. Só nos registries monitorados.
