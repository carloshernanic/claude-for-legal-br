# handoffs/ — memorandos de passagem de caso ao fim do semestre

Pasta por semestre com um memorando de passagem para cada caso ativo. Produzida por `/npj:semester-handoff` no fim do semestre. Lida pelos(as) estagiários(as) que entram durante o `/ramp`, para os casos que herdam.

## Layout

```
handoffs/
├── _README.md                             # este arquivo
└── [AAAA-semestre]/                       # ex.: 2026-1, 2026-2
    ├── _resumo.md                         # consolidado entre casos: o que transita, quem para quem
    ├── [id-do-caso].md                    # um por caso ativo
    └── ...
```

## Slug / convenções de pasta

Pasta do semestre: `[ano]-[1|2]` (semestre letivo). Exemplos:
- `2026-1`
- `2026-2`

(Se o NPJ trabalha por outra cadência — quadrimestre, modular —, ajuste a nomenclatura.)

Memorando do caso: use o `case_id` (de `deadlines.yaml` ou do registro de atendimento). Consistente com outros arquivos por caso.

## O que contém um memorando de passagem

- Resumo do caso (fatos, área de atuação, fase processual atual)
- Nome do(a) estagiário(a) que sai + relação construída com o(a) assistido(a) (se relevante)
- Prazos pendentes (extraídos de `deadlines.yaml`, contemplando CPC arts. 218-235 — dias úteis no processo civil — e prazos especiais: penais em dias corridos CPP, trabalhistas CLT, administrativos)
- Questões em aberto / decisões pendentes
- Histórico de comunicações (extraído de `client-comms/[id-do-caso]/log.md`)
- Documentos minutados / protocolados até o momento (ponteiros para os arquivos do caso)
- O que o(a) estagiário(a) que entra precisa saber / fazer primeiro
- Flags do(a) professor(a) supervisor(a) para o(a) estagiário(a) que entra (se houver)

**Casos sob segredo de justiça** (CPC art. 189; ECA art. 143; Lei Maria da Penha; Lei 9.474/97 art. 23) recebem tratamento de acesso restrito. O memorando permanece dentro do círculo de sigilo do NPJ; transferências de pasta seguem orientação da coordenação. LGPD aplica-se a todos os dados pessoais.

## Fluxo de trabalho

1. `/npj:semester-handoff` é rodado pelo(a) professor(a) supervisor(a) — ou pelos(as) próprios(as) estagiários(as) que estão saindo, em seus casos — cerca de 1 a 2 semanas antes do fim do semestre.
2. Gera memorandos por caso + resumo geral.
3. A turma que entra roda `/npj:ramp` no início do próximo semestre; o `/ramp` traz à tona os memorandos de passagem para os casos que cada novo(a) estagiário(a) recebe.

## Retenção

Memorandos de passagem ficam em disco. Históricos são úteis para o próprio registro do NPJ sobre transições de caso e para estagiários(as) que queiram ver como um caso evoluiu ao longo dos semestres. Retenção do dossiê do(a) assistido(a) é matéria do regimento interno do NPJ + Provimento CFOAB 188/2018; segredo de justiça e LGPD permanecem aplicáveis.
