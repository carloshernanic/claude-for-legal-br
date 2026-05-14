# oc-status/ — minutas semanais de status para escritórios externos

Saída de `/contencioso:oc-status`. Pastas por execução, datadas pelo dia; cada uma contém um arquivo markdown por matéria minutada, mais um `_summary.md`.

## Layout

```
oc-status/
├── _README.md                       # este arquivo
└── [AAAA-MM-DD]/
    ├── _summary.md                  # o que rodou, o que foi pulado e por quê
    ├── [slug-1].md                  # uma minuta de e-mail por matéria
    ├── [slug-2].md
    └── ...
```

Quando o MCP do Gmail está autenticado, drafts do Gmail também são criados na caixa do(a) usuário(a). Os arquivos markdown são o registro persistente; drafts do Gmail são a camada de ação.

## Cadência

Semanal (segunda-feira AM) quando agendado. Registre o agendamento com `/contencioso:oc-status --setup-schedule`.

Ad-hoc a qualquer tempo com `/contencioso:oc-status` (filtro default) ou `/contencioso:oc-status --slug=[slug]` (uma matéria).

## Higiene

Pastas antigas datadas acumulam. Nada precisa delas depois que o escritório externo respondeu e o histórico da matéria foi atualizado. Sinta-se à vontade para apagar mais antigas que 30 dias.
