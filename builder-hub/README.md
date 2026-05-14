# Plugin Legal Builder Hub

> Adaptação BR não oficial. Hub de construção de skills jurídicas calibrado para ecossistema BR (Vade Mecum, JusBrasil, Lexml, PJe, INPI etc.). Skills devem respeitar LGPD, Provimentos OAB (205/2021 publicidade, 188/2018 mediação) e CED. Veja `references/terminology-us-to-br.md`.

Descoberta e instalação de skills jurídicas comunitárias. Navega em registries do GitHub (lpm-skills, [registries adicionais — adicione via /builder-hub:registry-browser], entre outros), instala e atualiza automaticamente, e expõe skills comunitárias relacionadas dentro dos seus outros plugins jurídicos. A entrevista de cold-start É o recomendador do starter pack — pergunta seu tipo de prática e recomenda o que instalar.

**Toda skill comunitária é exibida em formato bruto antes da instalação, escaneada por padrões de prompt-injection e avaliada contra o Legal Skill Design Framework. O plugin ajuda você a achar e avaliar; você decide em quem confiar.**

## Para quem é

Todo mundo que usa os outros plugins jurídicos. Esta é a app store.

## Primeira execução: cold-start

Pergunta seu tipo de prática (advocacia contenciosa, consultivo, in-house, legal ops, NPJ etc.), setor, tamanho do time, conforto com ferramentas. Recomenda um starter pack de skills comunitárias que combinam. Instala as que você escolher.

```
/builder-hub:cold-start-interview
```

Sua configuração é armazenada em `~/.claude/plugins/config/claude-for-legal/builder-hub/CLAUDE.md` e sobrevive a atualizações do plugin.

## Postura de segurança

Skills comunitárias instaladas rodam com seu acesso a dados de clientes, pastas de processos/matérias e o playbook do seu time. O hub trata toda instalação e toda atualização como uma decisão de confiança. Quatro camadas de defesa, nenhuma suficiente isoladamente:

- **Allowlist (controlada pelo admin):** `~/.claude/plugins/config/claude-for-legal/builder-hub/allowlist.yaml` declara quais registries, publicadores e conectores MCP as skills comunitárias podem usar. O modo `permissive` (padrão) alerta sobre qualquer coisa fora da lista; o modo `restrictive` (recomendado para deployments de escritório/corporativo) recusa. A allowlist é checada antes do instalador ler qualquer conteúdo de terceiros. Veja `skills/skill-installer/references/allowlist.md` para o schema.
- **Código-fonte bruto, não resumo:** o instalador mostra o `SKILL.md` bruto integral — não um resumo gerado por IA — antes que qualquer coisa seja escrita. Resumo é conveniência; uma skill que faz algo duvidoso precisa fazê-lo em texto que o display bruto vai mostrar.
- **Varreduras heurísticas:** tanto o instalador quanto o `skills-qa` escaneiam a skill por padrões de prompt-injection (override/alegações de autoridade, leituras e escritas fora de escopo, URLs externas, unicode oculto, execução de shell, pedido de credenciais). São varreduras heurísticas de IA, explicitamente rotuladas como tal — uma varredura limpa não é uma auditoria de segurança, é um lembrete para você ler o texto.
- **Aprovação humana, toda vez:** nada é escrito em disco sem um `sim` digitado novo. Aprovação não é inferida de mensagens anteriores. Para defesa em profundidade, o instalador recomenda rodar o fetch/análise em um subagente read-only para que capacidades de Write só fiquem disponíveis depois da aprovação.

Atualizações usam a mesma postura: o auto-updater fixa commit SHAs (não tags mutáveis), mostra o diff completo incluindo hooks e mudanças MCP, e exige aprovação explícita por update. Não há modo de auto-aplicação.

Se uma skill der errado após instalada: `/builder-hub:disable [skill]` a silencia sem remover arquivos; `/builder-hub:uninstall [skill]` remove tudo. Ambos são restritos a skills comunitárias instaladas via hub — recusam tocar em skills first-party.

## Pré-requisitos

- Notificações Slack do agente registry-sync exigem um servidor Slack MCP configurado no seu ambiente. Sem ele, o agente escreve seu digest em arquivo.
- A lista padrão de registries em `~/.claude/plugins/config/claude-for-legal/builder-hub/CLAUDE.md` chega vazia, exceto por `lpm-skills`. Adicione registries de sua confiança via `/builder-hub:registry-browser` ou editando `~/.claude/plugins/config/claude-for-legal/builder-hub/CLAUDE.md`.

## Comandos

