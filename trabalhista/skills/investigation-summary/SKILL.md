---
name: investigation-summary
description: >
  Redige um resumo específico por audiência a partir do memorando privilegiado
  de apuração — versões para RH/DP, alta direção/conselho ou advocacia
  externa. Use quando o memorando precisa ser comunicado a uma audiência que
  não deve ver o material de trabalho privilegiado completo.
argument-hint: "[nome da apuração] [audiência: rh / direcao / advocacia-externa]"
---

# /investigation-summary

Redige um resumo enxuto e apropriado por audiência a partir do memorando
privilegiado de apuração. Resumos para RH/DP não contêm análise de sigilo
profissional. Resumos para direção são em alto nível. Briefings para
advocacia externa incluem contexto completo.

## Instruções

1. Carregue a skill de referência `internal-investigation` e rode o Modo 5 (Resumo por audiência).
2. Se ainda não houver memo, ofereça redigir o memo primeiro.
3. Resumos para RH/DP não devem incluir impressões mentais do(a) advogado(a),
   metodologia de avaliação de credibilidade ou análise de exposição jurídica.

## Exemplos

```
/trabalhista:investigation-summary [nome da apuração] rh
```

```
/trabalhista:investigation-summary [nome da apuração] direcao
```

```
/trabalhista:investigation-summary [nome da apuração] advocacia-externa
```

> Regras de filtragem por audiência e templates de resumo vivem na skill de
> referência `internal-investigation` — carregue antes de fazer trabalho
> substantivo.
