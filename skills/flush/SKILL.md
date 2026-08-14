---
name: flush
description: >
  Flush de fim de sessao do segundo cerebro — captura tudo que aconteceu e persiste.
  Percorre memory/ (operacional) e wiki/ (conhecimento), garantindo que nada se perde.
  Sempre rodar antes de fechar sessao.
  Ativar SOMENTE via /flush explicito. Sem triggers em linguagem natural (PT ou EN).
---

# /flush

Flush completo de fim de sessao. Captura o contexto da conversa e persiste no segundo cerebro.

---

## Passo 1 — Revisar a sessao (sem output)

**Escopo: esta conversa apenas.** O /flush captura o que aconteceu aqui,
nao o estado geral do secbrain. Sessoes de outras janelas/ferramentas nao
salvas ficam de fora — mencionar no output final quais sao conhecidas
("Ficou fora: [sessao X]"), mas nao tentar reconstrui-las.

Revisar mentalmente TUDO que aconteceu:

**Operacional (memory/):**
- Decisoes tomadas
- Pendencias criadas ou resolvidas
- Pessoas mencionadas (novas ou com role atualizado)
- Projetos com status alterado
- Deadlines novos ou concluidos

**Conhecimento (wiki/):**
- Sources ingeridas (papers, podcasts, artigos lidos)
- Conceitos novos mencionados que merecem pagina propria
- Entidades novas
- Sinteses/analises produzidas que valem persistir

**Estrutural:**
- Skills criadas, editadas
- Schema (CLAUDE.md) alterado

---

## Passo 2 — Atualizar arquivos

### Decisao: memory/ vs wiki/

Se a informacao tem prazo de validade (pendencia de hoje, decisao deste mes) -> `memory/`.
Se serve daqui a 2 anos (conceito, perfil estrategico de pessoa) -> `wiki/`.
Pode ir nos dois (pessoa vai em `memory/context/people.md` operacional E `wiki/entities/` conceitual).

### Checklist de atualizacao

Para cada categoria, verificar se houve mudanca na sessao:

- [ ] `memory/context/pendencias.md` — novas pendencias? resolvidas?
- [ ] `memory/context/deadlines.md` — novos prazos? cumpridos?
- [ ] `memory/context/people.md` — pessoa nova? role mudou?
- [ ] `memory/context/decisoes/YYYY-MM.md` — decisao importante?
- [ ] `memory/projects/{slug}.md` — projeto mudou de status?
- [ ] `memory/projects/_index.md` — tabela consistente?
- [ ] `memory/context/business-context.md` — cache ainda reflete a realidade?
- [ ] `wiki/sources/` — fonte ingerida?
- [ ] `wiki/concepts/` — conceito novo ou atualizado?
- [ ] `wiki/entities/` — entidade nova ou atualizada?
- [ ] `wiki/synthesis/` — analise nova?

---

## Passo 3 — Criar/atualizar sessao do dia

Escrever em `memory/sessions/YYYY-MM/YYYY-MM-DD.md` (subpasta do mes; criar `YYYY-MM/` se for o 1o dia do mes):

```markdown
# Sessao — YYYY-MM-DD

## O que foi feito
- [lista]

## Decisoes
- [se houver, omitir secao senao]

## Atualizacoes na wiki
- [ingests, novas paginas — omitir secao se nenhuma]

## Em aberto
- [pendente para proxima sessao]
```

Se arquivo ja existir (segunda sessao do dia), **adicionar** secao `## Sessao HH:MM` no fim.
**Exceção:** se o arquivo do dia for da **mesma sessao em curso** (ex.: escrito a mao no
meio da sessao, antes do flush), **mesclar/enriquecer no lugar** em vez de anexar um novo
bloco `## Sessao HH:MM` — nao fragmentar uma sessao continua em duas.

---

## Passo 4 — Atualizar log.md

Append no `log.md` da raiz:

