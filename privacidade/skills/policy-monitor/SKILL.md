---
name: policy-monitor
description: >
  Mantém a política de privacidade alinhada com a prática. Dois modos:
  varredura semanal de RIPDs, revisões de contrato de tratamento e triagens
  salvos para achar drift; ou consulta direta para uma nova prática proposta.
  Use quando o(a) usuário(a) pergunta "nossa política cobre isto", "queremos
  começar a fazer X — precisa atualizar a política", "rode o monitor",
  "varredura de política", ou quer achar onde a política não bate mais com o
  que o time de fato faz.
argument-hint: "[descreva uma nova prática proposta — ou omita / use --sweep para modo crawl]"
---

# /policy-monitor

> **Adaptação BR.** Esta skill foi adaptada para o regime da **LGPD (Lei 13.709/2018)**. PIA/DPIA → **RIPD** (art. 38); DPA → **contrato de tratamento** (acordo controlador-operador); supervisory authority → **ANPD**. "Attorney work product" → sigilo profissional do(a) advogado(a) (**EOAB art. 7º XIX**). Avisos setoriais: BCB/CMN para financeiro; ANS para saúde suplementar; CFM para sigilo médico; ECA art. 100 + LGPD art. 14 para crianças/adolescentes; Marco Civil da Internet (Lei 12.965/2014). Sempre cite primárias BR.

**Modo varredura** (sem argumento ou `--sweep`):
1. Leia `~/.claude/plugins/config/claude-for-legal/privacidade/CLAUDE.md` → caminho da pasta de saídas, documento de política, data da última varredura.
2. Rode o workflow. Escaneie a pasta de saídas para arquivos posteriores à última varredura.
3. Para cada saída: extraia práticas aprovadas → diff contra compromissos atuais da política.
4. Classifique gaps: NECESSÁRIO (política representa mal a prática atual) vs ACONSELHÁVEL (política silente).
5. Para cada gap: cite trecho atual, descreva o gap, minute linguagem sugerida.
6. Atualize **Última varredura de política** no CLAUDE.md.

**Modo consulta direta** (com argumento descritivo):
1. Leia `~/.claude/plugins/config/claude-for-legal/privacidade/CLAUDE.md` → compromissos atuais + documento da política.
2. Parseie a prática proposta. Diff contra a política: categorias de dados, finalidades, terceiros, retenção, direitos do(a) titular, transparência.
3. Saída: coberto / faltando / em conflito + linguagem sugerida + recomendação de timing.

**Agenda:** Configure lembrete recorrente no seu scheduler (calendário, gestor de tarefas, CI) para rodar `/privacidade:policy-monitor` semanalmente. Execução agendada exige integração de tarefas agendadas, que não é parte deste plugin.

```
/privacidade:policy-monitor
/privacidade:policy-monitor "Queremos começar a usar dados comportamentais para personalizar e-mails de onboarding"
```

---

# Monitor de Política de Privacidade (LGPD)

## Propósito

Políticas de privacidade driftam em uma direção: a prática avança, a política fica para trás. Um RIPD aprovou nova categoria. Um contrato foi assinado com suboperador não listado. Uma triagem marcou novo caso de uso como condicional com requisito de transparência que a política ainda não faz. Meses depois, alguém lê e a política não reflete o que acontece.

Esta skill pega o drift antes de virar problema — varrendo a pasta de saídas semanalmente, ou respondendo direto à pergunta: "vamos começar a fazer X, o que isso significa para a política?"

A saída é sempre a mesma: eis o gap, eis a linguagem sugerida.

---

## Carregue o estado atual

Leia `~/.claude/plugins/config/claude-for-legal/privacidade/CLAUDE.md`:
- `## Quem somos` → footprint regulatório (LGPD; setoriais BR: BCB/CMN, ANS, CFM, ANATEL, CVM; ECA + LGPD art. 14 para crianças; CDC para relações de consumo; Marco Civil da Internet; GDPR/CCPA se extraterritorial)
- `## Compromissos da política de privacidade` — compromissos extraídos da política publicada
- `## Saídas` — caminho da pasta, local do documento, data da última varredura

