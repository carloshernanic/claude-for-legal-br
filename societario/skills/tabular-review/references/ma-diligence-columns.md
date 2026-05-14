# Diligência M&A — Conjunto Padrão de Colunas

> **Adaptação BR não oficial.** Calibrado para Lei 6.404/1976, Código Civil, Lei de Arbitragem 9.307/1996, CADE (Lei 12.529/2011), LGPD, INPI, Lei Anticorrupção 12.846/2013. Veja `references/terminology-us-to-br.md` na raiz do plugin.

Schema default para revisão de contratos do target em deal buy-side. Comece daqui, depois adicione ou corte colunas conforme o deal. Ponto de partida, não checklist — as reps do SPA e a request list é que dirigem o que importa de verdade.

```yaml
schema:
  name: "Diligência M&A — Padrão BR"
  columns:
    - id: contraparte
      label: "Contraparte"
      type: verbatim
      prompt: "Nome da parte contratante além do target, exatamente como aparece (inclua tipo societário: Ltda./S.A./EIRELI/SLU/pessoa física)."

    - id: cnpj_contraparte
      label: "CNPJ/CPF da Contraparte"
      type: verbatim
      prompt: "CNPJ ou CPF da contraparte se aparecer no instrumento."

    - id: tipo_contrato
      label: "Tipo de Contrato"
      type: classify
      options: [msa, pedido_compra, licenca_inbound, licenca_outbound, locacao, prestacao_servicos, fornecimento, distribuicao, representacao_comercial, nda, joint_venture, mutuo, fianca, garantia_real, contrato_trabalho, parceria, comodato, franquia, outro]
      prompt: "Que tipo de contrato é este?"

    - id: data_base
      label: "Data-base"
      type: date
      prompt: "Quando o contrato entrou em vigor?"

    - id: prazo
      label: "Prazo"
      type: duration
      prompt: "Qual o prazo inicial?"

    - id: renovacao_automatica
      label: "Renovação Automática"
      type: classify
      options: [nenhuma, anual, periodo_fixo, prazo_indeterminado]
      prompt: "O contrato se renova automaticamente? Em que ciclo?"

    - id: resilicao_unilateral
      label: "Resilição Unilateral (denúncia vazia)"
      type: classify
      options: [nenhuma, qualquer_parte, apenas_target, apenas_contraparte]
      prompt: "Alguma parte pode resilir sem motivo (denúncia vazia)? Quem? Atenção a contratos cativos com prazo certo — CC art. 473 par. único exige prazo razoável para denúncia em contratos de execução continuada com investimento relevante."

    - id: prazo_aviso_previo
      label: "Aviso Prévio de Resilição"
      type: duration
      prompt: "Que antecedência mínima é exigida para resilir?"

    - id: mudanca_controle
      label: "Mudança de Controle"
      type: classify
      options: [omisso, anuencia_necessaria, anuencia_nao_pode_ser_negada_sem_motivo, rescisao_automatica, apenas_notificacao, direito_de_rescisao_da_contraparte, tag_along_contratual]
      prompt: "O contrato trata mudança de controle do target? Que gatilho e qual consequência? Para S.A. aberta, lembrar art. 254-A Lei 6.404/76 (tag-along legal de 80% do preço pago ao controlador em alienação de controle)."

    - id: cessao
      label: "Cessão de Posição Contratual"
      type: classify
      options: [omissa, anuencia_necessaria, anuencia_nao_pode_ser_negada_sem_motivo, livremente_cessivel, cessivel_a_partes_relacionadas, intransferivel]
      prompt: "O target pode ceder este contrato? Que restrições se aplicam? (Cessão de crédito — CC arts. 286-298; assunção de dívida — arts. 299-303; cessão de contrato bilateral exige anuência da contraparte, salvo cláusula em contrário.)"

    - id: exclusividade
      label: "Exclusividade / Não-Concorrência"
      type: classify
      options: [nenhuma, fornecedor_exclusivo, cliente_exclusivo, nao_concorrencia, nao_solicitacao, restricao_territorial, cliente_mais_favorecido]
      prompt: "O contrato restringe alguma parte de competir ou contratar com terceiros? Atenção a cláusulas que possam configurar conduta anticompetitiva (Lei 12.529/2011 art. 36) e a restrições pós-contratuais — devem ter prazo, território e atividade definidos, sob pena de invalidade (CC art. 122 + jurisprudência)."

    - id: limite_responsabilidade
      label: "Limite de Responsabilidade"
      type: currency
      prompt: "Há cap de responsabilidade? Valor ou múltiplo? Lembrar que CDC veda limitação em relação de consumo (art. 51, I); cláusulas limitativas entre empresários são em regra válidas (CC art. 421-A — autonomia paritária)."

    - id: indenizacao
      label: "Indenização"
      type: classify
      options: [nenhuma, mutua, target_indeniza, contraparte_indeniza, apenas_PI, apenas_reclamacao_de_terceiros, apenas_violacao_LGPD]
      prompt: "Quem indeniza quem, e por quê?"

    - id: forca_maior_caso_fortuito
      label: "Força Maior / Caso Fortuito / Onerosidade Excessiva"
      type: classify
      options: [omisso, definicao_padrao, definicao_ampliada, exclui_eventos_economicos, gatilho_de_renegociacao, suspende_obrigacoes, permite_rescisao]
      prompt: "Como o contrato trata força maior e caso fortuito (CC arts. 393, 478-480)? Inclui pandemia/eventos econômicos? Permite revisão por onerosidade excessiva (CC art. 478) ou só rescisão?"

    - id: lei_aplicavel
      label: "Lei Aplicável"
      type: verbatim
      prompt: "Qual lei rege o contrato? Cite exatamente. Se contraparte ou objeto for estrangeiro, atenção: LINDB art. 9º — obrigações são regidas pela lei do país em que se constituírem; partes brasileiras podem eleger lei estrangeira em arbitragem internacional (Lei 9.307/96 art. 2º §1º)."

    - id: foro_solucao_conflitos
      label: "Foro / Solução de Conflitos"
      type: classify
      options: [justica_estadual, justica_federal, justica_trabalho, arbitragem_camarb, arbitragem_camccbc, arbitragem_camb3, arbitragem_amcham, arbitragem_iccbr, arbitragem_outra, arbitragem_ad_hoc, mediacao_previa_obrigatoria, dispute_board, omisso]
      prompt: "Como conflitos são resolvidos? Cite a câmara arbitral, se houver (CAM-CCBC, CAM-B3, CAMARB, AMCHAM, ICC-Brasil, etc.). Lei de Arbitragem 9.307/1996; mediação Lei 13.140/2015. Atenção a cláusulas escalonadas (mediação → arbitragem)."

    - id: sede_arbitragem
      label: "Sede da Arbitragem"
      type: verbatim
      prompt: "Cidade-sede da arbitragem, se houver cláusula compromissória. (Sede determina lei processual da arbitragem.)"

    - id: cliente_mais_favorecido
      label: "Cláusula de Cliente Mais Favorecido / Proteção de Preço"
      type: classify
      options: [nenhuma, preco_mais_favorecido, equiparacao_de_preco, direito_de_benchmarking]
      prompt: "Há cláusula de most-favored-customer ou proteção de preço?"

    - id: compromissos_minimos
      label: "Compromisso Mínimo de Compra / Volume"
      type: currency
      prompt: "Há compromisso mínimo de compra, volume ou spend?"

    - id: titularidade_pi
      label: "Titularidade de Propriedade Intelectual"
      type: classify
      options: [cada_um_mantem_o_seu, target_titular_de_obra_sob_encomenda, contraparte_titular_de_obra_sob_encomenda, cotitularidade, apenas_licenca, omisso]
      prompt: "Quem é titular da PI criada ou usada sob o contrato? Atenção a obras sob encomenda — Lei 9.610/98 (direitos autorais) e Lei 9.609/98 (software): cessão deve ser expressa e por escrito. Para invenções de empregados, Lei 9.279/96 arts. 88-93 (LPI). Para marcas, cessão de titularidade exige averbação no INPI."

    - id: licencas_open_source
      label: "Open Source / Software de Terceiros"
      type: classify
      options: [omisso, proibido, permitido_com_restricoes, declaracao_de_uso_anexa, copyleft_proibido]
      prompt: "O contrato trata uso de open source ou componentes de terceiros?"

    - id: lgpd_dados_pessoais
      label: "LGPD / Dados Pessoais"
      type: classify
      options: [omisso, papeis_definidos_controlador_operador, clausulas_lgpd_padrao, anexo_de_protecao_de_dados, transferencia_internacional_regulada, dpa_separado, dpa_pendente]
      prompt: "O contrato trata tratamento de dados pessoais? Define papéis (controlador/operador — Lei 13.709/2018 arts. 5º VI-VII)? Há DPA anexo? Há cláusula sobre transferência internacional (LGPD arts. 33-36)?"

    - id: confidencialidade_sobrevida
      label: "Sobrevida da Confidencialidade"
      type: duration
      prompt: "Por quanto tempo as obrigações de confidencialidade sobrevivem à rescisão?"

    - id: seguro_exigido
      label: "Seguros Exigidos"
      type: classify
      options: [nenhum, responsabilidade_civil_geral, responsabilidade_civil_profissional, cyber, riscos_de_engenharia, riscos_operacionais, seguro_garantia, fianca_bancaria, guarda_chuva]
      prompt: "Que seguros devem ser mantidos? Há exigência de garantia (fiança bancária, seguro-garantia)?"

    - id: direitos_de_auditoria
      label: "Direitos de Auditoria"
      type: classify
      options: [nenhum, contraparte_audita_target, target_audita_contraparte, mutuo]
      prompt: "Alguma parte tem direito de auditoria?"

    - id: anticorrupcao_compliance
      label: "Anticorrupção / Compliance"
      type: classify
      options: [omisso, clausula_lei_12846_padrao, clausula_FCPA_UKBA_tambem, programa_integridade_exigido, treinamento_anual_exigido, direito_de_auditoria_de_compliance]
      prompt: "Há cláusula anticorrupção (Lei 12.846/2013 + Decreto 11.129/2022)? Exige programa de integridade ou treinamento? Para subsidiárias de multinacionais americanas/inglesas, FCPA/UKBA podem se somar."

    - id: notificacoes
      label: "Endereço para Notificação"
      type: verbatim
      prompt: "Qual o endereço e método de notificação do target?"

    - id: registro_publico
      label: "Registro / Averbação Exigida"
      type: classify
      options: [nao_aplicavel, registro_em_cartorio_titulos_e_documentos, averbacao_inpi, averbacao_BCB_RDE_ROF, registro_junta_comercial, registro_imobiliario]
      prompt: "O contrato exige registro ou averbação para validade ou oponibilidade a terceiros? Ex.: contratos de licença e transferência de tecnologia para gerar dedutibilidade tributária e remessa cambial precisam averbação no INPI (Lei 9.279/96 arts. 211-212 + IN INPI 70/2017); contratos com financeiro internacional, RDE-ROF no BCB."
```

