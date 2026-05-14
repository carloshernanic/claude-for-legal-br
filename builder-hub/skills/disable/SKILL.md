---
name: disable
description: >
  Desativa uma skill comunitária instalada via hub sem remover os arquivos.
  Use quando o(a) usuário(a) quer silenciar temporariamente uma skill
  comunitária ("desativar [skill]"), parar os hooks dela enquanto preserva a
  configuração, ou reativar uma skill antes desativada.
argument-hint: "[nome da skill]"
---

# /disable

Rode o workflow `disable` da skill de referência `skill-manager` contra a skill
nomeada.

O que o disable faz:

- Renomeia o `SKILL.md` da skill para `SKILL.md.disabled`, de modo que o Claude
  deixa de descobri-la como skill ativa. Arquivos, references, templates e
  configuração permanecem no lugar.
- Se a skill embala hooks em `hooks/hooks.json`, também renomeia esse arquivo
  para `hooks.json.disabled` para que nenhum gatilho automático dispare
  enquanto a skill está desativada.
- Registra a ação em
  `~/.claude/plugins/config/claude-for-legal/builder-hub/install-log.yaml`.

Regras de segurança:

1. **Só desative skills comunitárias instaladas via este hub.** Mesma checagem
   do uninstall — consulte o install log e a tabela de instaladas no
   CLAUDE.md.
2. **Nunca desative uma skill de plugin de primeira parte.** Fora dos limites.
3. **Confirme antes de renomear.** Mostre os caminhos, obtenha `yes`
   explícito.

Reative rodando o mesmo comando com o mesmo nome de skill — o workflow do
skill-manager reconhece uma skill desativada e reverte o rename.

> Workflows detalhados de uninstall, disable e reativação ficam na skill de
> referência `skill-manager` — carregue-a antes de fazer trabalho relevante.
