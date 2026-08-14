# wiki/concepts/

Frameworks, definicoes e ideias **estaveis** — coisas que valem a pena entender uma vez bem e voltar.

## O que vai aqui

- Frameworks da sua area de conhecimento
- Definicoes tecnicas que voce referencia com frequencia
- Mental models que voce usa

## O que NAO vai aqui

- **Pessoas/empresas** -> `wiki/entities/`
- **Conhecimento sobre uma fonte especifica** -> `wiki/sources/`
- **Analises suas proprias** -> `wiki/synthesis/`
- **Pendencia/projeto/decisao operacional** -> `memory/`

## Criterio para criar pagina

Pergunte: "Daqui a 1 ano, eu vou voltar aqui para reler?"
- **Sim** -> vale a pagina
- **Nao** -> nao vale, deixa so na source page

## Template

```markdown
---
type: concept
tags: []
created: YYYY-MM-DD
updated: YYYY-MM-DD
sources: [wiki/sources/source-1.md]
---

# Nome do Conceito

[Definicao em 2-3 linhas — o que e, em linguagem natural.]

## Mecanica / Estrutura

[Como funciona]

## Quando usar / Quando nao usar

[Limites de aplicabilidade]

## Por que importa para mim

[Conexao com seus projetos ou objetivos]

## Ver tambem

- [[wiki/concepts/relacionado]]

## Sources

- [[wiki/sources/paper-que-citei]] — [resumo de 1 linha]
```