## Adições comuns por tipo de deal

- **Targets de tecnologia / PI-pesados:** escrow de código-fonte, restrições de open source, direitos sobre dados, direitos de treinamento de modelo, acesso a APIs, averbação INPI de software (Lei 9.609/98).
- **Saúde / ciências da vida:** registro ANVISA, obrigações de farmacovigilância, pesquisa clínica (Res. CNS 466/2012 e 510/2016), CEP/CONEP, BPF/GMP, CBPF.
- **Energia / infraestrutura:** licenças ambientais (LP/LI/LO, Lei 6.938/81), outorga ANEEL/ANP/ANA, autorizações setoriais, royalties, encargos setoriais.
- **Imobiliário:** opções de renovação, reajuste (IPCA/IGP-M), CAM/condomínio, sub-rogação, exigência de carta-fiança, ITBI, Lei 8.245/91 se locação.
- **Financeiro regulado:** aprovações regulatórias (BCB, CVM, SUSEP, Previc), requisitos de capital, gatilhos de comunicação a regulador.
- **Concessionárias / contratos com a administração pública:** Lei 14.133/2021 (nova Lei de Licitações), Lei 8.987/95 (concessões), exigência de licitação para subcontratação, equilíbrio econômico-financeiro, garantias.
- **Trabalhista / RH:** sindicato, acordo coletivo, convenção coletiva, FGTS, sucessão trabalhista (CLT arts. 10 e 448 + Súmulas 10 e 448 TST).
- **Internacional / câmbio:** RDE-IED no BCB para investimento estrangeiro direto, RDE-ROF para operação financeira, Lei 14.286/2021 (novo marco cambial), Resolução BCB 277/2022, preço de transferência (Lei 14.596/2023).

## Cortes comuns para primeira passada rápida

Para triagem inicial sob pressão de tempo, estas 7 colunas respondem a 80% das perguntas iniciais do deal: contraparte, data_base, prazo, mudanca_controle, cessao, resilicao_unilateral, foro_solucao_conflitos. Rode essas primeiro; expanda o schema depois que o time da operação priorizar.