| Comando | O que faz |
|---|---|
| `/builder-hub:cold-start-interview` | Perfil de prática + recomendação de starter pack |
| `/builder-hub:registry-browser [query]` | Pesquisa skills nos registries monitorados |
| `/builder-hub:skill-installer [skill]` | Instala uma skill comunitária |
| `/builder-hub:auto-updater` | Verifica atualizações para skills instaladas |
| `/builder-hub:related-skills-surfacer` | Sugere skills com base no que você vem fazendo |
| `/builder-hub:skills-qa [skill]` | Avalia uma skill contra o Legal Skill Design Framework antes de instalar |
| `/builder-hub:disable [skill]` | Desabilita uma skill comunitária instalada sem remover arquivos |
| `/builder-hub:uninstall [skill]` | Desinstala uma skill comunitária instalada pelo hub |

## Skills

| Skill | Propósito |
|---|---|
| **cold-start-interview** | Perfil de prática → starter pack |
| **registry-browser** | Pesquisa em registries monitorados |
| **skill-installer** | Allowlist-gate, fetch, exibe SKILL.md bruto, trust-check, QA, instala skills comunitárias |
| **uninstall** | Desinstala skill comunitária instalada via hub (skills first-party são off-limits) |
| **disable** | Desabilita uma skill comunitária sem remover seus arquivos; reabilite depois |
| **skill-manager** | Referência: workflows detalhados de uninstall/disable/re-enable usados pelas skills `uninstall` e `disable` |
| **skills-qa** | Avalia uma skill contra o Legal Skill Design Framework — design, modos de falha, superfície de confiança e varredura heurística de prompt-injection |
| **auto-updater** | Verifica updates; mostra diff e revisão de confiança; aplica apenas com aprovação explícita |
| **related-skills-surfacer** | Surge com skills comunitárias relacionadas após uma tarefa (direto ou via hook) |

## Comandos interativos vs. agentes agendados

Os comandos acima rodam quando você invoca — para quando você está trabalhando numa matéria/caso. Os agentes abaixo rodam em schedule — para o que se movimenta enquanto você não está olhando:

| Agente | O que monitora | Cadência padrão |
|---|---|---|
| **registry-sync** | Registries monitorados por skills novas e atualizadas; posta notificações conforme suas preferências de update | Semanal |

## Registries monitorados (padrão)

A allowlist padrão chega com os registries comunitários que revisamos pré-configurados. Edite `references/allowlist-default.yaml` no repositório, ou sua allowlist por-instalação em `~/.claude/plugins/config/claude-for-legal/builder-hub/allowlist.yaml`, para adicionar, remover ou alternar entre modos restritivo e permissivo.

- **lpm-skills** — Gestão de projetos jurídicos (Scott Margetts / LegalOps Consulting) — `github.com/legalopsconsulting/lpm-skills`
- **Lawvable / awesome-legal-skills** — Lista curada de skills de agentes IA para trabalho jurídico — `github.com/lawvable/awesome-legal-skills`
- **Lawvable / agent-skills** — Coleção curada de skills de agentes para trabalho jurídico — `github.com/lawvable/agent-skills`
- Adicione os seus via `/builder-hub:registry-browser` ou editando a allowlist

## Ecossistema BR — fontes e integrações comuns

Skills brasileiras tipicamente integram ou referenciam:

- **Fontes primárias e legislação:** Planalto (planalto.gov.br), **Lexml** (lexml.gov.br), **DOU** (in.gov.br), portais oficiais de **STF, STJ, TST, TSE, CNJ**, diários oficiais estaduais e municipais.
- **Vade Mecum** (digital ou impresso) como referência consolidada.
- **Pesquisa jurisprudencial:** sites oficiais (STF, STJ, TST, TJs, TRFs, TRTs), **JusBrasil**, **Jusbrasil API**, repositórios de precedentes qualificados (Temas Repetitivos STJ, Repercussão Geral STF, IRDR, IAC).
- **Tribunais eletrônicos:** **PJe** (CNJ — federal, trabalhista e parte da Justiça Estadual), **e-SAJ** (Softplan — TJSP, TJSC, TJMS, TJAC, TJAL etc.), **e-Proc** (TRFs e parte do TST), **Projudi** (alguns TJs). Acesso via certificado digital ICP-Brasil; scraping ou APIs limitadas; consultas processuais públicas via DataJud (CNJ).
- **INPI** para marcas, patentes, desenhos industriais, programa de computador (Lei 9.279/1996 e Lei 9.609/1998).
- **Receita Federal** (Sintegra/CNPJ), **Junta Comercial** (registro societário), **CVM** (mercado de capitais), **BCB** (operações cambiais e RDE-IED), **ANPD** (LGPD), **CADE** (concorrência), **COAF** (PLD/FT).
- **Ferramentas de legaltech BR** (ecossistema de integração): **Linte**, **NetLex**, **Doc9**, **Lawit**, **Contraktor**, **Tikal Tech**, **EasyJur**, **Astrea**, **JusBrasil**, **Aurum**, **ADVBOX**.
- **Assinatura eletrônica:** **ICP-Brasil** (MP 2.200-2/2001) — qualificada; **Lei 14.063/2020** — assinatura simples/avançada/qualificada para interações com governo; **gov.br Assina** (assinatura avançada gratuita); **e-Notariado** (Provimento CNJ 100/2020).
- **Associações e comunidades:** **ABRADEP**, **ALAP-BR** (Legal Ops), comunidade **Legal Brasil**, **AB2L** (Associação Brasileira de Lawtechs e Legaltechs).