Se `## Saídas` tem `[PLACEHOLDER]`:
> "Saídas não configuradas. Posso ainda rodar checagem em consulta direta — descreva o que pretende e eu diferencio da política atual. Para habilitar varredura, rode `/privacidade:cold-start-interview` e forneça caminho da pasta."

Leia o documento real da política do caminho em `## Saídas` → **Documento de política de privacidade**. Os compromissos do CLAUDE.md são resumo; o documento real é autoridade para sugerir edições.

### Compromissos de privacidade vivem em várias superfícies — varra todas

A política do site é uma superfície. Programas modernos fazem compromissos vinculantes em pelo menos quatro outros lugares que a ANPD e órgãos de proteção do consumidor escrutinam:

1. **Banners de consentimento de cookies / CMPs.** A plataforma de consentimento promete categorias e finalidades específicas. Se a política diz "usamos cookies de analytics" e o CMP só oferece "estritamente necessários", há conflito. A ANPD publicou guia sobre cookies (2022); Procons e Senacon também monitoram.
2. **Selos de privacidade de app store.** Apple App Privacy (a "etiqueta nutricional") e Google Data Safety são autodeclarados — e regulatoriamente escrutináveis no BR também (Senacon, Procons via CDC). Empresa que atualiza política mas não atualiza o selo tem inconsistência material visível ao regulador. Cheque: quando o selo foi atualizado pela última vez? Bate com categorias, finalidades e compartilhamento da política atual?
3. **Fluxos de consentimento no produto.** As telas onde titulares fazem escolhas (consentimentos de onboarding, toggles em configurações, diálogos "atualizamos nossa política"). A política diz o que se faz; o fluxo diz com o que o(a) titular concordou. Têm que bater. Lembre que o **consentimento LGPD (art. 5º XII)** exige manifestação livre, informada e inequívoca — banner genérico não basta.
4. **Avisos setoriais.** BCB Resolução 4.658/2018 e Open Finance para financeiro; ANS para saúde suplementar; ECA art. 100 + LGPD art. 14 para crianças/adolescentes; CFM Resolução 1.821/2007 para registros médicos. Detalhes abaixo em "Avisos setoriais."

**Acrescente campos no perfil para localização e data de cada superfície.** A varredura checa cada uma contra a política atual e sinaliza divergência: "Política atualizada [data]. Selo da App Store atualizado [data anterior] — pode não refletir nova categoria. CMP configurado [data] — verificar finalidades de cookies contra a política."

Política limpa + selo desatualizado é reclamação no Procon esperando para acontecer. Varra superfícies, não só o documento.

### Avisos setoriais estão no escopo

A política do site é um aviso. Práticas setoriais reguladas exigem aviso separado e específico que o site não substitui. Se `## Quem somos` → footprint inclui qualquer dos itens abaixo, a varredura diffa prática contra esse aviso além da política do site — ou sinaliza ausência se não configurado:

| Item do footprint | Aviso setorial a diffar | O que sinalizar |
|---|---|---|
| **BCB / Open Finance** (instituição financeira tratando dados financeiros) | Política de segurança cibernética (Res. BCB 4.658/2018), termos de Open Finance, comunicações específicas a clientes | Saídas que sugerem nova categoria de dado financeiro, compartilhamento com não-coligados, ou mudanças nos opt-outs que o aviso não reflete. Contrato assinado com fornecedor de analytics recebendo dado financeiro sem update no aviso é gap. |
| **ANS** (operadora de plano de saúde, hospital, prestador) | Termos de plano e comunicação a beneficiários sob RN ANS aplicável | Saídas que sugerem novo uso ou compartilhamento de dados de saúde, novas categorias, ou mudanças nos direitos do(a) beneficiário(a). Lembre dados de saúde são sensíveis (LGPD art. 5º II + art. 11). |
| **CFM** (estabelecimento de saúde) | Política de prontuário (Resolução CFM 1.821/2007) e termos do(a) paciente | Saídas implicando compartilhamento de prontuário, novas categorias, mudanças em consentimento informado. |
| **ECA art. 100 + LGPD art. 14** (operador de serviço dirigido a crianças/adolescentes <12 / <18) | Aviso aos pais/responsáveis + termos do produto | Saídas implicando nova coleta de crianças/adolescentes, novo compartilhamento, mudança no fluxo de consentimento parental. **LGPD art. 14 §1º** exige consentimento específico e em destaque de pelo menos um dos pais ou responsável. |
| **Marco Civil da Internet (Lei 12.965/2014)** | Termos de uso e política de privacidade da aplicação de Internet | Mudanças em retenção de registros (arts. 13 e 15), guarda de registros de aplicação, compartilhamento com autoridades. |
| **CVM / mercado de capitais** | Avisos específicos a investidores, RCVM 35/44, fatos relevantes | Tratamento de dados de investidores e mudanças relevantes. |
| **CDC + Senacon** (relação de consumo) | Termos do(a) consumidor(a), políticas de devolução/cancelamento | Compromissos publicitários sobre tratamento de dados (CDC arts. 30–35). |

