# Guia de Setup — Segundo Cerebro

> **Como usar este guia:** Abra uma conversa com qualquer AI (Claude, ChatGPT, Gemini, etc.)
> e va seguindo as etapas abaixo. A AI vai te ajudar a preencher os arquivos e personalizar
> o cerebro para o seu contexto.
>
> Tempo estimado: 30-60 minutos para o setup basico. Depois, o cerebro cresce organicamente.

**Caminho rapido (Claude Code):** Etapa 0 pra instalar as ferramentas e clonar, depois
`/setup` e a AI conduz o resto conversando. As etapas 1 a 4 sao a versao manual, pra
quem usa ChatGPT/Claude web ou prefere fazer na mao.

---

## Pre-requisitos

1. **Git instalado** — para versionar tudo
2. **Editor de texto** — VS Code, Cursor, ou qualquer um
3. **Obsidian (opcional)** — para navegar visualmente com wikilinks e graph view
4. **Uma AI com acesso a arquivos** — Claude Code, Cursor, ou cole contexto manualmente

---

## Etapa 0 — Instalar as ferramentas (15-20 min, so na primeira vez)

> **Se voce vai usar so ChatGPT/Claude web (sem terminal):** voce ainda precisa dos
> arquivos. Abra a pagina do starter no GitHub, clique no botao verde **Code → Download
> ZIP**, extraia numa pasta e pule direto pra Etapa 1. Voce perde a automacao das skills
> e o versionamento, mas o core funciona colando o `CLAUDE.md` no inicio de cada conversa.

### 0a. Instalar o Git

Verifique se ja tem instalado:
```bash
git --version
```
Se der erro ("comando nao encontrado"):
- **Windows:** baixe em https://git-scm.com/download/win e instale com as opcoes padrao.
- **Mac:** rode `xcode-select --install` no Terminal (instala o Git junto com as ferramentas de linha de comando da Apple).
- **Linux:** `sudo apt install git` (Debian/Ubuntu) ou equivalente da sua distro.

Depois de instalar, configure seu nome e email (usados nos commits):
```bash
git config --global user.name "Seu Nome"
git config --global user.email "seu@email.com"
```

### 0b. Instalar o Node.js (necessario pro Claude Code)

Verifique:
```bash
node --version
```
Se der erro, baixe em https://nodejs.org (versao LTS) e instale.

### 0c. Instalar o Claude Code

```bash
npm install -g @anthropic-ai/claude-code
```

Depois, dentro da pasta do segundo cerebro, rode:
```bash
cd segundo-cerebro    # ou o nome que voce escolheu pra pasta
claude
```
Na primeira vez ele vai pedir login (conta Claude.ai ou API key da Anthropic — siga o
fluxo que aparece no terminal/navegador).

### 0d. Como as skills funcionam por baixo dos panos

O Claude Code **nao detecta skills automaticamente** so por existirem em `skills/`.
Cada skill precisa de um arquivo "wrapper" em `.claude/commands/{nome}.md` — uma linha
so, apontando pro `SKILL.md` correspondente:

```
Leia o arquivo `skills/{nome}/SKILL.md` e execute o roteiro descrito nele integralmente.
```

**Esses wrappers ja vem prontos neste starter** (pasta `.claude/commands/`) pras skills
inclusas (`setup`, `cerebro`, `ingest`, `flush`, `skillify`, `audit`) — nao precisa criar nada
pra usar o que ja esta aqui. So precisa criar um wrapper novo se usar `/skillify` pra
adicionar uma skill que ainda nao existe (a propria skill ja faz isso automaticamente).

