> **Adaptação BR — não oficial.** Este plugin foi portado do `claude-for-legal` (jurisdição EUA) para o contexto trabalhista brasileiro (CLT, Reforma 13.467/2017, e-Social, FGTS, jurisprudência TST). É uma adaptação comunitária, sem vínculo institucional com Anthropic ou com os autores originais. Todo conteúdo gerado é **rascunho para revisão por advogado(a) inscrito(a) na OAB** — não é parecer jurídico, não substitui profissional habilitado(a).

# Plugin Trabalhista (Employment Legal)

Fluxos jurídicos trabalhistas para departamento jurídico interno: análise de admissão, revisão de rescisão, redação de políticas, atualização de manuais de conduta, perguntas sobre jornada e remuneração com sensibilidade à categoria. Construído em torno de um perfil de prática aprendido na primeira execução — o plugin conhece em quais estados/categorias você opera, quais convenções coletivas (CCT) e acordos coletivos (ACT) se aplicam, e o que diferencia cada um.

**Toda saída é um rascunho para revisão por advogado(a) — citado, sinalizado e com gates — não é conclusão jurídica.** O plugin faz o trabalho: lê documentos, aplica seu playbook, identifica os riscos, redige a peça. Um(a) advogado(a) revisa, verifica e decide. Citações são marcadas por fonte — você sabe quais vieram de uma ferramenta de pesquisa e quais precisam de checagem. Marcadores de sigilo são aplicados de forma conservadora para nada se perder por acidente. Ações de consequência — protocolar, enviar, executar — ficam atrás de confirmação explícita.

## Para quem é

| Papel | Fluxos principais |
|---|---|
| **Advogado(a) trabalhista in-house** | Revisão de rescisão, redação de políticas, análise de jornada e remuneração |
| **RH / Departamento Pessoal (DP)** | Revisão de admissão, dúvidas sobre manual, primeira linha de Q&A em jornada/remuneração |
| **General Counsel / Diretor(a) Jurídico(a)** | Receptor(a) de escalação para termos de alto risco e demissões coletivas |

## Primeira execução: cold-start

Pergunta em quais estados e países (caso haja expatriados ou subsidiárias estrangeiras) você tem empregados, lê seu manual interno e três rescisões recentes, e constrói uma tabela de escalação sensível à categoria sindical e à CCT/ACT aplicável.

```
/trabalhista:cold-start-interview
```

Sua configuração é armazenada em `~/.claude/plugins/config/claude-for-legal/trabalhista/CLAUDE.md` e sobrevive a atualizações do plugin.

## Pré-requisitos

- **Caminho de dados persistente.** O registro de licenças/afastamentos, os logs de apuração interna e os trackers de expansão são gravados em `~/.claude/plugins/config/claude-for-legal/trabalhista/`, caminho independente de versão que sobrevive a atualizações. Esses arquivos contêm dados pessoais e informações privilegiadas — garanta backup e controle de acesso (LGPD art. 46 e segs.).
- **Acesso a pesquisa jurídica.** As skills deste plugin intencionalmente não armazenam regras substantivas (pisos de CCT, enforcement de cláusula de não-concorrência, prazos de pagamento de verbas rescisórias, redação de TRCT/homologação, frameworks de país no caso de expatriados). Toda regra específica é pesquisada e citada no momento da revisão. Garanta que a sessão tenha acesso às ferramentas de pesquisa que você usa (busca web, integrações internas de pesquisa, repositórios de jurisprudência TST/TRT, base de CCT/ACT, ementários).
- **Escritório externo.** Nenhuma orientação específica de jurisdição estrangeira ou de categoria sindical pouco frequente é produzida sem engajamento de escritório especializado em qualquer caso fronteiriço ou nova categoria.

## Skills

