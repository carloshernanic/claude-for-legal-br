# demand-letters/ — trabalho de notificações extrajudiciais (pré-contencioso)

Esta pasta guarda o material de trabalho de cada notificação extrajudicial que o(a) advogado(a) envia: notificações de cobrança, notificações de inadimplemento / interpelação para cura, cessar-e-desistir, notificações de desligamento trabalhista, notificações de preservação documental.

Separada de `matters/` porque:

- Nem toda notificação vira matéria rastreada. Cobranças de pequeno valor e cobranças de rotina não precisam de linha no registro.
- Toda notificação tem o mesmo formato de workflow (intake → minuta → envio → checklist), independentemente de virar matéria depois.
- Quando uma notificação vira matéria, o `matter.md` da matéria faz cross-link para cá — o histórico de redação fica com a carta.

## Layout

```
demand-letters/
├── _README.md                     # este arquivo
└── [slug]/
    ├── intake.md                  # captura de contexto, estratégia, leverage, filtros de sigilo
    ├── draft-v1.docx              # a notificação (v2, v3 conforme iterado)
    └── checklist.md               # checklist pós-envio — entrega, cópias, prazos calendarizados, follow-up
```

## Convenções de slug

`[tipo]-[contraparte]-[aaaa-mm]`. Exemplos:

- `cobranca-acme-2026-04`
- `cessar-desistir-concorrente-x-2026-04`
- `inadimplemento-fornecedor-2026-04`
- `desligamento-silva-2026-04`
- `preservacao-fornecedor-2026-04`

## Workflow

1. `/contencioso:demand-intake [título]` → roda intake adaptativo, escreve `intake.md`
2. `/contencioso:demand-draft [slug]` → roda checklist de sigilo de tratativas (CPC 166 §3º; Lei 13.140/2015 art. 30) / sigilo profissional (EOAB art. 7º XIX) / waiver, minuta `.docx`, escreve `checklist.md`, oferece criar matéria

## Relação com matérias

Após a notificação ser minutada, `demand-draft` avalia materialidade (heurística do `~/.claude/plugins/config/claude-for-legal/contencioso/CLAUDE.md` da casa) e oferece criar matéria. Em caso afirmativo, uma linha vai para `matters/_log.yaml` com `source: demand-letter`, e `matters/[matter-slug]/matter.md` faz cross-link para a pasta desta notificação.

Notificações imateriais ficam aqui apenas. Continuam sendo registro de material de trabalho — só não rastreadas no portfólio.

## Correções e versões

Nunca sobrescreva minuta enviada. Se a notificação foi enviada e precisa de revisão (ex. notificação suplementar), inicie `draft-v2.docx`. O histórico de versões é registro útil em si.
