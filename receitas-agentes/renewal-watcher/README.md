> Adaptação BR não oficial. Calibrado para INPI (marcas decenais, patentes 20a, anuidades), Lei 9.609 (software), CCT/ACT (Súmula 277 TST), certidões RFB/FGTS/trabalhista, licenças ambientais, alvarás, ICP-Brasil. Veja `references/terminology-us-to-br.md`.

# Renewal Watcher — managed-agent template

## Visão geral

Varre o repositório contratual em busca de prazos próximos de renovação e de não-renovação (notice de denúncia/oposição à prorrogação), confronta com o playbook do time, sinaliza contratos com prazos críticos, desvios de playbook e gatilhos de escalonamento, e gera um relatório de alertas. Mesma origem do agent [`renewal-watcher`](../../comercial/agents/renewal-watcher.md) no Claude Code e da skill [`renewal-tracker`](../../comercial/skills/renewal-tracker) — este diretório é o cookbook de Managed Agent para `POST /v1/agents`.

Cobre tanto contratos comerciais (vigência, prorrogação tácita vs. expressa — CC arts. 472–480) quanto **registros e ativos com prazo**: marcas INPI (Lei 9.279/96 art. 133 — vigência decenal + 6 meses retardatário), patentes (PI 20 anos, MU 15 anos; anuidades a partir do 3º ano — art. 84), desenhos industriais (10 + 3×5 anos — art. 108), programas de computador (Lei 9.609/98 art. 2º §2º — 50 anos), domínios `.br` (NIC.br/Registro.br, renovação anual), certidões negativas (RFB, FGTS, trabalhista CNDT, criminal — validade 30–180 dias), alvarás municipais, licenças ambientais (LP/LI/LO — 4 a 10 anos), CCT/ACT (1–2 anos; ver Súmula 277 TST e atual entendimento STF — sem ultratividade automática), procurações (vencimento + revogação), apostille (Convenção da Haia), ata de eleição de diretoria (S.A. limite de 3 anos — registro de alteração na Junta Comercial) e certificados ICP-Brasil (e-CPF / e-CNPJ — 1 a 3 anos).

Este é um **cookbook, não um produto.** Assume Ironclad como CLM de referência porque é o que o plugin pareado assume; times em Agiloft, alternativas ao Ironclad, iManage, SharePoint ou pasta de PDFs assinados em Google Drive devem trocar o endpoint MCP correspondente.

## ⚠️ Antes de implantar

- **Datas-limite e termos de renovação extraídos de metadados do CLM podem estar errados.** Metadados de CLM divergem do documento assinado — aditivos firmados sem reingestão, datas de eficácia diferentes da assinatura, prorrogação tácita mal etiquetada. Antes de confiar em um prazo computado para uma decisão de denúncia ou renovação, **um(a) advogado(a) inscrito(a) na OAB confere contra o contrato assinado e seus aditivos**. Conteúdo gerado é **rascunho para revisão**, não parecer jurídico.
- **Para registros INPI**, datas críticas vêm do RPI (Revista da Propriedade Industrial) e do extrato no e-INPI; o cookbook não substitui a consulta direta ao sistema do INPI.
- **Roteamento de escalonamento segue a matriz configurada; não toma o juízo de mérito.** Um desvio de playbook sinalizado pode ser aceitável no contexto; um termo não sinalizado pode ainda exigir atenção. A matriz é roteador, não revisor.
- **Semanas calmas não são semanas limpas.** Um contrato (ou registro) que não apareceu pode estar ausente do CLM, mal etiquetado, ou já fora da janela de oposição sem o metadado refletir isso. O rodapé "tudo certo" significa que o agente rodou, não que nada precisa ser feito.

## Deploy

```bash
export ANTHROPIC_API_KEY=sk-ant-...
export IRONCLAD_MCP_URL=...
export GDRIVE_MCP_URL=...
# Opcional — habilite no manifesto se os acordos assinados ficarem aqui
export IMANAGE_MCP_URL=...
export DOCUSIGN_MCP_URL=...
../../scripts/deploy-managed-agent.sh renewal-watcher
```

## Eventos de steering

Veja [`steering-examples.json`](./steering-examples.json). A varredura padrão de segunda-feira usa o primeiro exemplo. Os outros dois cobrem rodadas ad-hoc por contraparte e checagens de desvio pós-assinatura.

## Segurança e handoffs

Texto contratual, mensagens de contrapartes e comentários de CLM são **entrada não-confiável.** Isolamento em três camadas:

| Camada | Toca documentos não-confiáveis? | Ferramentas | Conectores |
|---|---|---|---|
| **`repo-reader`** | **Sim** | `Read`, `Grep` apenas | ironclad, gdrive (somente leitura); imanage desligado por padrão |
| `deadline-calculator` / Orquestrador | Não | `Read`, `Grep`, `Glob`, `Agent` | Nenhum |
| **`alert-writer`** (titular de Write) | Não | `Read`, `Write`, `Edit` | Nenhum |

