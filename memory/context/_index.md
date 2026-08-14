# memory/context/

Singletons operacionais — estado atual do seu mundo. Cada arquivo aqui aparece **uma vez** e e atualizado in-place.

## Arquivos

| Arquivo | Funcao | Update |
|---------|--------|--------|
| `pendencias.md` | Tudo em aberto, classificado por urgencia | Toda sessao |
| `deadlines.md` | Prazos com data | Quando deadline novo/cumprido |
| `business-context.md` | Cache compilado do panorama geral | Quando algo mudar |
| `people.md` | Operacional: contatos, status, proxima acao | Quando relacao mudar |
| `decisoes/YYYY-MM.md` | Decisoes tomadas no mes | Quando decisao importante for tomada |

## Convencao de urgencia em `pendencias.md`

- **Criticas** — bloqueiam progresso AGORA
- **Importantes** — nao bloqueiam mas precisam de acao esta semana
- **Backlog** — sem urgencia
- **Resolvidas** — mover pra ca quando concluir, limpar no fim do mes
