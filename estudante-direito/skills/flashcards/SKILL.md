---
name: flashcards
description: >
  Gera ou drilla flashcards para memorização de letra-de-lei — baldes
  Leitner, armazenamento markdown por disciplina, modo drill com
  auto-avaliação. Use quando o(a) usuário(a) disser "drilla flashcards",
  "faz cards a partir de", "me testa em cards", ou quer memorizar normas.
argument-hint: "[disciplina] [--generate | --drill | --review | --stats | --session <n>]"
---

> **Nota BR.** Adaptação não oficial. Calibrado para Vade Mecum (CF/88, CC/02, CPC/15, CP/40, CPP/41, CLT/43, CDC/90, LGPD, leis especiais) + súmulas (STF, STJ, TST) + temas de repercussão geral / repetitivos.

# /flashcards

1. Carregue `~/.claude/plugins/config/claude-for-legal/estudante-direito/CLAUDE.md` → disciplinas atuais, disciplinas fracas, locais dos esquematizados.
2. Aplique o framework abaixo.
3. Roteie por flag:
   - `--generate`: monta cards a partir de fonte (caminho do esquematizado, anotações, manual) segundo regras. Escreve em `~/.claude/plugins/config/claude-for-legal/estudante-direito/flashcards/[disciplina]/cards.md`.
   - `--drill` (default): prioriza cards vencidos + novos; mostra Q, espera resposta, mostra A, recebe auto-avaliação, atualiza baldes + próxima revisão.
   - `--review`: navega o deck por balde.
   - `--stats`: snapshot de progresso; sinaliza cards travados para drill verbal.
   - `--session <n>`: sessão focada de N cards, priorizada por erros prévios + cards vencidos; anexa resultados a `study-plan.yaml` → `session_history`.
4. Aplique disciplina de confiança: sinalize cada card gerado sem fonte com `[VERIFICAR]`.

---

## Checagem de matéria real

Se a pergunta do(a) estudante parece sobre situação REAL — o aluguel dele, a multa, o negócio da família, a prisão de um amigo, valor real em reais, prazo real, nome real de parte — pare.

> "Isso parece situação real, não hipótese de estudo. Eu não posso dar orientação jurídica, e você também não pode — você ainda não é advogado(a) inscrito(a) na OAB (Lei 8.906/94). Se é real, [a pessoa] precisa de advogado(a) de verdade: Defensoria Pública, NPJ da faculdade, Comissão de Assistência Judiciária da seccional OAB local, ou (se houver recurso) advogado(a) privado(a). Posso te ajudar a entender os conceitos jurídicos gerais, mas isso é estudo, não orientação."

Atenção a: nomes reais, endereços reais, datas reais, valores específicos em reais, "meu locador/chefe/pai/amigo", "recebi multa/citação/notificação", prazos em dias. Qualquer um é gatilho.

## Propósito

Esquematizados são para síntese; flashcards são para memorização. A OAB e a maior parte das provas de faculdade premiam recall rápido de norma. Esta skill gera cards a partir do seu esquematizado (ou anotações ou trechos de manual), drilla com espaçamento leve, e rastreia o que está travado e o que ainda não.

**Não é sistema SRS completo.** Baldes Leitner simples. Bom o bastante para estudar, leve o bastante para manter. Se você quer Anki, use Anki; isto é para quando está no chat e quer drill rápido.

## Disciplina de confiança

Mesma regra das outras skills geradoras:

- Se gerando cards a partir de fonte que você fornece (esquematizado, anotações, trecho de manual ou Vade Mecum), o Q/A do card vem dessa fonte. Confiante.
- Se gerando cards do meu conhecimento sem fonte, sinalizo cada card que enuncia norma sobre a qual não estou totalmente confiante com `[VERIFICAR: norma — confirmar contra fonte]`. Cheque antes de fixar o card como alvo de aprendizagem.
- Se não conheço bem uma área, gero menos cards em vez de inventar. Melhor 8 cards bons que 20 onde 5 estão errados.

## Carregar contexto

- `~/.claude/plugins/config/claude-for-legal/estudante-direito/CLAUDE.md` → disciplinas atuais, disciplinas fracas, esquematizados existentes
- `~/.claude/plugins/config/claude-for-legal/estudante-direito/flashcards/[disciplina]/cards.md` se existir (build incremental)
- Fonte fornecida (caminho do esquematizado, anotações, trecho de manual ou Vade Mecum) se houver

## Modos

Flag: `--generate | --drill | --review | --stats | --session <n>` (default: pergunta)

### `--session <n>` — sessão focada de N cards

Para quando o(a) estudante diz "vamos 5 cards de Contratos" ou roda `/estudante-direito:session Contratos 5 --flashcards`.

- Carregue `~/.claude/plugins/config/claude-for-legal/estudante-direito/study-plan.yaml` se existir e leia `session_history` desta disciplina.
- Priorize: cards previamente marcados errados > cards vencidos > cards novos.
- Rode N cards um por vez no fluxo `--drill`.
- Ao fim, anexe resultados a `study-plan.yaml` → `session_history`:

```yaml
session_history:
  - date: 2026-05-08
    subject: Contratos
    type: flashcards
    n_cards: 5
    right: 3
    partial: 1
    wrong: 1
    stuck_topics: [vicios-redibitorios-CC-441]
```

- Se não há `study-plan.yaml`, escreva em `~/.claude/plugins/config/claude-for-legal/estudante-direito/session-history.yaml`.

### `--generate` — criar cards

**Entradas:**
- Disciplina (nome da matéria ou tema)
- Fonte (caminho do esquematizado, anotações, ou "use meu esquematizado existente de ~/.claude/plugins/config/claude-for-legal/estudante-direito/CLAUDE.md")
- Opcional: meta de quantidade (default 10-20 por sessão)

**Estrutura do card:**

```markdown
### Card [N]
**Q:** [pergunta — um conceito, um card]
**A:** [resposta — a norma, uma ou duas frases]
**Fonte:** [seção do esquematizado, página do manual, artigo do Vade Mecum, data da anotação de aula]
**Balde:** novo
**Última revisão:** —
**Próxima revisão:** [data de hoje]
**Notas:** [opcional — distinções, exceções, armadilhas, súmula relacionada]
```

**Regras de redação:**
1. **Um conceito por card.** "Elementos da responsabilidade civil" vira 4 cards (conduta, nexo, dano, culpa/risco), não 1.
2. **Frente é pergunta, não tópico.** "Dever de cuidado" ruim. "Quais os elementos da responsabilidade civil subjetiva (CC 186, 927)?" bom.
3. **Verso é norma, não parágrafo.** Se a resposta precisa de parágrafo, divida em vários cards.
4. **Cite a fonte** (artigo do Vade Mecum, súmula, manual) para reconferir durante o drill.

**Checagem de citação.** Quando cards são gerados do meu conhecimento em vez de fonte colada, a norma e qualquer julgado/lei citado no verso foram gerados por modelo de IA e não foram verificados. Antes de memorizar, confirme contra seu esquematizado, manual (Tepedino, Tartuce, Gonçalves, Marinoni, Didier), Vade Mecum, ou Planalto. Um card errado drillado até a mestria é pior que nenhum card.

### `--drill` — sessão de estudo

**Priorização:**
1. Cards onde `next_review <= hoje` E balde != dominado
2. Cards novos ainda não tentados
3. Se nenhum vencido e nenhum novo: pergunte se quer revisão de cards dominados (prevenção de decaimento)

**Fluxo do drill por card:**
1. Mostre Q. Espere resposta.
2. Usuário(a) responde (ou digita "pula" / "não sei")
3. Mostre A.
4. Auto-avaliação: `certo` / `parcial` / `errado` / `não sei`
5. Atualize balde + próxima revisão pela tabela:

| Auto-avaliação | Mudança de balde | Próxima revisão |
|---|---|---|
| certo | sobe um (novo → aprendendo → revisão → dominado) | +1d novo, +3d aprendendo, +7d revisão, +21d dominado |
| parcial | mesmo balde | +1d |
| errado | desce um (revisão → aprendendo; aprendendo → novo; novo fica) | hoje +4h |
| não sei | desce um | hoje +4h |

### `--review` — navegar o deck

Mostra todos os cards de uma disciplina. Agrupados por balde. Útil para escanear o que está no deck e ajustar conteúdo manualmente.

### `--stats` — snapshot de progresso

Por disciplina: total de cards, distribuição por balde, vencidos hoje, revisados na semana. Destaque cards que voltaram a `novo` mais de duas vezes — esses são os conceitos travados que valem drill verbal via `/estudante-direito:socratic-drill`.

## Integração com outras skills

- **outline-builder:** depois de montar ou estender esquematizado, ofereça gerar flashcards do novo material
- **socratic-drill:** se um card errou 2+ vezes, encaminhe para `/estudante-direito:socratic-drill` para drill verbal — flashcards não bastam para conceito que você não entende
- **bar-prep-questions:** disciplinas da OAB com stats fracas de flashcard pesam mais no drill 1ª fase

## Armazenamento

```
flashcards/
└── [disciplina]/
    └── cards.md
```

Um arquivo por disciplina. Cards em markdown. Metadados de balde/revisão inline por card. Não ótimo para decks muito grandes (>500) mas bom para o tamanho típico de deck de faculdade.

## O que esta skill não faz

- **Substituir Anki.** Se você já tem hábito de flashcard, mantenha. Isto é para quando está no chat e quer drillar sem trocar de app.
- **Inventar cards para bater meta.** Se eu só consigo gerar 8 cards confiantes da sua fonte, você recebe 8. Encher com chutes pesados de `[VERIFICAR]` é pior que deck menor.
- **Disciplinar seu estudo.** Dias de revisão perdidos compostam; a skill só mostra o que vence. Você decide se drilla.
- **Te ensinar a norma.** Cards são para drillar o que você já estudou. Se um card está consistentemente errado, o problema é a montante — use `/estudante-direito:socratic-drill` ou releia a fonte.