`repo-reader` devolve JSON com limite de tamanho e validado contra schema. `deadline-calculator` é pura computação sobre esse JSON mais a configuração de playbook em disco — sem MCP, sem web. `alert-writer` produz `./out/renewal-alerts-<YYYY-MM-DD>.md` e emite um `handoff_request` para entrega no Slack.

**Handoffs:** o orquestrador roteia o `handoff_request` do `alert-writer` para um worker de envio Slack usando o canal da configuração de House style do time que implantou. O agente nunca envia mensagens no Slack diretamente.

**Agents relacionados:** um `handoff_request` também pode rotear para [`deal-debrief`](../../comercial/agents/deal-debrief.md) quando for necessária uma checagem de desvio pós-assinatura, ou para [`playbook-monitor`](../../comercial/agents/playbook-monitor.md) quando desvios em renovação acumularem padrão. Agents nomeados nunca se chamam diretamente — roteamento é função do orquestrador.

**Não garantido:** este agente recomenda uma ação; **o(a) advogado(a) responsável decide** se denuncia, renegocia, deixa a prorrogação tácita correr, paga a anuidade do INPI no prazo ordinário ou retardatário, ou renova o registro de marca.

## Notas de adaptação

Antes de confiar na saída no seu fluxo:

- **Aponte para seu CLM.** `IRONCLAD_MCP_URL` é o padrão. Se os acordos assinados estiverem no iManage, vire `imanage` para `default_config: { enabled: true }` em `agent.yaml` e `subagents/repo-reader.yaml` e configure `IMANAGE_MCP_URL`. Se estiverem em pasta do Google Drive, use `gdrive` e o caminho de busca de fallback do repo-reader. Se estiverem em CLM sem MCP público (Agiloft, Conga), conecte um custom connector e atualize o bloco de servidor MCP.
- **Integre extratos do INPI e Registro.br.** Para registros (marca, patente, desenho industrial, software, domínio), o `repo-reader` deve ler extratos exportados do e-INPI ou do Registro.br colocados na pasta de entrada — o agente **não consulta o INPI ao vivo**. Confirme periodicidade da exportação com a equipe responsável.
- **Defina o canal do Slack.** O alert-writer emite um `handoff_request` que nomeia um canal Slack. O orquestrador lê esse canal do campo **House style → Alertas de renovação** na configuração de playbook. Configure antes da primeira rodada agendada, senão o handoff vai para dead-letter.
- **Calibre as janelas de antecedência por tipo de prazo.** As faixas padrão do deadline-calculator são vencido / 30 / 60 / 90 / 180 dias. Ajuste por categoria:
  - **Marca INPI:** alerta com 12 meses (janela ordinária do art. 133) e 18 meses (corte do retardatário).
  - **Patente INPI (anuidades):** alerta com 60 dias antes da retribuição ordinária (art. 84 §2º) e antes do término do prazo extraordinário com retribuição adicional.
  - **Domínio `.br`:** alerta com 60 e 30 dias.
  - **Certidões (RFB/FGTS/CNDT/criminal):** alerta com 15 dias antes do fim da validade.
  - **Licenças ambientais (LP/LI/LO):** alerta com 120 dias antes do vencimento (LO costuma exigir requerimento de renovação com 120 dias de antecedência — Res. CONAMA 237/97 art. 18 §4º).
  - **CCT/ACT:** alerta no início da data-base, com lembrete sobre Súmula 277 TST e atual entendimento STF.
  - **Procurações / ata de diretoria S.A. / e-CPF / e-CNPJ:** alerta com 60 e 30 dias.
- **Ajuste a matriz de escalonamento.** O deadline-calculator lê a matriz de escalonamento do seu playbook para decidir se marca `escalation_needed: true` e para quem rotear. Confirme se a matriz reflete sua alçada atual (quem assina deixar prorrogação correr, quem assina renegociação acima de teto financeiro, quem assina não-renovar marca/patente) antes de habilitar rodadas agendadas. A skill [`escalation-flagger`](../../comercial/skills/escalation-flagger) é carregada em `alert-writer` para formatação.
- **Confirme o cabeçalho de privilégio.** O append headless em `agent.yaml` instrui o agente a prefixar o cabeçalho de material de trabalho do seu playbook. Valide a redação com o(a) sócio(a) responsável / GC antes de ativar. Cabeçalho sugerido: `MATERIAL DE TRABALHO — PRIVILEGIADO E CONFIDENCIAL (Art. 7º, XIX, EOAB)`.
- **Idioma.** Todas as saídas em **pt-BR**. Documentos em idioma estrangeiro citados no relatório devem indicar se exigem **tradução juramentada** (Decreto 13.609/1943) ou **apostille** (Convenção da Haia) para uso/registro no Brasil.
- **Cadência.** Semanal é o padrão. Times de alto volume devem rodar diariamente; times pequenos podem rodar mensalmente. A cadência vive no seu próprio motor de workflow — o cookbook não se agenda sozinho.