**Se nenhum aviso setorial estiver configurado para um regime do footprint**, sinalize como gap permanente em cada varredura, não achado único. A saída deve incluir:

> **Cobertura de aviso setorial:**
> - [regime]: [caminho do aviso + data de atualização, ou "NÃO CONFIGURADO — sinalizar a cada varredura até resolver"]

**Se a varredura não localiza o aviso setorial**, diga explicitamente — não diffe só com a política do site silenciosamente. Um(a) Encarregado(a) de fintech confiando em varredura que ignorou BCB sairia com aviso desatualizado sem alerta. Sinalize alto.

**Pergunte se o footprint está ambíguo.** Se `## Regulatório` diz "LGPD" mas a varredura encontra categorias de dado financeiro, dados de saúde ou dados de crianças, traga à tona antes de prosseguir: "Seu footprint não lista [BCB / ANS / ECA+art. 14] mas esta varredura está olhando saídas que envolvem [categoria]. Deve adicionar esse regime ao footprint, e há aviso setorial para diffar?"

---

## Detecção de modo

**Modo varredura:** Sem argumento, `--sweep`, ou disparado por agenda.
→ Escanear a pasta de saídas. Diffar todas as saídas desde a última varredura contra a política atual.

**Modo consulta direta:** Usuário(a) fornece descrição de prática proposta.
→ Diffar a prática contra a política. Sugerir updates.

---

## Modo 1: Varredura

### Determine o escopo

Leia `## Saídas` → **Última varredura de política**. Escaneie arquivos na pasta com data posterior. Se não há data, escaneie tudo e note: "Primeira varredura — escaneando todas as saídas."

Se a pasta está vazia ou sem arquivos novos:
> "Sem novas saídas desde [última varredura]. Política parece atual com a prática recente. Próxima varredura agendada: [data]."

Atualize **Última varredura de política** no CLAUDE.md para a data de hoje após completar.

### O que ler em cada tipo de saída

**RIPDs (Relatórios de Impacto):**
- Extrair: categorias de dados tratadas, finalidades, terceiros / suboperadores envolvidos, prazos de retenção, implicações em direitos do(a) titular, condições no tratamento
- Sinalizar: qualquer coisa nessa lista não presente nos compromissos atuais da política

**Revisões de contrato de tratamento (assinados ou aprovados):**
- Extrair: suboperadores adicionados, localizações de dados acordadas (transferência internacional — Res. CD/ANPD 19/2024), finalidades cobertas, obrigações criadas pelo contrato
- Sinalizar: suboperadores não listados (se a política nomeia), novas categorias, novas localizações, obrigações inconsistentes

**Triagens (RIPD NECESSÁRIO / PROSSEGUIR):**
- Extrair: o que foi aprovado, condições impostas que implicam compromisso público (ex.: "transparência a titulares afetados antes do lançamento")
- Sinalizar: práticas aprovadas não cobertas pela política, condições que exigem linguagem nova

**Respostas a requisição de titular:**
- Extrair: novas categorias surgidas que não estavam em respostas anteriores, novos sistemas
- Sinalizar: categorias coletadas mas não declaradas na política

