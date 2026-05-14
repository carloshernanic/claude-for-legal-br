---
name: log-leave
description: >
  Adiciona um novo afastamento ao registro de afastamentos com a informação
  mínima necessária para começar a rastrear prazos. Use quando um(a)
  empregado(a) afasta-se e você quer que o tracker monitore CAT, encaminhamento
  ao INSS, estabilidade acidentária e retorno desde o primeiro dia.
argument-hint: "[descreva o afastamento — empregado(a)/função, tipo, UF, data de início]"
---

# /log-leave

Adiciona uma nova entrada ao `~/.claude/plugins/config/claude-for-legal/trabalhista/leave-register.yaml` com a informação
mínima necessária para começar a rastrear prazos. Use quando um(a)
empregado(a) afasta-se e você quer que o tracker monitore os relógios desde
o primeiro dia.

## Instruções

1. Leia `~/.claude/plugins/config/claude-for-legal/trabalhista/CLAUDE.md` → tabela de jurisdições/categorias e seção Sistemas.

2. Pergunte tudo o seguinte em um único bloco — não pingue pergunta por pergunta:

   > Perguntas rápidas para configurar o rastreamento do afastamento:
   >
   > - Nome ou função do(a) empregado(a) (anonimizar tudo bem)
   > - Onde trabalha? (UF — determina TRT competente e CCT/ACT aplicável)
   > - Categoria sindical / regime: CLT geral / doméstico (LC 150/2015) / rural (Lei 5.889/1973) / aprendiz / estagiário / temporário (Lei 6.019/1974) / bancário / outro
   > - Tipo de afastamento:
   >   * Auxílio-doença comum (B31) — Lei 8.213/91
   >   * Auxílio-doença acidentário (B91) — Lei 8.213/91 + CAT obrigatória (art. 22)
   >   * Licença-maternidade (CLT 392; 120 dias) ou Empresa Cidadã (Lei 11.770/2008; 180 dias)
   >   * Licença-paternidade (CF 7º XIX; 5 dias) ou Empresa Cidadã (20 dias)
   >   * Acidente do trabalho com CAT
   >   * Licença não-remunerada / sabático (se previsto em CCT/ACT)
   >   * Afastamento para mandato sindical / CIPA / Comissão de Conciliação Prévia
   >   * Outro (descrever)
   > - Data de início do afastamento
   > - Houve CAT emitida? Em qual data? (prazo 1 dia útil — Lei 8.213/91 art. 22)
   > - Atestado médico recebido pela empresa? Data?
   > - Encaminhamento ao INSS feito? (a partir do 16º dia — CLT art. 60 §3º)
   > - Data prevista de retorno (se conhecida — deixar em branco se não)
   > - Há estabilidade aplicável? (gestante / acidentária / dirigente sindical / CIPA / reabilitado(a) / pré-aposentadoria conforme CCT/ACT)

3. Usando a tabela em `~/.claude/plugins/config/claude-for-legal/trabalhista/CLAUDE.md`, identifique o regime aplicável
   (CLT geral, doméstico, rural, etc.) e a CCT/ACT vigente da categoria.

4. Compute o primeiro prazo relevante baseado na informação fornecida:
   - CAT não emitida e há indício de acidente → prazo: 1 dia útil (Lei 8.213/91 art. 22)
   - Pagamento dos 15 primeiros dias pela empresa (CLT 60 §3º) — vence no 15º dia
   - Encaminhamento ao INSS a partir do 16º dia
   - Empresa Cidadã (180 dias maternidade / 20 dias paternidade): requerimento no início do afastamento
   - Próximo prazo: alta médica + retorno + exame de retorno ao trabalho (NR-7 PCMSO)
   - Estabilidade acidentária: 12 meses após cessação do B91 (Lei 8.213/91 art. 118)

5. Grave uma nova entrada em `~/.claude/plugins/config/claude-for-legal/trabalhista/leave-register.yaml` usando o formato
   do registro descrito no agente leave-tracker. Se o arquivo não existir, crie.

6. Confirme em uma única linha:
   > "Registrado. [Empregado(a)/Função] — [Tipo de afastamento] — [UF/Categoria] — iniciado em [data].
   > Primeiro prazo: [o que é e quando]. O leave-tracker alertará automaticamente."

## Exemplos

```
/trabalhista:log-leave
```

```
/trabalhista:log-leave
Maria (Analista Sr., SP, comerciária CCT FECOMERCIO-SP) afastou-se hoje por
auxílio-doença comum. Atestado de 30 dias. CAT não aplicável.
```

```
/trabalhista:log-leave
João (Operador de máquina, MG, metalúrgico) sofreu acidente do trabalho
ontem com lesão em membro superior. CAT ainda não emitida.
```
