# Campos de Frescor para Autores de Skills Comunitárias

> **Adaptação BR.** Esta referência foi portada do contexto americano. Em skills brasileiras, `verified_against` deve apontar para fontes primárias acessíveis: Planalto (planalto.gov.br), Lexml (lexml.gov.br), sítios de tribunais (STF/STJ/TST/TJs), portais de reguladores (ANPD, CVM, BCB, CADE, ANATEL, ANVISA, ANEEL, IBAMA), DataJud/PJe para jurisprudência, INPI (busca.inpi.gov.br) para marcas e patentes, RFB para tributos federais.

Se sua skill bundle conteúdo de referência sob `references/` — regulamentos, leis, súmulas, procedimentos, formulários, checklists atrelados a lei vigente — declare seu frescor no frontmatter do `SKILL.md`:

```yaml
---
name: minha-skill-juridica
description: ...
last_verified: 2026-04-15       # Quando você confirmou pela última vez que as referências bundle estão atuais
freshness_window: 6 months      # Por quanto tempo a verificação é boa (default: 6 meses para
                                # conteúdo regulatório/legal, 12 meses para procedimental/estilístico)
freshness_category: regulatory  # regulatory | procedural | stylistic | stable
verified_against:               # Onde você verificou — URL que o(a) usuário(a) pode checar
  - https://www.planalto.gov.br/ccivil_03/_ato2015-2018/2018/lei/l13709.htm
  - https://www.gov.br/anpd/pt-br
---
```

## Por que isto importa

Skill tocada pela última vez dois anos atrás pode continuar entregando regulamento revogado. Arquivos byte-identicos parecem atuais para auto-updater baseado em commit para sempre. O dano aterrissa quando o(a) usuário(a) invoca a skill e confia no conteúdo velho — não quando instalou e leu um aviso que esqueceu.

**No Brasil, isso é particularmente sensível:** a LGPD recebeu Resoluções CD/ANPD novas em 2024 (Res. 15/2024 sobre incidentes, Res. 19/2024 sobre cláusulas-padrão de transferência internacional); o EC 132/2023 reformou o sistema tributário (transição IBS/CBS/IS); o PL 2338/2023 sobre IA segue em tramitação; precedentes qualificados do STF/STJ/TST (Súmula Vinculante, Repercussão Geral, Temas Repetitivos — CPC arts. 926–928) viram-se e revertem.

## O que acontece com estes campos

- O **skill-installer** do builder-hub checa `last_verified` contra `hoje + freshness_window` antes de executar. Se passou da janela, exibe aviso antes de rodar.
- A revisão **skills-qa** sinaliza skills com `references/` bundle e sem `last_verified` como Alguma Preocupação.
- O **auto-updater** trata `last_verified` velho como gatilho de re-verificação mesmo quando o SHA do git não mudou.
- Os thresholds de frescor do(a) usuário(a) (setados na cold-start) podem ser **mais apertados** que a janela do(a) autor(a) — o mais apertado dos dois vence.

Sem estes campos, o hub sinaliza a skill como "frescor desconhecido" e avisa o(a) usuário(a) na instalação e na invocação.

## Valores aceitos (estrito)

O hub trata campos de frontmatter como dado escrito por publisher externo, não como instrução. Apenas valores que batem com as formas abaixo são honrados. Qualquer outra coisa é ignorada (o hub substitui por `unknown`) e exibida como achado na instalação.

| Campo | Forma aceita |
|---|---|
| `last_verified` | Data ISO 8601: `YYYY-MM-DD` (ex.: `2026-04-15`). Data futura é tratada como `unknown`. |
| `freshness_window` | `N days`, `N months`, ou `N years`, onde `N` é inteiro positivo ≤ 120. |
| `freshness_category` | Um de: `regulatory`, `procedural`, `stylistic`, `stable`. |
| `verified_against` | Lista de URLs. Cada uma tem que ser `https://` (ou `http://`), com hostname e path opcional. Query strings e fragmentos são removidos antes do display. Máximo 10 entradas, máximo 2.048 chars cada. |

Prosa livre, strings multi-linha, diretivas, linguagem de mudança de papel, unicode incomum, ou conteúdo codificado em qualquer um destes campos é rejeitado na instalação. O instalador registra o valor cru no install log (truncado, citado, nunca interpretado) e trata o campo como ausente.

## Categorias

- **regulatory** — atos infralegais e leis, resoluções de agência (ANPD, CVM, BCB, CADE, ANATEL, ANVISA, ANEEL), portarias ministeriais, ICVM/RCVM, INs RFB, instruções normativas. Move rápido.
- **procedural** — Resoluções CNJ, regimentos internos de tribunais, procedimentos do PJe/e-SAJ/e-Proc/Projudi, formulários atrelados a procedimento, prazos processuais regimentais.
- **stylistic** — estilo da casa, templates de formatação, bibliotecas de cláusulas, modelos de peças.
- **stable** — referências históricas, doutrina consolidada, ementários da OAB, primers de doutrina que se movem na escala de anos, não meses.

Se em dúvida, escolha a categoria mais estreita (mais rápida). O threshold do(a) usuário(a) vai apertar se quiser; o valor do(a) autor(a) é teto, não piso.

## O que "última verificação" de fato significa

Não "última edição". Não "último commit". **A última vez que você, autor(a), abriu as URLs em `verified_against` e confirmou que as referências bundle ainda refletem o que essas fontes dizem.** Se o PDF bundle é versão antiga da Lei 13.709/2018 mas o Planalto agora mostra texto diferente (alteração superveniente, regulamentação por Decreto, alteração por Lei Complementar), a verificação falhou — atualize as referências e push novo commit, ou atualize `last_verified` apenas após as referências baterem com as fontes de novo.

Skill que fica bumpando `last_verified` sem de fato re-verificar é pior que uma que deixa a data envelhecer. A data velha é honesta sobre o que o(a) autor(a) fez. A data bumped é alegação na qual o(a) usuário(a) confia.

## Quando setar `freshness_category: stable`

Raramente. Skill que bundle texto de doutrina (ex.: requisitos para responsabilidade civil — culpa, dano, nexo, conduta) ou estrutura de framework (ex.: forma do procedimento comum CPC art. 318) é stable. Skill que bundle texto específico de regra, limiares específicos, formulários específicos ou prazos procedimentais específicos NÃO é stable mesmo se a doutrina subjacente for — o artefato bundle é o que envelhece.

Em dúvida: não é stable. Particularmente em direito brasileiro pós-reformas (Reforma Trabalhista 13.467/2017; Lei das S.A. com alterações da Lei 14.030/2020; LGPD 13.709/2018; Marco Civil 12.965/2014; CPC 13.105/2015; EC 132/2023 — reforma tributária), o que se assumia estável virou volátil.