### Identificação de gap

Para cada item sinalizado, avalie:

**Update NECESSÁRIO** — a política faz compromisso que esta saída contradiz, ou o tratamento ocorre e a política não cobre. Não atualizar cria representação enganosa material — risco sob LGPD art. 6º VI (transparência) e CDC arts. 30 e 36.

> Exemplo: Política diz "coletamos nome, e-mail e dados de pagamento." Um RIPD aprovou coleta de geolocalização. Política não menciona. É NECESSÁRIO — você coleta dado não declarado.

**Update ACONSELHÁVEL** — a política é silente mas não conflitante. O tratamento é defensável sem update, mas mais limpo com ele.

> Exemplo: Política diz "podemos compartilhar dados com prestadores de serviço." Contrato foi assinado com novo fornecedor de analytics. Política não nomeia mas não exclui. Aconselhável adicionar à lista de suboperadores se mantida.

### Formato de saída de varredura

```markdown
[CABEÇALHO DE MATERIAL DE TRABALHO — por config ## Saídas — varia por papel; ver `## Quem está usando`]

# Monitor de Política de Privacidade — Relatório de Varredura

**Data:** [data]
**Saídas escaneadas:** [N arquivos] | **Novas desde a última varredura:** [N arquivos]
**Gaps encontrados:** [N] NECESSÁRIOS | [N] ACONSELHÁVEIS

---

## Updates NECESSÁRIOS

### [Gap 1 — nome curto]

**Fonte:** [arquivo / tipo de saída que disparou]
**O que está acontecendo:** [descrição em português claro]
**Política atual:** [trecho — ou "Sem cobertura"]
**Gap:** [o que falta ou está inconsistente]
**Risco regulatório:** [LGPD art. ___; setorial; CDC ___]

**Linguagem sugerida:**
> *Adicionar à [seção]:*
> "[Texto minutado — específico, consistente com o estilo da política]"

---

[repetir para cada gap NECESSÁRIO]

---

## Updates ACONSELHÁVEIS

### [Nome do gap]

**Fonte:** [arquivo]
**O que está acontecendo:** [descrição]
**Política atual:** [trecho ou "Silente"]
**Linguagem sugerida:**
> *Adicionar a / atualizar [seção]:*
> "[Texto minutado]"

---

## Sem ação necessária

[Lista de saídas escaneadas onde não houve gap — confirma revisão]

---

## Próximos passos

