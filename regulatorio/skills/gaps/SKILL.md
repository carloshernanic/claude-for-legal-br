---
name: gaps
description: Tracker de lacunas abertas — o que está sinalizado e ainda não fechado. Use quando o usuário perguntar "quais lacunas estão abertas", "tracker de lacunas", "status de remediação", ou quiser fechar (--close GAP-ID) ou risk-aceitar (--accept GAP-ID) uma lacuna acompanhada.
argument-hint: "[opcional: --close GAP-ID | --accept GAP-ID]"
---

> **Adaptação BR não oficial.** Calibrado para reguladores brasileiros (ANPD, CVM, BCB, ANVISA, ANATEL, ANS, ANEEL, ANP, ANTT, ANAC, IBAMA, CADE, SENACON), Lei 9.784/1999, LINDB.

# /gaps

1. Leia o gap tracker em `~/.claude/plugins/config/claude-for-legal/regulatorio/gap-tracker.yaml`.
2. Se `--close`: marque lacuna como fechada com nota de resolução.
3. Se `--accept`: registre justificativa de risk-acceptance e quem aceitou, status → risk-accepted (LINDB art. 22 exige motivação).
4. Caso contrário: reporte lacunas abertas por idade e materialidade.

> Schema detalhado do tracker, formato do status report, lógica de notificação ao owner (confirmação per-send, sem exceções), cadência de lembretes, os modos close/risk-accept, e o consequential-action gate ficam na skill de referência **gap-surfacer** — carregue antes de trabalho substantivo.
