---
name: uninstall
description: >
  Desinstala uma skill comunitária instalada via hub. Confirma antes de apagar
  arquivos, recusa-se a tocar skills de plugin de primeira parte e registra
  toda ação. Use quando o(a) usuário(a) quer remover por completo uma skill
  comunitária ("desinstalar [skill]", "remover esta skill") em vez de só
  desativar.
argument-hint: "[nome da skill]"
---

# /uninstall

Rode o workflow `uninstall` da skill de referência `skill-manager` contra a
skill nomeada.

Regras de segurança:

1. **Só desinstale skills comunitárias instaladas via este hub.** Cheque
   `~/.claude/plugins/config/claude-for-legal/builder-hub/install-log.yaml`
   e a tabela de starter pack instalada no CLAUDE.md. Se a skill não estiver
   registrada lá, recuse e avise o(a) usuário(a).
2. **Nunca desinstale uma skill de plugin de primeira parte.** Os 12 plugins
   core que vêm com claude-for-legal estão fora dos limites deste comando. Se
   o nome resolver para um caminho dentro de um desses plugins, recuse.
3. **Confirme antes de remover arquivos.** Mostre ao(à) usuário(a) cada caminho
   que será apagado. Prossiga somente com `yes` explícito.
4. **Registre a desinstalação.** Adicione ao `install-log.yaml` a ação
   `uninstall` com timestamp para manter a trilha de auditoria intacta.

Se o(a) usuário(a) quer parar uma skill mas preservar os arquivos (p. ex.,
para reativar depois, ou para preservar configuração), sugira
`/builder-hub:disable`.

> Workflows detalhados de uninstall, disable e reativação ficam na skill de
> referência `skill-manager` — carregue-a antes de fazer trabalho relevante.
