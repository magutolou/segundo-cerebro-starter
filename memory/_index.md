# memory/

Camada **operacional** — informacao com prazo de validade. Pendencias, projetos ativos, sessoes, decisoes.

## Regra mental

> Se a informacao tem prazo de validade -> `memory/`.
> Se vai te servir daqui a 2 anos -> `wiki/`.

## Subpastas

| Pasta | O que vive aqui | Frequencia de update |
|-------|-----------------|----------------------|
| `context/` | Estado atual do seu mundo (singletons) | Toda sessao / semana |
| `projects/` | Um arquivo por projeto + `_index.md` | Quando projeto muda de status |
| `sessions/` | Log diario do que rolou | Apos cada sessao de trabalho |

## Singletons em `context/`

- `pendencias.md` — tudo em aberto (criticas, importantes, backlog)
- `deadlines.md` — todos os prazos
- `business-context.md` — cache compilado do panorama
- `people.md` — operacional: contatos, status, proxima acao
- `decisoes/YYYY-MM.md` — um arquivo por mes
