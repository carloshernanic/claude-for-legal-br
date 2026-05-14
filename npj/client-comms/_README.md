# client-comms/ — registros de atendimento por caso

Uma pasta por caso. Dentro, um `log.md` corrente registrando todo contato com o(a) assistido(a) — entrada e saída, em telefone, e-mail, WhatsApp, carta e atendimento presencial. Produzido e acrescentado por `/npj:client-comms-log`.

## Layout

```
client-comms/
├── _README.md                     # este arquivo
└── [id-do-caso]/
    └── log.md                     # log append-only
```

## Slug

Use o mesmo ID do caso adotado em outros lugares (ficha de atendimento, `case_id` em `deadlines.yaml`). Um caso = uma pasta.

## Por que isso existe

- **Defesa em ação de responsabilidade civil do(a) advogado(a) / disciplinar OAB** — "comunicamos X na data Y" precisa de registro. EOAB art. 32; CED-OAB; jurisprudência STJ sobre responsabilidade do(a) advogado(a).
- **Continuidade na passagem semestral** — o(a) estagiário(a) que entra lê o log e conhece a história do(a) assistido(a) sem precisar re-entrevistar.
- **Visibilidade de padrões** — cinco recados não retornados em seis semanas é flag de supervisão.
- **Guarda do dossiê do(a) assistido(a)** — NPJs têm obrigações de retenção (regimento interno + Provimento CFOAB 188/2018 sobre documentação); esse log integra o dossiê.
- **LGPD** — base legal para o tratamento desses dados (Lei 13.709/2018 art. 7º, IX — legítimo interesse e execução de política pública educacional; art. 11 para sensíveis quando aplicável). Discutir com a coordenação a designação de encarregado(a).

## Como são as entradas do log

```markdown
## [AAAA-MM-DD HH:MM] — [entrada / saída] — [meio]

**Quem (estagiário(a)):** [nome]
**Quem (lado do(a) assistido(a)):** [nome do(a) assistido(a), ou terceiro(a) se ligação de advogado(a) da parte contrária etc.]
**Duração / extensão:** [ligação de 10 min | e-mail de 3 parágrafos | carta de 2 páginas]

**Resumo:**
[O que aconteceu, 2-4 frases. Conteúdo mais tom quando relevante.]

**Itens de ação:**
- [Item que o(a) estagiário(a) deve ao(à) assistido(a), com prazo]
- [Item que o(a) assistido(a) deve ao(à) estagiário(a), com tempo esperado]

**Follow-up em:** [data se aplicável]

**Notas:**
[Qualquer coisa que importe e não couber acima — idioma utilizado, dinâmica familiar observada, nível de ansiedade, sinais de violência, fatos sensíveis sob segredo de justiça CPC art. 189]
```

## O que esta pasta NÃO contém

- Análise jurídica substantiva (essa fica nos arquivos de atendimento / memorando / status)
- Minutas de documentos (essas ficam em pastas separadas de cada caso)
- Notas privilegiadas só para advogado(a) (essas ficam no que o NPJ usa para notas internas de caso, sob sigilo profissional EOAB art. 7º XIX)

O log de comunicações é o registro factual do contato, não trabalho jurídico em sentido estrito. Mantenha conteúdo aqui; mantenha estratégia e análise em outro lugar.

## Retenção

Append-only. Nunca edite entradas passadas — se algo estava errado ou precisa de esclarecimento, acrescente nova entrada referenciando a anterior. O registro do que foi dito e quando integra o dossiê do(a) assistido(a); reescrever a história derrota o propósito.

**Casos sob segredo de justiça** (CPC art. 189 — família, infância, dados pessoais sensíveis; ECA art. 143; Lei Maria da Penha; Lei 9.474/97 art. 23 — refúgio): aplicar tratamento de acesso restrito conforme orientação da coordenação. O log permanece dentro do círculo de sigilo do NPJ.
