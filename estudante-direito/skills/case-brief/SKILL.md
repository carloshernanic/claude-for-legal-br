---
name: case-brief
description: >
  Resumo de julgado (acórdão/decisão STF/STJ/TST/TJs) no seu formato preferido.
  Em modo drill-me, faz o(a) estudante enunciar a tese/dispositivo antes de
  tudo. Use quando o(a) usuário(a) disser "resume [julgado]", "qual a tese em",
  "case brief", ou colar um acórdão.
argument-hint: "[nome do julgado ou citação, ou cole o acórdão]"
---

> **Nota BR.** Adaptação não oficial para o ordenamento brasileiro. Referências a tribunais (STF, STJ, TST, TJs) e à formatação de resumo seguem prática docente brasileira; ABNT NBR 6023/10520 para citações.

# /case-brief

1. Carregue `~/.claude/plugins/config/claude-for-legal/estudante-direito/CLAUDE.md` → preferências de esquematizado/resumo de julgado.
2. Aplique o workflow abaixo.
3. Resuma no formato do(a) estudante. Se modo drill-me: peça que o(a) estudante enuncie a tese/dispositivo primeiro.

---

## Propósito

Um resumo de acórdão é ferramenta para lembrar o que o julgado faz. Esta skill monta um no seu formato — o que você efetivamente vai usar no seu esquematizado.

## Disciplina de confiança

Resumos de julgado enunciam teses, ratio decidendi e fundamentos. Errar transforma seu esquematizado num mapa falso. A regra desta skill:

- **Se você colar o acórdão:** extraio tese/ratio/fundamentos do que está na minha frente. Confiante.
- **Se você dá só o nome ou número do julgado (RE, REsp, HC, AI):** resumo a partir do conhecimento. Vale bem menos. Sinalizo cada linha sobre a qual não tenho certeza com `[INCERTO: razão específica]`, e recomendo fortemente conferir contra o acórdão antes de pôr o resumo no esquematizado. Se eu não conheço o julgado o bastante, eu digo.
- **Se o julgado tem interpretações famosas-mas-controvertidas (ex.: ADI 4277/ADPF 132; HC 124.306; RE 605.708; Tema 985):** dou a leitura majoritária e `[VERIFICAR: cheque seu manual e a moldura do(a) professor(a)]`.

Um resumo construído na minha suposição e na sua boa-fé é pior que nenhum resumo. Melhor errar para "não tenho certeza — leia você" do que inventar.

## Carregar contexto

`~/.claude/plugins/config/claude-for-legal/estudante-direito/CLAUDE.md` → preferências (formato, profundidade), estilo de aprendizagem.

## Regra "não resuma por mim" (regra dura)

Um resumo que você não escreveu é um resumo que você não vai lembrar. Todo modo desta skill por padrão monta o andaime do resumo, não o resumo.

**O que esta skill faz em todos os modos:**
- Pergunta ao(à) estudante o que já tirou da leitura: fatos, controvérsia, tese como entendeu.
- Fornece o template em branco no formato preferido (campos para Fatos, Controvérsia, Tese/Ratio, Fundamentos, Norma extraída, Notas).
- Faz follow-ups pontuais onde a seção está magra: "Quais fatos o tribunal efetivamente usou na ratio?", "Qual a tese estrita vs. a questão mais ampla?", "Por que o tribunal rejeitou a divergência?"
- Se você colar o texto do acórdão, extraio literalmente a linguagem do tribunal para tese e fundamentos — isso não é escrever por você; é apontar o que o julgado diz.
- Sinalizo entendimentos confusos ou errados: "Você disse que a tese é X. A linguagem do tribunal é mais próxima de Y. Qual a norma que você vai levar para o esquematizado?"

**O que esta skill NÃO faz, mesmo se pedida:**
- Escrever um resumo completo só a partir do nome do julgado. Essa é exatamente a coisa que o(a) estudante está aprendendo a não precisar.
- "Resume esse julgado para mim" — recusado. O resumo é para lembrar, e isso exige escrever.

**Exceção** (a única): o(a) estudante explicitamente sobrepõe — "Já li três vezes, travei na redação da tese, me dá uma frase de partida para eu reescrever." Aí escrevo um starter mínimo com `[VERIFICAR]` e peço que reescreva nas próprias palavras antes de ir para o esquematizado.

