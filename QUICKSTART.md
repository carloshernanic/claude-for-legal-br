# Início Rápido

**60 segundos.** Isso te leva a usar os plugins.

> Esta é uma **adaptação não oficial** do repositório `anthropics/claude-for-legal` para o contexto jurídico brasileiro (LGPD, CLT, CPC, Código Civil, Lei 6.404, Marco Civil, Lei 9.279/96, Lei 9.610/98, etc.). A versão original cobre direito dos EUA. Veja `references/terminology-us-to-br.md` para o mapeamento de institutos.

## Instalar no Claude Cowork
1. [Instale o Claude Desktop](https://claude.com/download)
2. Obtenha acesso ao Claude Cowork
3. Siga o vídeo do repositório original (fluxo de instalação é o mesmo)

## Instalar no Claude Code

1. **Abra o Claude Code** (terminal) ou **Claude Cowork** (desktop). Se você tem o Claude rodando numa janela de terminal, é Claude Code.

2. **Adicione o marketplace.** No Claude Code, digite `/plugin marketplace add ` (com espaço no fim) e **arraste a pasta `claude-for-legal-brasil` para a janela do terminal** — vai preencher o caminho. Aperte Enter.

   (Ou digite o caminho completo: `/plugin marketplace add /caminho/para/claude-for-legal-brasil`)

3. **Instale o plugin.** Escolha o que combina com sua prática na tabela abaixo e:
   ```
   /plugin install privacidade@claude-for-legal-brasil
   ```

4. **⚠️ Reinicie o Claude Code.** Feche e reabra. Esta etapa não é opcional — o plugin só fica ativo após reinício.

5. **Rode o setup.** Leva 2 minutos (rápido) ou 10–15 (completo).
   ```
   /privacidade:cold-start-interview
   ```

6. **Conecte uma ferramenta de pesquisa.** Sem ela, citações ficam marcadas como não verificadas. Para Brasil sugerimos: JusBrasil, Lexml, sítios oficiais (planalto.gov.br, stf.jus.br, stj.jus.br, tst.jus.br) — pode ser via MCP de web search. CoCounsel/Westlaw (no plugin externo) cobre primariamente EUA.

## Instalar com escopo de usuário, não de projeto

Quando `/plugin install` perguntar, escolha **escopo de usuário**.

Escopo de projeto parece mais seguro mas bloqueia o plugin de ler arquivos fora da pasta do projeto — seus modelos no Downloads, seu contrato no Documents, o caso na nuvem. A maior parte das skills lê arquivos. Escopo de usuário **não dá ao plugin nenhum acesso extra** — ele só lê o que você apontar ou o que estiver na pasta atual; apenas funciona de qualquer pasta.

Para trocar: `/plugin uninstall <plugin>`, depois `/plugin install <plugin>@claude-for-legal-brasil` a partir da sua home.

## Qual plugin é pra mim?

| Você é… | Instale… | Primeiro comando |
|---|---|---|
| Advogado(a) de privacidade / Encarregado(a) LGPD | `privacidade` | `/privacidade:use-case-triage` |
| Advogado(a) de contratos / comercial | `comercial` | `/comercial:review` |
| Societário / M&A | `societario` | `/societario:diligence-issue-extraction` |
| Trabalhista / RH jurídico | `trabalhista` | `/trabalhista:wage-hour-qa` |
| Product counsel | `produto` | `/produto:is-this-a-problem` |
| Propriedade intelectual / INPI | `propriedade-intelectual` | `/propriedade-intelectual:clearance` |
| Contencioso (interno ou banca) | `contencioso` | `/contencioso:matter-intake` |
| Regulatório / compliance | `regulatorio` | `/regulatorio:reg-feed-watcher` |
| Governança de IA | `governanca-ia` | `/governanca-ia:use-case-triage` |
| Coordenador(a) de NPJ / clínica | `npj` | `/npj:cold-start-interview` |
| Estudante de Direito | `estudante-direito` | `/estudante-direito:cold-start-interview` |
| Legal ops / buscando skills | `builder-hub` | `/builder-hub:registry-browser` |

## O que você está instalando

Cada plugin aprende o playbook do seu escritório/jurídico através de uma entrevista de setup, grava num perfil de prática (`~/.claude/plugins/config/claude-for-legal/<plugin>/CLAUDE.md`) e toda skill lê dele. O perfil é seu — edite, rode o setup de novo, ou diga pra uma skill atualizar.

**Todo output é rascunho para revisão por advogado(a) inscrito(a) na OAB.** Os plugins sinalizam o que não têm certeza, marcam citações por fonte e travam qualquer coisa irreversível. Cabe ao(à) advogado(a) revisar, validar e assumir responsabilidade profissional. Os plugins aceleram essa revisão; não substituem.

## O que tem na caixa

12 plugins por área de prática, 5 cookbooks de agentes monitorados, conectores. A referência completa está no [README.md](README.md).

## Travou?

- **"Command not found"** após instalar → você pulou o passo 4. Reinicie o Claude Code.
- **"Run setup first"** → rode `/<plugin>:cold-start-interview` antes de qualquer comando.
- **Citações marcadas `[verify]`** → conecte uma ferramenta de pesquisa (passo 6). Sem isso, cada citação vem de dados de treino, não de base atualizada.
- **"I can't read [file]"** → quase sempre significa que o plugin está em escopo de projeto e o arquivo está fora. Veja "Instalar com escopo de usuário" acima — reinstale com escopo de usuário ou mova o arquivo pra dentro do projeto.
- **O plugin não faz X** → rode `/builder-hub:related-skills-surfacer` pra achar uma skill melhor, ou veja "What this plugin does not do" no README do plugin.
