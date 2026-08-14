---
name: ingest
description: >
  Processa uma fonte nova (paper, artigo, podcast, video, transcricao) e integra
  ao segundo cerebro. Le de raw/, escreve source page em wiki/sources/, atualiza
  paginas de conceitos/entidades relacionadas, atualiza index.md e log.md.
  Triggers: "ingest", "/ingest", "processa essa fonte", "adiciona a wiki",
  "ingere [arquivo]", "discutir fonte", "discutir [video/paper]", "vamos discutir".
---

# /ingest

Pipeline de ingest de fonte nova. A wiki e um artefato que compounding —
cada source nova torna a wiki mais rica.

---

## Inputs aceitos

- Arquivo em `raw/` (PDF, MD, TXT, transcricao)
- URL (artigo web, YouTube, Instagram, GitHub)
- Texto colado direto no chat
- Imagem (paper screenshot, notas)

Para cada caso, identificar antes de prosseguir:
- **Tipo**: paper | article | podcast | video | book | transcript | other
- **Autor**
- **Data de publicacao**
- **Onde esta** (path em `raw/` ou URL)

Se faltar metadado essencial, **pergunte uma vez** antes de processar.

### Captura de transcript de video (YouTube/Instagram)

WebFetch nem sempre expõe o transcript de um video — antes de desistir da fonte, escolher o metodo certo:

1. **YouTube** — tentar `youtube_transcript_api` (ou a legenda exposta via WebFetch). Sem legenda disponivel: fallback `yt-dlp` (baixa audio) + `openai-whisper` (transcreve localmente, requer `ffmpeg`).
2. **Instagram (Reels/posts)** — WebFetch so retorna a legenda/caption do post, **nunca** o audio/transcript do video. Pipeline: `yt-dlp` (baixa audio `.m4a`) -> `openai-whisper` local -> usar o texto resultante como fonte.
3. **Outra plataforma sem transcript exposto** — mesmo fallback (`yt-dlp` + `whisper`) quando aplicavel.

**Regra de `raw/`:** se a fonte foi lida direto de uma URL com fetch (transcript/legenda ja exposta), **nao** salvar copia em `raw/` — a URL na wiki ja e a referencia. So salvar transcript em `raw/` quando ele veio do pipeline manual (`yt-dlp`+Whisper) e nao da pra re-fetch — nesse caso o campo `url:` da source page fica vazio.

---

## Passo 0 — Escolher o modo

Antes do pipeline, decidir o modo:

- **Modo rápido** — fonte simples ou de baixa densidade. Segue o pipeline direto (Passos 1–8).
- **Modo discussão** — fonte rica + usuário quer aprender a fundo (ou pede "discutir"). Ver seção [Modo Discussão](#modo-discussao) abaixo. Nesse modo, as páginas wiki e a source page emergem **depois** da discussão, não antes — a discussão as enriquece.

---

## Pipeline

### Passo 1 — Ler a fonte

Se arquivo em `raw/`: ler conforme tipo.
Se URL: fetch o conteudo.
Se texto colado: usar direto.

Para PDFs longos, fazer leitura completa primeiro, nao so primeiras paginas.

> **Guarda de leitura:** nunca sintetizar nem atribuir conteudo a uma fonte antes de confirmar que ela esta de fato em contexto. Um retorno "file read" (ou "PDF file read") **sem o conteudo visivel nao conta como lida** — pode ter falhado ao renderizar. Se um PDF/anexo nao apareceu, reler antes de afirmar qualquer coisa sobre ele.

### Passo 2 — Identificar conexoes com a wiki

Antes de escrever qualquer coisa nova, **verificar o que ja existe**:

```bash
# Ler index.md para mapa atual da wiki
cat index.md

# Paginas existentes
ls wiki/concepts/*.md 2>/dev/null | grep -v '_index.md'
ls wiki/entities/*.md 2>/dev/null | grep -v '_index.md'
```

Identificar:
- Quais conceitos da fonte ja tem pagina? (vai *atualizar*, nao duplicar)
- Quais entidades da fonte ja tem pagina? (mesmo)
- Quais conceitos/entidades **novos** merecem pagina propria? (criar)
- A fonte **contradiz** algo que ja esta na wiki? (flagar)

> Nunca toque em `_index.md`. Esses arquivos sao mapas/convencoes de cada pasta.

### Passo 3 — Criar a source page

Em `wiki/sources/{slug}.md`:

```markdown
---
type: source
source_type: paper | podcast | article | book | video | transcript
url: [URL ou path em raw/]
author: [nome]
date: YYYY-MM-DD
ingested: YYYY-MM-DD
tags: [tag1, tag2]
---

# [Titulo da fonte]

## Citacao

[Autor, Ano. "Titulo". Veiculo. URL]

## Resumo (TL;DR)

[3-5 linhas, o argumento central]

## Key takeaways

1. [insight principal]
2. [insight principal]
3. [insight principal]

## Quotes relevantes

> "..."

## Conexoes com a wiki

- Atualiza [[wiki/concepts/X]] — [como]
- Cria [[wiki/concepts/Y-novo]] — [contexto]
- Reforça [[wiki/entities/Z]] — [aspecto]

## Notas pessoais

[Por que isto importa para mim. O que vou fazer com isto.]
```

### Passo 4 — Atualizar paginas relacionadas

Para cada conceito/entidade que a fonte toca:

**Se a pagina ja existe:**
- Adicionar nova source na secao `## Sources` da pagina
- Se ha informacao nova, atualizar o corpo (preservar o que ja estava)
- Bumpar `updated: YYYY-MM-DD` no frontmatter
- Se a fonte contradiz algo, criar/atualizar secao `## Contradicoes`

**Se a pagina nao existe e o conceito merece:**
- Criar `wiki/concepts/{slug}.md` ou `wiki/entities/{slug}.md` com template padrao
- Linkar de volta para a source

**Criterio para "merece pagina propria":**
- Conceito tem profundidade conceitual (vai voltar em outras conversas)
- Entidade e referencia recorrente (nao one-off)
- Se for tangencial, **nao** criar pagina — deixar so na source page

### Passo 5 — Atualizar index.md

- Adicionar entrada na secao apropriada
- Atualizar contadores
- Bumpar timestamp

### Passo 6 — Atualizar log.md

Append:

```markdown
## [YYYY-MM-DD HH:MM] ingest | [Titulo da fonte]

Atualizou: [pages tocadas, lista curta]
Notas: [1 linha sobre o achado mais importante]
```

### Passo 7 — Atualizar memory/ (se aplicavel)

Se a fonte gera acao operacional:
- **Insight aplicavel a projeto ativo** -> nota em `memory/projects/{slug}.md`
- **Decisao estrategica disparada** -> registrar em `memory/context/decisoes/YYYY-MM.md`
- **Pendencia criada** -> adicionar em `memory/context/pendencias.md`

### Passo 8 — Confirmar com output compacto

```
Source ingerida: [titulo]

Wiki:
  + wiki/sources/[slug].md (nova)
  + wiki/concepts/[X].md (nova)
  ^ wiki/concepts/[Y].md (atualizada)
  ^ wiki/entities/[Z].md (atualizada)

Index:
  ^ index.md (N entradas adicionadas)

Log:
  ^ log.md

Memory:
  ^ memory/projects/[slug].md (nota adicionada)

Proximos:
  - Considere ingerir [paper relacionado mencionado na fonte]
```

---

## Modo Discussão
<a name="modo-discussao"></a>

Usar quando a fonte e rica e o objetivo e **aprendizado profundo**, nao so registro. O ingest deixa de ser uma tacada mecanica e vira uma discussao didatica, bloco a bloco, da qual as paginas wiki emergem.

### Antes de comecar

- Segmentar a fonte em **blocos tematicos** (nao capitulo a capitulo).
- Mapear capitulos -> blocos.
- Planejar checagem de cobertura de capitulos no fim (garantir que nada ficou de fora).

### Conduzir UM subtopico por vez

Esperar o usuario entre cada subtopico — **nunca despejar o bloco inteiro**. Cada subtopico segue esta estrutura:

1. **Locator** do trecho — minutagem (ex.: `4.2 [20:26–23:06]`) ou, em transcricoes sem tempo, pagina (`PAxx, p.N`).
2. **Sub-divisao** se o subtopico for denso (partes nomeadas).
3. **Detalhamento que vai alem da fonte** — explicar, nao resumir; trazer o raciocinio completo, inclusive o que a fonte deixa implicito. **Expor no vocabulario do proprio autor**; ao inserir comentario proprio, **marcar explicitamente que e comentario meu, nao do autor** (fidelidade acima de tudo).
4. **Quotes/referencias** diretas da fonte.
5. **Conexao com o usuario** — *opcional*: so quando a ligacao com ele/os projetos for real. **Nao forcar analogia**; se nada genuino se apresenta, omitir este elemento.
6. **Captura proposta** ao fim do subtopico: sintese (2-3 linhas) **+ 1 frase-ancora verbatim com locator** -> usuario aprova/edita/descarta -> persistir. O gatilho de anotar e meu, nao da memoria dele.

**Recall:** por default, uma pergunta de recall por subtopico. A pedido do usuario, pode ser **agrupado no fim** da discussao (perguntas sobre os blocos, nao por subtopico) — respeitar a preferencia dele.

### Ao fim de cada bloco

Fechamento curto sintetizando o bloco.

### Ao fim da fonte inteira

1. **Fechamento geral** conectando os blocos.
2. **Revisao da fonte inteira** — notas cross-cutting que so aparecem com tudo na mesa + higiene de citacoes (a source page existe? as citacoes apontam pra fonte certa? ha embaraço entre fontes parecidas?).
3. **So entao** criar/finalizar a source page e rodar os Passos 5–7 (index, log, memory).

> **Distincao importante:** revisao de conteudo (camada do conhecimento, o *que*) ≠ dream cycle (camada do processo/skill, o *como*). Sao passos diferentes; nao confundir um com o outro.

### Subtopicos grandes que merecem sessao propria

Se a discussao gerar um subtema grande demais pra resolver inline (ex.: um projeto novo, uma decisao de arquitetura), **avisar o usuario**: criar sessao agora so se for oportuno; senao, registrar em `memory/context/pendencias.md` com contexto suficiente pra retomar frio. Manter o foco na fonte.

---

## Regras

- No modo discussao, **um subtopico por vez** — nunca despejar o bloco inteiro.
- Captura e **proposta, nao salva automaticamente** — sempre aguardar aprovacao. Responder a pergunta de recall do subtopico **nao** conta como aprovar a captura. Um token de continuacao explicito ("segue"/"sim") apos uma captura proposta **conta como aprovacao** dela (o usuario pode fixar essa convencao); fora isso, aguardar "aprovo"/edicao explicita antes de persistir.
- A discussao **vai alem da fonte** — conectar com o usuario e projetos e parte do protocolo, nao extra.
- **Nunca duplicar.** Sempre verificar `index.md` antes de criar pagina nova.
- **Frontmatter YAML obrigatorio** em toda pagina criada.
- **Wikilinks `[[...]]`** sempre, para Obsidian compat.
- **Contradicoes devem ser flagged**, nunca silenciosamente substituir info anterior.
- **Source page e fonte canonica do que o autor disse.** Interpretacoes vao em `## Notas pessoais` ou `wiki/synthesis/`.
- **Nao fazer ingest de fonte de baixa qualidade.** Post de forum aleatorio vai em `memory/sessions/` como nota, nao em `wiki/sources/`.

---

## Quando NAO usar /ingest

- Pergunta pontual ("o que e X?") — so responder, nao criar source page
- Discussao conceitual sem fonte especifica — registrar em `wiki/synthesis/` ou `memory/sessions/`
- Task operacional — nao tem source de conhecimento