## Bifurcação por modo

**Modo drill-me:** peça ao(à) estudante para enunciar a tese antes de qualquer coisa:
> "Você leu o acórdão. Qual é a tese? Uma frase."

Se não conseguir enunciar, mande ler de novo. O resumo é apoio de memória, não substituto da leitura. Depois siga o andaime — peça que enuncie fatos, controvérsia, fundamentos e norma, um a um. Rebata afirmações magras ou erradas.

**Modo me-explique:** mesmo workflow andaimado, tom mais brando. A skill conduz o(a) estudante por cada seção, oferece prompts estruturais ("uma boa tese tem uma frase: sim/não + a norma"), mas ainda espera o(a) estudante escrever o conteúdo. **Me-explique não significa "escreve o resumo por mim."** Significa "explica como um bom resumo se parece, e me guia ao escrever o meu."

Se o(a) estudante colar o texto do acórdão em qualquer um dos modos, a skill pode extrair a linguagem do tribunal para Fatos/Tese/Fundamentos — isso não é escrever por você; é apontar a fonte.

## O resumo — andaime, depois o(a) estudante preenche

A skill produz o **template com perguntas**, não o resumo preenchido. Estudante preenche cada seção; skill revisa, rebate, sugere o que falta.

Conforme o formato do(a) estudante em `~/.claude/plugins/config/claude-for-legal/estudante-direito/CLAUDE.md`. Se nenhum capturado, default:

```markdown
## [Nome do Julgado], [citação — ex.: STF, RE 605.708/SP, Pleno, Rel. Min. Dias Toffoli, j. 23.10.2019]

**Tribunal:** [Órgão julgador, ano]

**Fatos:** [Os fatos que importam para a tese. Não cada fato — os que o tribunal usou na ratio. Duas a quatro frases.]

**Histórico processual:** [Como chegou aqui? Sentença julgou X, é apelação/RE/REsp de quê? Uma frase.]

**Controvérsia:** [A questão que o tribunal decidiu. Forma de pergunta.]

**Tese / Dispositivo:** [A resposta. Uma frase. Sim/não + a norma. Se houver tese de repercussão geral ou tema repetitivo, cite o número — "Tema 985 STF", "Tema 1.046 STJ".]

**Fundamentos / Ratio decidendi:** [Por quê. A lógica do tribunal. É aqui que mora a norma. Três a cinco frases. Distinga do obiter dictum.]

**Norma extraída:** [A regra que você poria no esquematizado. O takeaway portátil.]

**Notas:** [Voto vencido (divergência) que vale conhecer? Distinguishing fático? Como o(a) professor(a) enfatizou? Súmula relacionada? Súmula Vinculante? Reflexo em jurisprudência consolidada do tribunal?]

---

**Checagem de citação.** A citação do acórdão, citações diretas e qualquer autoridade citada acima foram geradas por modelo de IA e não foram verificadas. Antes de confiar — em resumo, parecer, esquematizado ou prova — confira no site do tribunal (STF, STJ, TST, TJ correspondente), JusBrasil, Vade Mecum ou Planalto. Citações geradas por IA às vezes são fabricadas ou mal citadas (números de processo trocados, ementas confundidas).
```

## Calibração de profundidade

Conforme `~/.claude/plugins/config/claude-for-legal/estudante-direito/CLAUDE.md` — alguns(mas) estudantes querem resumo de uma linha (norma + citação), outros querem tratamento completo. Combine o formato.

Se está no 1º ou 2º ano aprendendo a ler acórdão: resumo mais completo. Se está no 5º ano em preparação OAB: só normas.

## O que esta skill não faz

- Resumir acórdão que o(a) estudante não leu. No modo drill-me, a checagem da tese impõe isso.
- Dizer o que cai na prova. Resuma tudo; a prova surpreende.
- **Resumir de memória sem sinalizar.** Se você só me dá o nome do julgado e eu resumo do que acho que sei, cada linha sobre a qual estou inseguro vira `[INCERTO]` ou `[VERIFICAR]`. Não ponha um resumo no esquematizado sem ter conferido contra o acórdão.
