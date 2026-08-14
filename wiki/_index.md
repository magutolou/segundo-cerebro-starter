# wiki/

Camada de **conhecimento que compounding** — informacao que vai te servir daqui a 2 anos.
Diferente de `memory/`, que e operacional (pendencias, sessoes, projetos ativos).

## Subpastas

| Pasta | O que vive aqui | Exemplo |
|-------|-----------------|---------|
| `concepts/` | Frameworks, definicoes, ideias estaveis | `machine-learning.md`, `valuation.md` |
| `entities/` | Pessoas, empresas, organizacoes de relevancia | `elon-musk.md`, `openai.md` |
| `sources/` | Papers, podcasts, artigos ingeridos (1 arquivo por fonte) | `karpathy-2024-llm-os.md` |
| `synthesis/` | Analises suas que conectam multiplas sources | `comparacao-x-vs-y.md` |

## Regras de toda pagina da wiki

1. **Frontmatter YAML obrigatorio** — `type`, `tags`, `created`, `updated`, `sources`.
2. **Wikilinks `[[...]]`** — para Obsidian/Dataview funcionarem.
3. **Cross-refs explicitos** — secao `## Ver tambem` no fim.
4. **Toda alegacao factual** com fonte aponta para arquivo em `sources/`.
5. **Slugs em kebab-case** — `machine-learning.md`, nao `Machine Learning.md`.

## Diferenca: `wiki/entities/` vs `memory/context/people.md`

- **`memory/context/people.md`** — operacional: contato, proxima acao. Atualizado toda semana.
- **`wiki/entities/fulano.md`** — conceitual: quem e, background, papel estrategico. Atualizado quando ingere source nova.

Catalogo completo: `index.md` na raiz do repo.
