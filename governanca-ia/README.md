# Plugin de Governança de IA

> Adaptação BR não oficial. Status legal: PL 2338/2023 (Marco Legal da IA) em tramitação. Calibrado para LGPD art. 20, Resoluções ANPD, Resolução CNJ 332/2020, Marco Civil, CDC, Lei 9.610/98 e princípios OCDE. Veja `references/terminology-us-to-br.md`.

Workflows de assessoria de governança de IA in-house: triagem de casos de uso, avaliações de impacto de IA, revisão de fornecedores de IA e análise de gaps regulação-vs-política. Construído em torno de um perfil de prática aprendido a partir da sua política de IA, uma avaliação de impacto de referência e os principais contratos de fornecedores de IA.

**Todo output é rascunho para revisão por advogado(a) habilitado(a) na OAB — citado, sinalizado e com gates — não é parecer jurídico.** O plugin faz o trabalho: lê os documentos, aplica o seu playbook, encontra os pontos críticos, redige a minuta. O(a) advogado(a) revisa, valida e decide. Citações são marcadas por fonte para você saber quais vieram de uma ferramenta de pesquisa e quais precisam ser checadas. Marcadores de sigilo/privilégio (EOAB art. 7º, XIX) são aplicados de forma conservadora para que nada seja exposto por acidente. Ações consequenciais — protocolar, enviar, executar — ficam atrás de confirmação explícita.

## Para quem é

| Papel | Workflows principais |
|---|---|
| **Encarregado(a) (DPO) / advogado(a) de governança de IA** | Avaliações de impacto, revisão de fornecedor de IA, análise de gap regulatório |
| **Product counsel / jurídico de produto** | Triagem de casos de uso, revisão de lançamento com componente de IA |
| **Diretor(a) Jurídico(a) / Legal ops** | Governança de política de IA, escalonamento, temas para conselho/diretoria |
| **Suprimentos / Jurídico** | Revisão contratual de fornecedor de IA |

## Primeira execução: a entrevista cold-start

O plugin entrevista você para aprender: você é construtor (provedor), implantador (deployer/operador) ou ambos — quais regulações de fato se aplicam — quais são as linhas vermelhas do seu caso de uso — e o que uma boa avaliação de impacto parece aqui. Depois, lê seus documentos-semente e aprende suas posições reais e o house style.

```
/governanca-ia:cold-start-interview
```

## Comandos

| Comando | Faz |
|---|---|
| `/governanca-ia:cold-start-interview` | Entrevista cold-start — escreve seu perfil de prática |
| `/governanca-ia:use-case-triage [caso de uso]` | Classifica um caso de uso contra seu registro (aprovado / condicional / proibido) |
| `/governanca-ia:aia-generation [caso de uso]` | Roda uma avaliação de impacto de IA (AIA) no seu house style |
| `/governanca-ia:vendor-ai-review [fornecedor/arquivo]` | Revisa contrato de fornecedor de IA contra suas posições |
| `/governanca-ia:reg-gap-analysis [regulação]` | Compara nova regulação ou orientação com política/prática atual |
| `/governanca-ia:policy-monitor` | Varredura semanal de drift na política de IA, ou consulta direta para uma prática nova proposta |
| `/governanca-ia:policy-starter` | Esboça uma política corporativa de uso de IA a partir de políticas-modelo publicadas, adaptada ao seu perfil de prática (rascunho para revisão por advogado(a)) |
| `/governanca-ia:matter-workspace` | Gerencia workspaces de matéria (apenas advocacia multicliente) — new, list, switch, close, none |

## Skills

| Skill | Propósito |
|---|---|
| **cold-start-interview** | Escreve `~/.claude/plugins/config/claude-for-legal/governanca-ia/CLAUDE.md` a partir da entrevista + documentos-semente |
| **use-case-triage** | Classifica casos de uso contra o registro; sinaliza avaliações ausentes |
| **aia-generation** | Avaliação de impacto de IA (AIA) no formato da casa; faz interface com RIPD (LGPD art. 38) quando há tratamento de dados pessoais |
| **vendor-ai-review** | Revisão de contrato de fornecedor de IA contra posições de governança |
| **reg-gap-analysis** | Nova regulação/orientação vs. estado atual, plano de remediação |
| **policy-monitor** | Varre outputs em busca de drift de prática; sugere atualizações de linguagem da política de IA |
| **policy-starter** | Produz primeiro rascunho de política de uso de IA a partir de fontes publicadas (Provimento CFOAB 205/2021 e 218/2023, CED-OAB, Resolução CNJ 332/2020, Estudo Preliminar ANPD sobre IA, EU AI Act como benchmark, princípios OCDE), adaptado ao seu perfil de prática — rascunho para revisão por advogado(a), não uma política final |
| **matter-workspace** | Cria, lista, troca e fecha workspaces de matéria para advocacias multicliente; isola cada cliente/caso para que o contexto não vaze entre eles |

