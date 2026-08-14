# wiki/sources/

Papers, podcasts, artigos, videos e livros que voce **ingeriu**. Uma pagina por fonte.

## O que vai aqui

- Papers academicos
- Podcasts relevantes
- Artigos longos (blogs, ensaios)
- Livros (capitulos relevantes)
- Videos/palestras
- Transcricoes de chamadas importantes

## O que NAO vai aqui

- **Tweets, posts curtos** — se importa, mencione inline em outra pagina
- **Conteudo que voce escreveu** -> `wiki/synthesis/`

## Template

```markdown
---
type: source
source_type: paper | podcast | article | book | video | transcript
url: [URL ou path em raw/]
author: [nome]
date: YYYY-MM-DD
ingested: YYYY-MM-DD
tags: []
---

# Titulo da fonte

## Citacao

[Autor, Ano. "Titulo". Veiculo. URL]

## Resumo (TL;DR)

[3-5 linhas — o argumento central]

## Key takeaways

1. ...
2. ...
3. ...

## Conexoes com a wiki

- Atualiza [[wiki/concepts/X]] — [como]
- Reforça [[wiki/entities/Y]] — [aspecto]

## Notas pessoais

[Por que isto importa pra voce. O que vai fazer com isto.]
```

## Convencao de slug

`{primeiro-autor}-{ano}-{tema-curto}.md` — exemplos:
- `karpathy-2024-llm-os.md`
- `pg-2005-how-to-start-a-startup.md`
