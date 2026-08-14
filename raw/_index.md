# raw/

**Fontes brutas imutaveis.** PDFs, transcripts, screenshots, qualquer arquivo que serve como input para a wiki.

O LLM **le** desta pasta mas **nunca modifica**.

## O que vai aqui

- PDFs de papers, reports, decks
- Transcripts de podcasts
- Screenshots com info relevante
- Markdown de artigos clipados
- Datasets (CSVs, JSONs)

## O que NAO vai aqui

- **Resumos / interpretacoes** -> `wiki/sources/{slug}.md`
- **Notas pessoais** -> `memory/sessions/`
- **Conteudo gerado** -> `wiki/synthesis/`

## Workflow

1. Jogue o arquivo aqui
2. Peca para a AI: "faz ingest de raw/{arquivo}"
3. A AI cria source page + atualiza wiki
4. O arquivo fica aqui intocado para referencia

## Convencao de nomes

Datada e descritiva: `2026-05-karpathy-llm-os.pdf`, `2026-04-podcast-startup-x.txt`.
Evite nomes genericos como `doc1.pdf`.

## Quando salvar transcript aqui

So salvar transcript em `raw/` quando ele for **a fonte canonica** (pipeline manual — `yt-dlp`+Whisper, extracao de PDF — e o campo `url:` da source page na wiki fica vazio, porque nao da pra re-fetch). Se a fonte foi lida direto de uma URL (YouTube/Instagram/GitHub via fetch), **nao** salvar copia aqui — a URL na wiki ja e a referencia. Nao duplicar os dois ao mesmo tempo.

## Git e tamanho

- PDFs muito grandes (>50 MB): considere `.gitignore` ou `git-lfs`
- Audio raw: quase sempre ignorar (guardar transcript em vez disso)
