# Plugin Privacy Counsel (Adaptação BR — LGPD)

> **Aviso — adaptação BR não oficial.** Este plugin é uma adaptação não oficial do `privacidade` original (framework EUA: GDPR/CCPA) para o contexto jurídico brasileiro (LGPD — Lei 13.709/2018). README.md e CLAUDE.md já foram portados; as skills internas em `skills/` ainda usam o framework GDPR original e estão em processo de adaptação. Consulte `references/terminology-us-to-br.md` para o mapeamento canônico EUA→BR. Todo resultado é **rascunho para revisão por advogado(a) inscrito(a) na OAB** — não é parecer jurídico.

Fluxos de trabalho de privacidade para departamento jurídico interno: revisão de contratos de tratamento (DPA/acordo controlador-operador), elaboração de respostas a requisições de titulares (LGPD art. 18), geração de RIPD (Relatório de Impacto à Proteção de Dados Pessoais — LGPD art. 38) e análise de gap entre nova norma e política/prática atuais. Construído em torno de um perfil de prática aprendido a partir da sua política de privacidade real, do seu template de contrato de tratamento e de um RIPD de referência.

**Toda saída é um rascunho para revisão por advogado(a) — com fontes citadas, pontos sinalizados e ações sensíveis bloqueadas — não é conclusão jurídica.** O plugin faz o trabalho: lê os documentos, aplica seu playbook, identifica os pontos, elabora a minuta. Um(a) advogado(a) revisa, verifica e decide. As citações são marcadas por fonte para você saber quais vieram de uma ferramenta de pesquisa e quais precisam ser conferidas. Marcadores de sigilo profissional são aplicados de forma conservadora para não comprometer proteções por acidente. Ações com consequência — protocolar, enviar, executar — exigem confirmação explícita.

## Para quem é

| Papel | Fluxos principais |
|---|---|
| **Advogado(a) de privacidade** | Revisão de contrato de tratamento, validação de RIPD, análise de gap regulatório |
| **Gestor(a) de programa de privacidade** | Atendimento a requisições de titulares, intake de RIPD, revisão de fornecedores |
| **Advogado(a) de produto** | Geração de RIPD para lançamentos |
| **Suporte / atendimento** | Primeira resposta a requisições de titulares (com escalonamento) |

## Primeira execução: entrevista de cold-start

O plugin entrevista você para aprender: você é controlador, operador ou ambos? Quais bases legais (LGPD art. 7º e art. 11) se aplicam ao seu tratamento? O que você aceita e o que rejeita em um contrato de tratamento? Em seguida, lê três documentos-semente — sua política de privacidade, seu template de contrato de tratamento, um RIPD com o qual você está satisfeito(a) — e aprende suas posições reais e seu estilo de redação.

Sua configuração é gravada em `~/.claude/plugins/config/claude-for-legal/privacidade/CLAUDE.md` e sobrevive a atualizações do plugin.

```
/privacidade:cold-start-interview
```

## Comandos

| Comando | Função |
|---|---|
| `/privacidade:cold-start-interview` | Entrevista de cold-start |
| `/privacidade:use-case-triage [atividade]` | Esta atividade precisa de RIPD? Classificação rápida + condições |
| `/privacidade:dpa-review [arquivo]` | Revisa contrato de tratamento contra seu playbook (detecta direção automaticamente) |
| `/privacidade:dsar-response` | Conduz uma requisição de titular e elabora a resposta |
| `/privacidade:pia-generation [funcionalidade]` | Gera um RIPD no seu estilo |
| `/privacidade:reg-gap-analysis [norma]` | Faz diff entre nova norma e política/prática atuais |
| `/privacidade:policy-monitor` | Varredura semanal de desvio de política, ou consulta direta sobre nova prática proposta |
| `/privacidade:matter-workspace` | Gerencia workspaces por caso (apenas advocacia privada multi-cliente) — new, list, switch, close, none |

## Skills

