---
name: handbook-updates
description: >
  Compara uma proposta de alteração no manual interno (código de conduta /
  regulamento interno) contra a versão atual, e sinaliza efeitos cascata e
  impacto sobre suplementos por categoria/UF/CCT. Use quando o(a) usuário(a)
  disser "atualizar o manual", "adicionar isto ao manual", "mudança no
  regulamento interno", ou quando há política pronta para inserção.
---

# Atualizações de Manual Interno / Regulamento Interno

## Contexto de matéria

**Contexto de matéria.** Cheque `## Workspaces de matéria` no CLAUDE.md a nível de prática. Se `Habilitado` for `✗` (default para in-house), pule o resto deste parágrafo — skills usam contexto a nível de prática e a maquinaria de matéria fica invisível. Se habilitado e não há matéria ativa, pergunte: "De qual matéria é isto? Rode `/trabalhista:matter-workspace switch <slug>` ou diga `nível-de-prática`." Carregue `matter.md` da matéria ativa para contexto e overrides específicos. Grave saídas na pasta da matéria em `~/.claude/plugins/config/claude-for-legal/trabalhista/matters/<slug>/`. Nunca leia arquivos de outra matéria salvo se `Contexto cross-matter` estiver `on`.

---

## Propósito

Alterações em manual interno (ou regulamento interno) têm efeito cascata. Mude a política de férias e você afetou o cálculo de verbas rescisórias, a referência cruzada com a política de afastamentos e três suplementos por CCT/UF. Esta skill encontra os efeitos antes que virem inconsistência.

**Importante:** o **regulamento interno** no Brasil, uma vez aceito pelo(a) empregado(a) (expressa ou tacitamente), integra o contrato individual de trabalho (CLT art. 444; jurisprudência TST). Reduzir benefício oferecido pode caracterizar **alteração unilateral lesiva** (CLT art. 468) — risco trabalhista relevante.

## Carregue contexto

`~/.claude/plugins/config/claude-for-legal/trabalhista/CLAUDE.md` → local do manual, suplementos por categoria/UF/CCT, cadência de atualização.

## Workflow

### Passo 1: Obtenha a mudança

- Qual seção está mudando?
- Qual o novo texto?
- Por quê? (Exigência legal, decisão de política, ajuste)

### Passo 2: Diff contra a atual

Leia a seção atual do manual. Mostre o diff:

```diff
- [texto antigo]
+ [texto novo]
```

### Passo 3: Encontre referências cruzadas

Busque no manual referências à seção alterada:

- Outras políticas que citam esta ("ver política de férias para taxas de aquisição")
- Termos definidos que esta seção usa ou define
- Suplementos por CCT/UF/categoria que modificam esta seção

Para cada referência: ainda faz sentido após a mudança? Sinalize as que quebram.

### Passo 4: Impacto sobre suplementos por CCT/UF/categoria

Para cada suplemento em `~/.claude/plugins/config/claude-for-legal/trabalhista/CLAUDE.md`:

- Este suplemento modifica a seção que está sendo alterada?
- A mudança torna o suplemento obsoleto, errado ou incompleto?
- A mudança cria necessidade de um *novo* suplemento (categoria sindical com CCT mais restritiva, UF com PEC/legislação local, regime especial)?

### Passo 5: Checagem de promessa (alteração unilateral lesiva — CLT art. 468)

A mudança está reduzindo algo que a versão anterior prometia (PLR, benefício, gratificação habitual, dia de folga, condição de teletrabalho)?

Se sim: **bandeira vermelha**. O Brasil é estrito quanto à alteração contratual lesiva:
- **CLT art. 468:** alterações contratuais dependem de mútuo consentimento E não podem causar prejuízo (direta ou indiretamente) ao(à) empregado(a) — sob pena de nulidade.
- **Princípio da inalterabilidade contratual lesiva** (Súmulas TST diversas).
- **Estabilidade financeira / direito adquirido:** benefícios concedidos por mais de 10 anos ganharam status de cláusula contratual (Súmula 51 TST item I).
- **Gratificação habitual há mais de 10 anos:** não pode ser suprimida (Súmula 372 TST).
- **CCT/ACT:** se o benefício está em norma coletiva vigente, alteração depende de nova negociação coletiva.

Sinalize. Não bloqueie — mas sinalize com força, e proponha alternativas: aplicação apenas a novos admitidos, negociação coletiva, programa de transição.

## Saída

```markdown
## Atualização de Manual: [Nome da seção]

### Mudança

[diff]

### Impacto sobre referências cruzadas

| Seção | Referencia seção alterada | Ainda correta? | Correção necessária |
|---|---|---|---|
| [nome] | [como] | ✅/⚠️ | [o quê] |

### Impacto sobre suplementos por CCT/UF/categoria

| Categoria/UF | Suplemento atual | Após mudança | Ação |
|---|---|---|---|
| [categoria] | [o que diz] | [ainda válido / obsoleto / precisa atualizar] | [nenhuma / atualizar / novo suplemento necessário] |

### Checagem de promessa (CLT art. 468)

[Se redução de benefício: bandeira + análise de risco trabalhista por:
- Princípio da inalterabilidade lesiva
- Súmula 51 TST (estabilidade financeira)
- Súmula 372 TST (gratificação habitual)
- CCT/ACT aplicável
- Sugestão de alternativas: aplicação apenas a novos admitidos / negociação coletiva / programa de transição]

### Pronto para publicar

- [ ] Referências cruzadas atualizadas
- [ ] Suplementos por CCT/UF/categoria atualizados
- [ ] [Se redução de benefício: aviso prévio / negociação coletiva / aplicação apenas a novos admitidos endereçado]
- [ ] Versão e data atualizadas
- [ ] Processo de adesão / ciência do(a) empregado(a) (assinatura digital ICP-Brasil ou eSocial — MP 2.200-2/2001; Lei 14.063/2020)
- [ ] Comunicação ao sindicato (se aplicável por CCT/ACT)
```

## O que esta skill NÃO faz

- Aprovar mudanças no manual. RH/DP + liderança jurídica aprova.
- Comunicar mudanças aos empregados.
- Rastrear adesões / ciência.
