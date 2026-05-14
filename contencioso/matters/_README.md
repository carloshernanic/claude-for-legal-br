# matters/ — dados do portfólio

Esta pasta guarda o portfólio. Duas camadas:

- **`_log.yaml`** — o registro. Uma linha por matéria. Parseável por skills. Fonte da verdade para rollups.
- **`[slug]/`** — detalhe por matéria. Narrativa e histórico. Onde humanos leem e editam.

## Layout

```
matters/
├── _log.yaml                  # registro (todas as matérias, incluindo encerradas)
├── _README.md                 # este arquivo
└── [matter-slug]/
    ├── matter.md              # intake narrativo + tese + posição processual
    └── history.md             # log de eventos somente-append
```

## Convenções de slug

Minúsculas, hífens, ano no final. Exemplos:
- `acme-c-uniao-2026`
- `consulta-anpd-2026`
- `trabalhista-silva-2026`

O ano torna o slug estável mesmo que matéria similar surja depois. O nome da pasta bate exatamente com o slug.

## Quem escreve o quê

| Arquivo | Escrito por | Editar diretamente? |
|---|---|---|
| `_log.yaml` | `/matter-intake`, `/matter-update`, `/matter-close` | Sim, mas reflita a alteração no `history.md` da matéria |
| `matter.md` | `/matter-intake` no intake; appended por `/matter-close` | Sim, para evoluir tese / posição processual |
| `history.md` | `/matter-intake` semeia; `/matter-update` e `/matter-close` fazem append | Append-only na prática — trate entradas passadas como registro |

## Matérias encerradas

Ficam aqui. Não apague. `/portfolio-status` filtra-as do rollup ativo por padrão; `/portfolio-status --all` as inclui. Matérias encerradas são o training set para julgamento de portfólio.

## Correções

Se uma entrada histórica estava errada, não a edite. Adicione nova entrada que a referencie e corrija. O registro da correção é tão importante quanto a correção em si.