| Skill | O que faz |
|---|---|
| `/trabalhista:cold-start-interview` | Entrevista inicial — aprende footprint (UFs/categorias) + regras de escalação a partir do manual + rescisões recentes |
| `/trabalhista:hiring-review` | Revisão de carta-proposta + cláusulas restritivas (não-concorrência, sigilo), checagem de categoria/CCT |
| `/trabalhista:termination-review` | Revisão de rescisão com detecção de bandeiras vermelhas (estabilidades, dispensa discriminatória, retorno de afastamento) |
| `/trabalhista:policy-drafting [tópico]` | Rascunha uma política com suplementos por CCT/ACT onde necessário |
| `/trabalhista:wage-hour-qa [pergunta]` | Q&A de jornada/remuneração ou trabalhista geral, sensível à categoria |
| `/trabalhista:worker-classification` | Classifica uma contratação proposta (CLT/PJ/terceirização/estágio/autônomo) e sinaliza risco de pejotização (CLT art. 9º; Súmula 331 TST) |
| `/trabalhista:expansion-kickoff [país]` | Inicia planejamento de expansão internacional para um novo país |
| `/trabalhista:expansion-update [país]` | Atualiza um tracker de expansão em andamento |
| `/trabalhista:investigation-open` | Abre uma nova apuração interna (assédio, fraude, ética) |
| `/trabalhista:investigation-add` | Adiciona documentos, notas de entrevista ou observações a uma apuração aberta |
| `/trabalhista:investigation-query` | Faz perguntas contra o log de uma apuração aberta |
| `/trabalhista:investigation-memo` | Rascunha ou atualiza o memorando privilegiado de apuração |
| `/trabalhista:investigation-summary` | Rascunha um resumo dirigido ao público específico a partir do memorando de apuração |
| `/trabalhista:leave-tracker` | Verifica afastamentos abertos para alertas de prazo e decisões necessárias |
| `/trabalhista:log-leave` | Adiciona um novo afastamento ao registro |
| `/trabalhista:matter-workspace` | Gerencia workspaces de matéria (apenas para escritórios com múltiplos clientes) — new, list, switch, close, none |
| **handbook-updates** | Faz diff de mudanças propostas contra o manual atual, sinaliza impacto em suplementos de CCT/ACT |

As skills de referência `internal-investigation` e `international-expansion` carregam os frameworks detalhados e templates — as skills por modo acima as carregam conforme necessário.

## Skills interativas vs. agentes agendados

As skills acima rodam quando você as invoca — para quando você está trabalhando uma matéria. Os agentes abaixo rodam em uma agenda — para o que se move enquanto você não está olhando:

| Agente | O que observa | Cadência padrão |
|---|---|---|
| **leave-tracker** | Afastamentos abertos com prazos legais rígidos — licença-maternidade (CLT art. 392), licença-paternidade (CF 7º XIX), auxílio-doença INSS (Lei 8.213/91 art. 60), estabilidade acidentária (Lei 8.213/91 art. 118), estabilidade gestante (ADCT art. 10 II b); dispara alertas de ponto de decisão antes de prazos serem perdidos | Semanal (segunda-feira) |

## Como aprende

Seu perfil de prática em `~/.claude/plugins/config/claude-for-legal/trabalhista/CLAUDE.md` não é estático — ele melhora conforme você usa o plugin. Skills te avisam quando uma saída usou um default que você deveria calibrar. Você pode rodar o setup de novo, editar o arquivo diretamente, ou dizer a uma skill para registrar uma nova posição.

## Notas

- **Sensibilidade à categoria sindical e à CCT/ACT é o ponto.** No Brasil não há "lei estadual trabalhista" — a competência é federal (CF art. 22, I). O que varia é a **convenção coletiva** (CCT, por categoria) e o **acordo coletivo** (ACT, por empresa) — pisos, jornada, adicionais, banco de horas, intervalos. O plugin sabe que metalúrgicos de SP têm CCT diferente de bancários do RJ.
- **Revisão de rescisão NÃO substitui a conversa com o RH/DP e o(a) gestor(a).** É um checklist que captura o que todo mundo esqueceu — homologação (quando aplicável), TRCT, prazo do art. 477 §6º (10 dias da rescisão), recolhimento de FGTS + multa 40%, baixa em CTPS/e-Social, comunicação de afastamento, exame demissional (NR-7).
- **Q&A de jornada/remuneração cita a regra mas sinaliza dúvidas para revisão humana.** Decisões de classificação (CLT vs. PJ vs. terceirização vs. estágio vs. autônomo) têm consequências graves — pejotização gera reconhecimento de vínculo (CLT art. 9º), passivo retroativo de 5 anos (CF 7º XXIX), contribuições previdenciárias e FGTS retroativos.
