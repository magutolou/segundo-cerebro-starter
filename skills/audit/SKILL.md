---
name: audit
description: >
  Auditoria de governanca do segundo cerebro — varre wiki/ e memory/ por caminhos
  quebrados, indices dessincronizados, pendencias mortas e drift de estrutura.
  Diagnostica e propoe correcao por achado; nunca auto-aplica. Rodar mensal ou
  depois de reorganizacao grande. Triggers: "audit", "/audit", "auditar cerebro", "poda".
---

# /audit

Governanca do cerebro: detecta o drift que acumula em silencio (link quebrado, indice
que mente, pendencia morta, arquivo fora do lugar). Faz o que um cron de auditoria faria
num setup 24/7, mas sob demanda e com portao humano. Nao confundir com o passo 7 do
`/flush` (audita so processo/skills).

---

## Escopo e regra de ouro

- **Dois passes + 1 check transversal:** transversal (caminhos quebrados) → Pass A (wiki-lint) → Pass B (memory-poda).
- **Propoe, nunca aplica.** Cada achado vira uma acao proposta com portao (`aprovar / pular / depois`). Nada toca o disco sem OK explicito. Auto-aplicar amplifica defeito.
- **Arquivos vivos vs append-only.** NUNCA propor editar historico: `log.md`, `memory/sessions/`, `memory/context/decisoes/`, `raw/`. Caminho antigo ali e legitimo (registro do dia em que foi escrito). Auditar e propor mudanca **so em arquivo vivo**.
- **Nao commitar.** Fluxo de commit pertence ao `/flush`.
- **v1 = checks core + transversal** (abaixo). Secundarios ficam pra v2 — ver lista no fim. Saturar a fase: skill minima primeiro, expande quando o uso provar que o report e util e nao vira nag.

---

## Fase 1 — Carregar contexto (sem output)

Mapear, em paralelo:

1. `index.md` (catalogo da wiki) + arvore real de `wiki/**/*.md`.
2. `memory/projects/_index.md` + arvore real de `memory/projects/` (raiz + subpastas).
3. `memory/context/pendencias.md`, `deadlines.md`, `people.md`, `business-context.md`.
4. `log.md` (ultimas ~15 entradas) + `git log --oneline -15` + `memory/sessions/YYYY-MM/` (ultimos 7 dias, subpasta do mes) — pra cruzar o que ja foi feito.
5. `CLAUDE.md` secoes 3-4 (convencoes de arquivo + spec do lint).

---

## Fase 2 — Check transversal: caminhos quebrados (roda primeiro)

O mais importante. Grep por referencias de caminho em backtick (`` `memory/...` ``, `` `wiki/...` ``, `` `skills/...` ``) **so em arquivos vivos** — excluir `log.md`, `sessions/`, `decisoes/`, `raw/` do escopo. Pra cada ref, checar via Glob se o arquivo existe.

- Arquivo apontado nao existe → **🔴 caminho quebrado**. Acao proposta: corrigir pro caminho real (ou remover a ref se o alvo morreu).

---

## Fase 3 — Pass A: wiki-lint (`wiki/`)

**Core:**
- **Wikilink quebrado** — extrair todo `[[x]]` do vault; se `x.md` nao existe em lugar nenhum → **🔴**. (Ressalva: `[[caminho/com/...]]` ilustrativo em `_index` nao conta.)
- **index.md mente** — diff entre `index.md` e a wiki real: pagina no disco fora do catalogo, entrada do catalogo → arquivo inexistente, ou contador total errado → **🔴**.
- **Pagina orfa** — pagina de `concepts/`, `entities/`, `synthesis/` ou `sources/` sem nenhum inbound `[[link]]` de outra pagina (backtick `` `caminho.md` `` NAO conta — no Obsidian so wikilink cria aresta). Acao: propor o(s) wikilink(s) de onde ela deveria ser alcancada (tipicamente o "Ver tambem" do conceito-pai, convertendo backtick → `[[wikilink]]`) → **🟠**. **Escopo = so `wiki/`.** `memory/` (projects/context/sessions) e `raw/` sao acessados por caminho via skills, nao por grafo — orfandade ali e esperada, NAO flagar (excecao: arquivo de projeto que tem cluster conceitual obvio e ficou solto pode virar sugestao 🟡).

---

## Fase 4 — Pass B: memory-poda (`memory/`)

**Core:**
- **`_index` de projects dessincronizado** — projeto no disco sem entrada na tabela, ou entrada → caminho inexistente (pega quebra pos-move) → **🔴**.
- **Pendencia resolvida nao limpa** — item `[x]` ou que `log`/`sessions` mostram feito, ainda na lista ativa; secao "Resolvidas" inchada → **🟠** arquivar.
- **Residuo de projeto encerrado** — mencao operacional viva (ex.: "proxima acao" preenchida) a projeto pausado/encerrado em `people.md` ou `pendencias.md` → **🟠** podar.
- **Drift de subpasta** — arquivo na raiz de `projects/` que e artefato/cluster de um projeto existente, contra a convencao de subpasta do `_index` → **🟠** sugerir mover (com os ponteiros vivos a consertar junto).
- **Sessao nao salva** — commit recente sem session file correspondente em `memory/sessions/YYYY-MM/` → **🟠**.
- **Deadline vencido** — data no passado em `deadlines.md` ainda marcada como pendente → **🟠**.

---

## Fase 5 — Report (proposta com portao)

Agrupar por pass, **🔴 primeiro**. Cada achado:

```
[🔴|🟠|🟡] <check> · arquivo:linha
  achou:    <o que esta errado>
  proponho: <acao concreta — corrigir path X, mover Y pra subpasta, arquivar Z>
  [ aprovar / pular / depois ]
```

Fechar com contagem (`N achados: X criticos, Y higiene`). Nada e aplicado ate o usuario responder. Aplicar so os aprovados, um a um. Caminho quebrado e move de arquivo arrastam o conserto dos ponteiros vivos que apontam pro alvo (mesma regra: nunca tocar append-only).

---

## Regras

- **Propoe, nunca aplica** — portao por achado, sempre.
- **Nunca tocar append-only** (`log.md`, `sessions/`, `decisoes/`, `raw/`). Caminho historico ali e correto.
- **Nao commitar** — e do `/flush`.
- **🔴 primeiro** — quebra real antes de higiene.
- **Tom direto** — sem preambulo; report escaneavel.

---

## v2 (deferido — so depois da 1a rodada provar utilidade)

Pass A: claim sem fonte (regra 5 do CLAUDE.md), conceito recorrente sem pagina propria, pagina envelhecida (>90d sem `updated`), frontmatter incompleto, "Ver tambem" assimetrico.
Pass B: pendencia critica parada >14d (cobra, nao deleta), `business-context.md` defasado vs. mudancas recentes.