## Quick start

### 1. Setup

```
/governanca-ia:cold-start-interview
```

Tenha em mãos (se existirem): sua política de IA ou de uso aceitável, uma avaliação de impacto anterior, contratos-chave de fornecedores de IA, inventário de modelos ou lista de ferramentas aprovadas.

Sua configuração fica em `~/.claude/plugins/config/claude-for-legal/governanca-ia/CLAUDE.md` e sobrevive a atualizações do plugin.

### 2. Triar um novo caso de uso

```
/governanca-ia:use-case-triage "Time de vendas quer usar IA para pontuar leads automaticamente"
```

Output: tier de risco, match no registro ou gap, condições exigidas, necessidade ou não de avaliação de impacto (e, se houver dados pessoais, sinalização de RIPD nos termos da LGPD art. 38 + Resoluções CD/ANPD).

### 3. Rodar uma avaliação de impacto

```
/governanca-ia:aia-generation "Triagem de currículos por IA para RH"
```

Perguntas de intake → avaliação de impacto no seu formato → check de consistência com política → condições de mitigação. Para decisões automatizadas que afetam titulares, atenta para o direito à revisão da LGPD art. 20.

### 4. Revisar um contrato de fornecedor de IA

```
/governanca-ia:vendor-ai-review openai-terms.pdf
```

Output: cláusula a cláusula vs. suas posições, redlines propostos, gaps para escalonar (incluindo responsabilidade civil — CC arts. 186, 927; CDC arts. 12 e 14 quando há relação de consumo).

## Triângulo de plugins: governança de IA ↔ product counsel ↔ privacidade

Estes três plugins foram desenhados para trabalhar juntos. Governança de IA é a terceira perna.

- **Product counsel** detecta quando um lançamento tem componente de IA → faz o handoff para
  `/governanca-ia:use-case-triage` e `/governanca-ia:aia-generation`
- **Privacidade** detecta quando um caso de uso de IA envolve dados pessoais → faz handoff para
  `/privacidade:pia-generation` (RIPD), se o plugin estiver instalado
- **Governança de IA** detecta quando uma avaliação de impacto levanta questões de proteção de dados →
  handoff para `/privacidade:pia-generation`, se o plugin estiver instalado

O handoff é explícito: cada plugin sinaliza quando o outro é necessário e diz qual pergunta deve ser respondida lá.

## Estrutura de arquivos

```
governanca-ia/
├── CLAUDE.md
├── README.md
└── skills/
    ├── cold-start-interview/
    ├── use-case-triage/
    ├── aia-generation/
    ├── vendor-ai-review/
    ├── reg-gap-analysis/
    ├── policy-monitor/
    ├── policy-starter/
    └── matter-workspace/
```

## Como aprende

Seu perfil de prática em `~/.claude/plugins/config/claude-for-legal/governanca-ia/CLAUDE.md` não é estático — ele melhora conforme você usa o plugin. As skills avisam quando um output usou um default que você deveria afinar. O agente `policy-monitor` observa drift entre sua política de governança de IA e sua prática real, e propõe atualizações. Você pode rodar o setup de novo, editar o arquivo direto ou pedir a uma skill que registre uma nova posição.

## Notas

- Check de gap (`reg-gap-analysis`) lida com regulações que chegam (PL 2338/2023; novas Resoluções CD/ANPD; atos do CNJ; deliberações setoriais BCB/CVM/ANS; eventuais Resoluções TSE em ano eleitoral). Policy monitor lida com drift interno de prática. Ferramentas diferentes para direções diferentes de mudança.
- Policy monitor exige uma pasta de outputs configurada (setada no setup) para a varredura funcionar. O modo de consulta direta funciona sem ela.
- Triagem de casos de uso é tão boa quanto o registro. Gaste a entrevista de setup acertando as linhas vermelhas — elas direcionam todo o resto.
- O formato de avaliação de impacto vem da sua avaliação-semente. Se você não forneceu uma no setup, ele usa uma estrutura baseline — refaça o setup com uma referência para melhorar. Quando houver tratamento de dados pessoais, a AIA se acopla ao RIPD (LGPD art. 38; Resoluções CD/ANPD).
- Obrigações de provedor (builder) e de operador/deployer são tratadas separadamente. Se você é os dois, as skills perguntam qual chapéu você está usando para cada tarefa. O PL 2338/2023 distingue agentes de IA de forma análoga ao EU AI Act, usado aqui como benchmark global.
- Análise de gap é manual (você aponta para uma regulação ou orientação). Para monitoramento automatizado, combine com o plugin `regulatorio`, se instalado.
- A seção `## Perfil da empresa` é, por convenção, o primeiro bloco de `~/.claude/plugins/config/claude-for-legal/governanca-ia/CLAUDE.md`. Se você roda outros plugins `-counsel`, pode copiar entre eles em vez de reentrar o mesmo contexto.