Se o atalho `/nome` nao funcionar, invoque pelo nome em linguagem natural (ex.: "liga o
cerebro") — cada `SKILL.md` tem uma lista de triggers no frontmatter.

### 0e. Baixar o starter e tornar o repo seu

Escolha uma pasta onde o cerebro vai morar (ex.: `Documentos`) e clone:

```bash
git clone URL-DO-STARTER segundo-cerebro
cd segundo-cerebro
```

**Agora o passo mais importante desta etapa.** O que voce acabou de clonar ainda
aponta pro repositorio de quem te mandou o starter — se voce commitar assim, o
`/flush` vai tentar dar push no repo da outra pessoa (e falhar, porque voce nao tem
permissao). Apague o historico dele e comece o seu:

```bash
rm -rf .git
git init
git add -A
git commit -m "init: segundo cerebro - estrutura base"
```

No Windows, se o `rm -rf` nao funcionar no seu terminal, use
`Remove-Item -Recurse -Force .git` (PowerShell) ou apague a pasta oculta `.git`
pelo Explorer com "Itens ocultos" ligado.

A partir daqui o cerebro e seu: historico limpo, sem remote nenhum, nada mais
ligado ao starter original.

### 0f. Backup remoto no GitHub (recomendado, opcional)

1. Crie uma conta em https://github.com (se ainda nao tiver).
2. No canto superior direito, clique em **+ → New repository**.
3. De um nome (ex.: `segundo-cerebro`), marque **Private** (seus dados pessoais nao
   devem ficar publicos), e **NAO** marque "Add a README" (voce ja tem os arquivos).
4. Clique em **Create repository**. A proxima tela mostra a URL do repo
   (algo como `https://github.com/SEU-USER/segundo-cerebro.git`) — copie ela.
5. De volta no terminal, na pasta do cerebro:
```bash
git remote add origin https://github.com/SEU-USER/segundo-cerebro.git
git branch -M main
git push -u origin main
```

Se preferir interface grafica em vez de terminal, use o **GitHub Desktop**
(https://desktop.github.com) — ele faz o clone, o commit e o push por botao. So o
`rm -rf .git` da etapa 0e nao tem botao: apague a pasta oculta `.git` pelo Explorer
antes de adicionar a pasta no GitHub Desktop ("Add local repository" vai oferecer
criar um repo novo ali).

> **Nao sabe fazer nada disso?** Cole as etapas 0e e 0f inteiras numa conversa com o
> Claude Code aberto na pasta e peca pra ele executar. Ele roda os comandos por voce.

### 0g. Atalho: deixar a AI conduzir o setup

Se voce instalou o Claude Code, nao precisa seguir as etapas 1 a 4 na mao. Abra
uma sessao na pasta do cerebro e rode:

```
/setup
```

A skill `skills/setup/SKILL.md` faz o mesmo roteiro em formato de conversa — ela
pergunta uma coisa por vez, monta os textos por voce e salva cada arquivo depois
da sua aprovacao. Leva uns 15-20 minutos. Rodar `/cerebro` num cerebro ainda vazio
tambem cai direto nela.

As etapas 1 a 4 abaixo continuam valendo como **versao manual** (pra quem usa
ChatGPT/Claude web, ou pra quem prefere ver o que esta acontecendo). Se voce rodou
`/setup`, pule direto pra Etapa 5.

---

## Etapa 1 — Quem e voce? (10 min)

> **Objetivo:** Dar contexto para a AI te ajudar melhor em TODAS as conversas futuras.

Cole este prompt na sua AI:

```
Estou montando meu segundo cerebro — um repositorio de conhecimento pessoal
que qualquer AI pode usar para me ajudar com contexto. Preciso preencher meu
perfil. Me faz as seguintes perguntas uma por uma e depois gera o texto
final para eu colar no CLAUDE.md, secao "1. Quem sou eu":

1. Qual seu nome, idade e onde voce mora?
2. O que voce estuda ou em que voce trabalha?
3. Quais sao seus 2-3 projetos/objetivos principais agora?
4. Qual seu nivel tecnico? (nao sabe programar / basico / intermediario / avancado)
5. Em que areas voce quer construir conhecimento? (ex: financas, AI, medicina, direito...)
6. Qual idioma prefere para conversas? E para codigo/nomes tecnicos?
7. Que ferramentas voce usa no dia a dia? (editores, apps, plataformas)
```

**Depois:** Copie a resposta da AI e cole no `CLAUDE.md`, secao 1.

---

## Etapa 2 — Seus projetos (15 min)

> **Objetivo:** Mapear tudo que voce esta fazendo/construindo.

Cole este prompt:

```
Agora preciso mapear meus projetos ativos. Para cada projeto que eu te
descrever, crie um arquivo markdown seguindo este template:

---
status: ativo | em-construcao | exploracao | pausado
created: [data de hoje]
updated: [data de hoje]
owners: [quem esta envolvido]
tags: []
---

# [Nome do Projeto]

## O que e
[1-2 paragrafos]

## Responsaveis
[Quem faz o que]

## Status atual
[O que ja foi feito, o que falta]

## Pendencias
- [ ] ...

## Timeline
[Proximos marcos com datas, se tiver]

Vou te descrever meus projetos agora. Para cada um, gere o arquivo.
```

**Depois:**
- Salve cada arquivo em `memory/projects/{slug}.md`
- Atualize `memory/projects/_index.md` com cada projeto nas tabelas

---

## Etapa 3 — Contexto operacional (10 min)

> **Objetivo:** Preencher os arquivos singleton de memory/.

### 3a. Pendencias

Pense: o que esta em aberto na sua vida agora? Tarefas, coisas que voce precisa fazer.
Abra `memory/context/pendencias.md` e preencha:

```markdown
# Pendencias

## Criticas (resolver hoje/amanha)
- ...

## Importantes (resolver esta semana)
- ...

## Backlog (sem urgencia)
- ...

## Resolvidas (limpar no fim do mes)
- (nada ainda)
```

### 3b. Deadlines

```markdown
# Deadlines

| Data | O que | Projeto | Status |
|------|-------|---------|--------|
| YYYY-MM-DD | Descricao | Projeto X | pendente |
```

### 3c. People (opcional)

Se voce trabalha com outras pessoas nos seus projetos:

```markdown
# People — Operacional

| Nome | Papel | Contato | Proxima acao |
|------|-------|---------|--------------|
```

### 3d. Business Context

Cole este prompt na AI:

```
Com base nos projetos que mapeamos e nas pendencias/deadlines que defini,
gere um "business-context.md" — um resumo compilado de tudo que esta
acontecendo na minha vida profissional/academica agora. Formato:

# Business Context
## Visao geral (3-5 linhas)
## Projetos ativos (tabela resumida)
## Equipe / pessoas-chave
## Proximos marcos criticos
## Riscos e atencao

Esse arquivo serve como "briefing rapido" para qualquer AI que for me ajudar.
```

**Depois:** Salve em `memory/context/business-context.md`.

---

## Etapa 4 — Primeira ingestao (10 min)

> **Objetivo:** Testar o workflow de ingest com uma fonte real.

Escolha algo que voce leu recentemente (artigo, paper, podcast, video).
Cole este prompt:

```
Vou te dar uma fonte para ingerir no meu segundo cerebro. O workflow e:

1. Crie wiki/sources/{slug}.md com resumo + key takeaways + citacao
2. Identifique conceitos importantes -> crie/atualize paginas em wiki/concepts/
3. Identifique pessoas/empresas importantes -> crie/atualize paginas em wiki/entities/
4. Me de a entrada para adicionar no index.md e no log.md

Template da source page:
---
type: source
source_type: [paper|podcast|article|book|video]
url: [URL]
author: [nome]
date: YYYY-MM-DD
ingested: YYYY-MM-DD
tags: []
---

# Titulo

## Citacao
[Autor, Ano. "Titulo". Veiculo. URL]

## Resumo (TL;DR)
[3-5 linhas]

## Key takeaways
1. ...

## Conexoes com a wiki
- Atualiza [[conceito-X]] — [como]

## Notas pessoais
[Por que isto importa pra mim]

Aqui esta a fonte: [COLE O CONTEUDO OU URL AQUI]
```

**Depois:**
- Salve os arquivos gerados nas pastas corretas
- Atualize `index.md` e `log.md`
- Commit: `git add -A && git commit -m "ingest: primeira fonte - [titulo]"`

---

## Etapa 5 — Personalize as regras (5 min)

Revise o `CLAUDE.md` e ajuste:

- [ ] Secao 1 preenchida com seu perfil
- [ ] Secao 5 (regras do agente) — adicione/remova regras que fazem sentido pra voce
- [ ] Secao 8 (stack) — suas ferramentas e preferencias
- [ ] Tom de comunicacao — como voce quer que a AI fale com voce?

Dica: se voce usa o repo com multiplas ferramentas, copie o `CLAUDE.md` para
`AGENTS.md` na raiz — alguns agentes (Cursor, Codex) leem esse nome automaticamente.

---

## Etapa 6 — Uso no dia a dia

### Rotina sugerida

**Inicio da sessao:**
1. `/cerebro` (ou "liga o cerebro") — carrega `memory/` + catalogo da wiki e te da o
   briefing do momento: pendencias, deadlines, projetos, ultimos 7 dias
2. Sem Claude Code: abra o repo na AI, ela le o `CLAUDE.md` e ja tem contexto. Se
   precisar de mais: "le meu business-context.md e pendencias.md"

**Durante o trabalho:**
- Leu algo interessante? "Faz ingest disso: [link/texto]"
- Tomou uma decisao? "Registra em decisoes deste mes: [decisao]"
- Terminou uma tarefa? "Marca [tarefa] como resolvida em pendencias"

**Fim da sessao:**
1. `/flush` (ou "fecha a sessao") — atualiza memory/wiki, cria a nota do dia, commita
2. Se nao usa Claude Code: "O que mudou hoje? Atualiza os arquivos de memory/" e commit manual

**Uma vez por mes:**
- `/audit` — varre wiki e memory atras de link quebrado, indice dessincronizado,
  pendencia morta e drift de estrutura. So propoe, voce aprova achado por achado.
  Vale rodar tambem depois de qualquer reorganizacao grande de pastas.

### Skills ja incluidas (Claude Code)

Este starter vem com seis skills funcionais em `skills/`:

| Skill | Quando | O que faz |
|-------|--------|-----------|
| `/setup` | uma vez | onboarding guiado, preenche o cerebro conversando |
| `/cerebro` | inicio de sessao | briefing de pendencias, deadlines, projetos |
| `/ingest` | ao ler algo bom | vira source page + atualiza conceitos, index e log |
| `/flush` | fim de sessao | persiste em memory/wiki, cria a nota do dia, commita |
| `/skillify` | sob demanda | cria uma skill nova com DRY/MECE check |
| `/audit` | mensal | auditoria de saude do cerebro |

Nao precisa criar nada — so usar. Veja `skills/_index.md` pra detalhes de cada uma.

### Como a wiki cresce

A wiki cresce **organicamente** via ingest. Nao force. Toda vez que voce:
- Le um artigo/paper/livro -> ingest cria source + atualiza concepts/entities
- Tem uma insight original -> salva em `wiki/synthesis/`
- Conhece pessoa/empresa relevante -> cria entity

Com o tempo, o graph view do Obsidian vai mostrando as conexoes.

---

## Extras opcionais

### Obsidian como visualizador
1. Baixe o Obsidian (obsidian.md)
2. "Open folder as vault" -> selecione a pasta do segundo cerebro
3. Wikilinks `[[]]` ja funcionam
4. Ative o Graph View para ver conexoes

### Skills adicionais para Claude Code
As seis da tabela acima ja vem prontas. Pra criar mais (cockpit diario, rotina de
estudo, o que for do seu contexto), **nao escreva o `SKILL.md` na mao** — rode
`/skillify`. Ele checa se ja existe algo parecido, se a skill fecha uma lacuna real,
e so entao gera os tres arquivos de uma vez: a pasta `skills/{nome}/SKILL.md`, o
wrapper `.claude/commands/{nome}.md` e a entrada no `skills/_index.md`.

### Context pack para AIs sem acesso a arquivos
Se for usar ChatGPT/Claude web, crie um script que junta:
- CLAUDE.md (resumido)
- business-context.md
- pendencias.md
- projetos ativos

E cole como primeira mensagem. Isso da 80% do valor com 0% de setup tecnico.

---

## FAQ

**P: Preciso saber programar?**
R: Nao. O cerebro e so arquivos markdown. Git e o unico "tecnico" e voce
pode usar GitHub Desktop se preferir.

**P: Posso usar so com ChatGPT/Claude web (sem terminal)?**
R: Sim. Perde automacao mas o core funciona — voce gerencia os arquivos
manualmente e cola contexto no inicio de cada conversa.

**P: Quanto tempo leva pra ficar util?**
R: O setup basico (etapas 1-4) ja e util. Depois de ~10 ingests, a wiki
comeca a ter massa critica e as conexoes aparecem.

**P: E se eu nao uso Obsidian?**
R: Funciona igual. Obsidian e so visualizacao. Os arquivos sao markdown
puro — qualquer editor abre.

**P: Posso adaptar a estrutura?**
R: Sim. A estrutura e uma sugestao testada, nao uma lei. Se algo nao faz
sentido pro seu contexto, mude. So mantenha o CLAUDE.md atualizado pra AI
saber onde as coisas estao.

---

*Criado por Guilherme como template do sistema segundo-cerebro.*
*Faca dele o que quiser. Nao precisa dar credito.*