## Conformidade que toda skill deve respeitar

- **LGPD** (Lei 13.709/2018): skills que tratam dados pessoais precisam definir base legal (art. 7º para comuns; art. 11 para sensíveis), implementar minimização, registrar operações e respeitar direitos do titular (art. 18). Atendimentos a requisições de titular, RIPD (art. 38) e comunicação de incidente à ANPD (art. 48; Resolução CD/ANPD 15/2024) são obrigações que skills podem ajudar a operacionalizar, nunca substituir.
- **OAB Provimento 205/2021** (publicidade do(a) advogado(a)) e **Provimento 188/2018** (mediação e conciliação): skills que geram conteúdo de marketing, posts, peças publicitárias, materiais para redes sociais — ou minutas envolvendo mediação/conciliação — devem sinalizar essas restrições. **CED-OAB** (Código de Ética e Disciplina) governa a conduta profissional integral.
- **Resolução CNJ 332/2020** e **Resolução CNJ 615/2025** (uso de IA no Judiciário) e **PL 2338/2023** (Marco Regulatório da IA, aprovado no Senado em 2024) são contexto regulatório obrigatório para skills que operam no Judiciário ou produzem peças.
- **Acessibilidade:** **LBI** (Lei 13.146/2015) e padrão **eMAG** (Modelo de Acessibilidade em Governo Eletrônico) para skills que produzem materiais publicáveis (sites, PDFs, vídeos).
- **Assinatura eletrônica:** skills que geram fluxos de assinatura devem identificar o regime aplicável (ICP-Brasil x avançada x simples) e a destinação (privada x ato perante o governo, conforme Lei 14.063/2020).
- **Compliance e anticorrupção:** **Lei 12.846/2013** (Lei Anticorrupção) + **Decreto 11.129/2022** (programa de integridade); **Lei 8.429/1992** alterada pela **Lei 14.230/2021** (Improbidade); **Lei 12.683/2012** + **COAF** (PLD/FT).

Variações por unidade federativa importam — **legislações estaduais e municipais** (ex.: alíquotas e regulamentos de ICMS por estado, ISS por município, leis estaduais de meio ambiente, regulamentos do Procon estadual) e **jurisprudência de TJs/TRFs/TRTs** podem mudar o resultado. Skills devem reconhecer o federalismo (CF art. 22 — matéria civil, processual, penal, trabalhista é federal; tributário e ambiental têm competência concorrente) e nunca inventar "lei estadual" onde a competência é da União.

## Como o hub aprende

Seu perfil de prática em `~/.claude/plugins/config/claude-for-legal/builder-hub/CLAUDE.md` não é estático — melhora à medida que você usa o plugin. O hub o relê em todo `/builder-hub:registry-browser` e `/builder-hub:related-skills-surfacer`, então ajustar tipo de prática, setor ou registries monitorados afina recomendações futuras. Edite o arquivo direto ou rerode `/builder-hub:cold-start-interview --redo` quando seu trabalho mudar.

## Notas

- Skills comunitárias são lidas antes da instalação. Você vê o SKILL.md **bruto** — não um resumo — antes de aceitar.
- Auto-update vem desligado. Ligue por-skill se você confia na fonte.
- O related-skills-surfacer roda dentro de outros plugins: enquanto você executa uma tarefa, ele checa se a comunidade tem algo relevante.
- Deployments corporativos / de escritórios: defina `mode: restrictive` em `allowlist.yaml` e popule as listas `registries`, `publishers` e `connectors`. Em modo restritivo o instalador recusa fetch, análise ou instalação a partir de qualquer fonte não listada.
