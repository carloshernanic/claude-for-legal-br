---
name: leave-tracker
description: >
  Checa afastamentos abertos para alertas de prazo e decisões necessárias.
  Levanta apenas os afastamentos que exigem ação e explica por quê — não é
  painel de status. Use semanalmente, ou sempre que o(a) advogado(a) precisa
  saber quais afastamentos têm prazos próximos de notificação à seguradora,
  comunicação de retorno ou estabilidade acidentária.
argument-hint: "[sem argumentos — trabalha a partir de HRIS/folha ou leave-register.yaml]"
---

# /leave-tracker

Checa todos os afastamentos abertos com prazos legais duros e levanta apenas
os que exigem decisão ou ação. Não é painel de status — diz o que você
precisa fazer e por quê.

Regimes típicos no Brasil:
- Auxílio-doença (B31) e auxílio-doença acidentário (B91) — INSS, Lei 8.213/91; empresa paga os 15 primeiros dias (CLT art. 60 §3º)
- Licença-maternidade (CLT art. 392; 120 dias) + Empresa Cidadã (Lei 11.770/2008; 180 dias)
- Licença-paternidade (CF 7º XIX; 5 dias) + Empresa Cidadã (20 dias)
- Acidente do trabalho com CAT (Lei 8.213/91 art. 22) — estabilidade 12 meses pós-cessação (art. 118)
- Estabilidade gestante (ADCT art. 10 II b) — confirmação da gravidez até 5 meses pós-parto
- Afastamento por adoecimento mental / Burnout (CID-11 QD85) — desde 2022 doença ocupacional
- Licença não-remunerada / sabático — quando previsto em CCT/ACT

## Instruções

1. Carregue o agente `leave-tracker` e rode o workflow completo.

2. Se não houver HRIS/folha conectada e nem `~/.claude/plugins/config/claude-for-legal/trabalhista/leave-register.yaml`, peça
   ao(à) advogado(a) para anexar uma planilha de afastamentos ou usar
   `/trabalhista:log-leave` para adicionar entradas.

3. Alertas apenas para afastamentos que exigem ação. Afastamentos limpos
   resumidos em uma linha cada.

## Exemplos

```
/trabalhista:leave-tracker
```

Rode semanalmente — agende um lembrete para segunda-feira pela manhã para
invocar `/trabalhista:leave-tracker`. Agendamento automático exige
integração separada (lembrete de calendário, cron, etc.); agentes do Claude
Code não se auto-agendam.
