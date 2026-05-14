# inbound/ — correspondência jurídica recebida

Esta pasta guarda triagem e trabalho de resposta para qualquer coisa que chega de fora: notificações extrajudiciais recebidas, ofícios judiciais (CPC 380) servidos contra a empresa, consultas de reguladores, notificações de preservação documental, cartas de cessar-e-desistir contra nós.

Separada de `demand-letters/` (saída) e `matters/` (portfólio rastreado) porque itens recebidos têm seu próprio workflow: ler → triar → decidir → responder (ou escalar para matéria). Nem tudo que entra vira matéria rastreada.

## Layout

```
inbound/
├── _README.md
└── [slug]/
    ├── incoming.pdf              # ou .eml / .docx — o original (ou link/pointer)
    ├── triage.md                 # análise: escopo, mérito, opções, recomendação
    └── response-v1.docx          # resposta minutada, se respondermos (v2, v3 conforme iterado)
```

## Convenções de slug

`[tipo]-[remetente-curto]-[aaaa-mm]`. Exemplos:

- `notif-rec-acme-2026-04` (notificação extrajudicial recebida)
- `oficio-silva-c-uniao-2026-04` (ofício judicial / requisição CPC 380)
- `regulador-anpd-consulta-2026-04`
- `preservacao-fornecedor-2026-04` (notificação de preservação recebida)

## Workflow

| Tipo | Comando | Saídas |
|---|---|---|
| Notificação extrajudicial recebida | `/contencioso:demand-received [path]` | triage.md + opcional resposta minutada |
| Ofício / requisição judicial | `/contencioso:subpoena-triage [path]` | triage.md + memo de objeções |
| Consulta de regulador | *skill futura* | |

Cada triagem cruza com `matters/_log.yaml` para matérias relacionadas (mesma contraparte, objeto sobreposto). Se houver matéria relacionada, a triagem flaga e oferece adicionar isto como entrada `related_matters`. Se o item recebido devesse ele próprio virar matéria, a triagem encaminha para `/matter-intake` com campos pré-populados.

## Relação com matérias

- Recebido + relacionado a matéria existente → linkar via `related_matters` em `_log.yaml`; arquivo permanece em `inbound/`.
- Recebido + deve virar matéria → criar matéria; matter.md faz cross-link para `inbound/[slug]/`.
- Recebido + tratado e encerrado (sem necessidade de matéria) → permanece em `inbound/` como registro.

## Relação com saída

Se a resposta a uma notificação recebida é ela própria uma notificação extrajudicial (uma contra-notificação), a triagem encaminha para `/demand-intake` pré-populado. A notificação extrajudicial vive em `demand-letters/`, com cross-link de volta para esta pasta inbound.