| Skill | Propósito |
|---|---|
| **cold-start-interview** | Escreve o CLAUDE.md a partir da entrevista + documentos-semente |
| **use-case-triage** | Esta atividade precisa de RIPD? Pode seguir? Checagem de conflito com política + handoffs |
| **dpa-review** | Revisão termo a termo bidirecional (na posição de controlador ou de operador) |
| **dsar-response** | Verificação de identidade → mapeamento de sistemas → exceções → minuta de resposta |
| **pia-generation** | RIPD no formato da casa, com checagem de consistência com a política |
| **reg-gap-analysis** | Nova norma vs. estado atual, plano de remediação |
| **policy-monitor** | Varre as saídas em busca de desvio de prática; elabora atualizações de política |
| **matter-workspace** | Cria, lista, alterna e encerra workspaces por caso em prática multi-cliente; isola cada cliente/caso para evitar vazamento de contexto |

> **Status BR das skills:** os identificadores de comando ficam em inglês (são chaves técnicas), mas o conteúdo interno das skills ainda reflete GDPR/CCPA. A adaptação para LGPD por skill é trabalho em andamento.

## Início rápido

### 1. Setup

```
/privacidade:cold-start-interview
```

Tenha em mãos: URL da sua política de privacidade pública, seu contrato de tratamento padrão, um RIPD de referência.

### 2. Triagem de nova funcionalidade ou atividade de tratamento

```
/privacidade:use-case-triage "Marketing quer usar dados comportamentais para personalização de anúncios"
```

Saída: PROSSEGUIR / RIPD NECESSÁRIO / RIPD OBRIGATÓRIO / PARAR — com tabela de condições, pergunta sobre base legal (LGPD art. 7º ou art. 11) e oferta de iniciar o RIPD na mesma conversa.

### 3. Revisão de contrato de tratamento de um cliente

```
/privacidade:dpa-review contrato-tratamento-cliente.pdf
```

Saída: direção detectada automaticamente, revisão termo a termo contra o playbook, redlines propostos, checagem de consistência com a política.

### 4. Atendimento a uma requisição de titular

```
/privacidade:dsar-response
```

Conduz você por: classificar → verificar identidade → localizar dados → exceções → minuta. Usa sua lista de sistemas a partir do CLAUDE.md de configuração.

### 5. RIPD para nova funcionalidade

```
/privacidade:pia-generation "Compartilhamento de localização"
```

Perguntas de intake → RIPD no formato da casa → diff contra a política → lista de condições.

## Como o plugin aprende

Seu perfil de prática em `~/.claude/plugins/config/claude-for-legal/privacidade/CLAUDE.md` não é estático — ele evolui conforme você usa o plugin. As skills avisam quando uma saída usou um padrão que você deveria refinar. A skill `policy-monitor` observa desvios entre sua política e sua prática e propõe atualizações. Você pode rodar o setup de novo, editar o arquivo diretamente ou pedir a uma skill para registrar uma nova posição.

## Estrutura de arquivos

```
privacidade/
├── .claude-plugin/plugin.json
├── .mcp.json
├── CLAUDE.md
├── README.md
├── skills/
│   ├── cold-start-interview/
│   ├── use-case-triage/
│   ├── dpa-review/
│   ├── dsar-response/
│   ├── pia-generation/
│   ├── reg-gap-analysis/
│   ├── policy-monitor/
│   └── matter-workspace/
└── hooks/hooks.json
```

## Notas

- A revisão de contrato de tratamento é bidirecional: a mesma skill trata contratos quando você é operador (defender flexibilidade operacional) e quando você é controlador (proteger os dados). Direção é detectada automaticamente, ou pergunta-se.
- O formato do RIPD vem do seu RIPD-semente. Se você não forneceu um durante o setup, o plugin usa uma estrutura genérica — rode o setup de novo com um RIPD de referência para corrigir.
- Análise de gap (`reg-gap-analysis`) lida com normas que chegam de fora (nova Resolução CD/ANPD, novo entendimento ANPD, alteração legislativa). O policy-monitor lida com desvio interno de prática. Ferramentas diferentes para direções diferentes de mudança.
- O policy-monitor exige uma pasta de saídas configurada (no setup) para a varredura funcionar. O modo de consulta direta funciona sem ela.
- **Equivalente funcional vs. GDPR/CCPA:** alguns conceitos do plugin original não têm análogo direto na LGPD (ex.: distinção CCPA "sale of personal information" não existe; "right to opt out of sale" idem). Onde aparecer, a skill sinaliza "equivalente funcional" ou "não há análogo direto no Brasil — abordagem adotada: …".