- [ ] Revisar NECESSÁRIOS — cada um precisa decisão antes da funcionalidade/tratamento entrar no ar (ou imediatamente se já estiver)
- [ ] Revisar ACONSELHÁVEIS — urgência menor mas vale endereçar no próximo refresh
- [ ] Próxima varredura: [data]
```

---

## Modo 2: Consulta direta

### Parseie a prática proposta

Extraia da descrição:
- Que dado é coletado/tratado?
- Qual a finalidade?
- Quem mais está envolvido (fornecedores, parceiros, terceiros)?
- Quem são os(as) titulares?
- Há decisão automatizada (LGPD art. 20)?
- Há novo dever de transparência aos(às) titulares?
- Há transferência internacional (LGPD arts. 33-36; Res. CD/ANPD 19/2024)?

Se a descrição estiver vaga, faça uma pergunta esclarecedora. Não rode intake longo — este modo deve ser rápido.

### Diff de política

Cheque a prática proposta contra cada seção relevante da política:

| Checagem | Política diz | Prática proposta | Veredicto |
|---|---|---|---|
| Categorias de dados | [o que a política lista] | [nova categoria se houver] | 🟢 Coberto / 🟡 Gap / 🔴 Conflito |
| Finalidades | [declaradas] | [nova finalidade] | |
| Bases legais (LGPD art. 7º/11) | [bases declaradas] | [base implicada] | |
| Terceiros / suboperadores | [partes declaradas] | [nova parte se houver] | |
| Retenção (LGPD art. 15-16) | [compromisso] | [retenção implicada] | |
| Direitos do(a) titular (art. 18) | [ofertados] | [novas implicações] | |
| Transferência internacional | [o que diz] | [há transferência?] | |
| Transparência / aviso | [o que diz a respeito] | [o que exige] | |

### Formato de saída — consulta direta

```markdown
[CABEÇALHO DE MATERIAL DE TRABALHO — por config ## Saídas — varia por papel]

# Checagem de Política: [prática proposta em uma linha]

**Linha de fundo:** [UPDATE NECESSÁRIO / ACONSELHÁVEL / SEM UPDATE]

---

## O que está coberto

[Lista de aspectos da prática proposta já tratados pela política — breve, confirma que não mudam]

## O que falta

### [Gap 1]

**Política atual:** [trecho ou "Silente"]
**O que é preciso:** [por que importa — jurídico (LGPD art. ___), reputacional, consistência]

**Linguagem sugerida:**
> *Adicionar à [seção]:*
> "[Texto minutado]"

### [Gap 2]
[mesmo formato]

## O que conflita

### [Conflito 1 — se houver]

**Política diz:** [trecho]
**Prática proposta faz:** [o que conflita]
**Resolução:** [qual lado muda e por quê — geralmente a prática ajusta para a política, ou a política é atualizada para nova posição defensável]

---

## Timing

[Se algum gap é NECESSÁRIO: "Update de política antes de entrar no ar."
Se ACONSELHÁVEL: "Pode prosseguir; atualizar no próximo refresh."]
```

---

## Padrões de qualidade da linguagem sugerida

Linguagem de política deve:
- Casar com voz e estilo da política existente (leia o documento real, não só o resumo no CLAUDE.md, antes de minutar)
- Ser específica o suficiente para ser significativa mas não tão específica que mudanças rotineiras quebrem ("prestadores de serviço que nos auxiliam a operar nosso negócio" envelhece melhor do que nomear cada fornecedor)
- Não fazer compromisso que o time não consegue manter (ex.: não minute "nunca compartilharemos dados de localização" se a arquitetura manda esse dado a fornecedor de analytics)
- Sinalizar quando uma mudança de posição mais ampla pode ser necessária, não só adição de frase
- Quando for caso de **base legal**, indicar qual (LGPD art. 7º ou art. 11) — fundamental para conformidade documentada

Ao minutar, sempre diga em qual seção adicionar. Se a seção certa não existe, diga e sugira criar.

---

## Integração com agenda

Configure lembrete recorrente no seu scheduler (calendário, gestor de tarefas, CI) para rodar `/privacidade:policy-monitor` semanalmente. Execução agendada exige integração de tarefas agendadas, que não está embutida.

A varredura, quando rodada, atualiza `## Saídas` → **Última varredura de política** no CLAUDE.md, para a próxima só olhar o que é novo.

---

## Encerre com a árvore de decisão de próximos passos

Termine com a árvore conforme `## Saídas` no CLAUDE.md. Customize as opções ao que esta skill acabou de produzir — os cinco ramos default (minutar o X, escalar, buscar mais fatos, aguardar, outra coisa) são ponto de partida. A árvore É a saída; o(a) advogado(a) escolhe.

Se a varredura achou mais de ~10 findings, ou sempre que pedido: ofereça dashboard (ver CLAUDE.md `## Saídas → Oferta de dashboard para saídas data-heavy`). Desenhe a oferta para esta saída — contagens por superfície (cláusula de política / RIPD / contrato / triagem), contagens por severidade, e grid ordenável com artefato fonte e remediação recomendada.

## O que esta skill não faz

- Não atualiza a política em si — minuta linguagem sugerida e sinaliza decisões, mas humano(a) revisa e aprova cada mudança.
- Não pega mudanças regulatórias — isso é `reg-gap-analysis`. Esta skill monitora drift de prática interna, não mudanças externas de lei.
- Não força que saídas sejam salvas — se o time não salva RIPDs na pasta configurada, a varredura não acha. Modo consulta direta funciona sem salvos.
- Não lê e-mail ou Slack para decisões informais — só saídas estruturadas salvas na pasta.