```markdown
## [YYYY-MM-DD HH:MM] session | [resumo em 1 linha]

[2-3 linhas opcionais com highlights]
```

Se a sessao incluiu ingest, adicionar entrada separada:

```markdown
## [YYYY-MM-DD HH:MM] ingest | [titulo da source]

[1 linha — quais paginas foram afetadas]
```

---

## Passo 5 — Atualizar index.md (se houve mudanca em wiki/)

Se criou ou alterou pagina em `wiki/`:
- Adicionar/atualizar entrada na secao correta
- Atualizar contadores
- Bumpar timestamp

---

## Passo 6 — Verificacao rapida

Checar que nada ficou inconsistente:
- `_index.md` de projetos reflete os arquivos?
- `log.md` teve append?
- `pendencias.md` esta atualizado?

Se inconsistencia obvia, corrigir antes de commitar.

---

## Passo 7 — Dream cycle (melhoria de skill)

Passo automatico de auto-melhoria — **Mecanismo 1** (gatilho manual via /flush + passo garantido + aprovacao humana). Escopo **ESTREITO**: so processo/skills, nao conhecimento (o conhecimento ja foi capturado durante a sessao). Manter estreito e o que deixa isso barato e seguro.

Fazer UMA pergunta:

> "Das skills invocadas nesta sessao, alguma mostrou friccao, lacuna ou comportamento que tive que corrigir no meio?"

- **Se nao:** registrar "dream cycle: sem friccao" e seguir. Custa quase nada — a maioria das sessoes cai aqui.
- **Se sim:** para cada skill com friccao, **propor o diff** do SKILL.md correspondente (o que mudar e por que). **Aguardar aprovacao do usuario** antes de escrever — nunca auto-aplicar. Se aprovado, aplicar e incluir no mesmo commit.

> **Por que com portao de aprovacao:** auto-aplicar mudanca em skill sem revisao amplifica defeitos em vez de corrigir (principio "saturar a fase", ver `wiki/concepts/ai-loops.md`). O portao e o controle de qualidade, nao um estorvo. A versao totalmente automatica sem gatilho (Mecanismo 2, via hook SessionEnd + Claude headless) fica adiada — perde o portao de aprovacao e gasta tokens em toda sessao silenciosamente.

---

## Passo 8 — Commit e push

```bash
git add .
git commit -m "sessao: [resumo curto e especifico]"
git push origin main
```

Convencoes de commit:
- `sessao: ...` — flush regular
- `wiki: adiciona [pagina]` — quando criou wiki page
- `ingest: [titulo]` — quando fez ingest
- `setup: ...` — mudancas estruturais
- `decisao: ...` — quando uma decisao importante foi registrada

Se push falhar com "rejected":
```bash
git pull --rebase origin main
git push origin main
```

---

## Passo 9 — Confirmar

```
Sessao salva — DD/MM/YYYY HH:MM

Atualizado:
  [memory] [arquivo 1]
  [memory] [arquivo 2]
  [wiki]   [arquivo 3]
  [root]   log.md, index.md

Nao precisou:
  [categorias sem mudancas]

Pushed para origin/main.

Ficou fora (nao salvo aqui):
  [sessoes de outras janelas nao salvas, se conhecidas — omitir secao se nenhuma]
```

---

## Regras

- **Nunca pular o Passo 2** — revisar cada categoria mesmo que pareca nada novo
- **Nunca sobrescrever sessao existente** — append, nunca replace
- **Commits especificos** — "sessao: mapeia projetos + ingere paper X" > "sessao: updates"
- **Conflito de push** -> sempre `pull --rebase`, nunca `--force`
- **Se houve ingest, nao esqueca do `index.md` e `log.md`**
- **Dream cycle (Passo 7) e proposta, nunca auto-aplicacao** — sempre aguardar aprovacao antes de editar SKILL.md
- Tom direto no output, sem explicacao desnecessaria
