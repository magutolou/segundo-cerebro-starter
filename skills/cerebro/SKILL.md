---
name: cerebro
description: >
  Liga o segundo cerebro — carrega contexto operacional (memory/) e referencia
  o catalogo de conhecimento (wiki/). Modo Setup quando nao configurado.
  Modo Briefing quando ja configurado.
  Triggers: "cerebro", "/cerebro", "liga o cerebro", "conecta o segundo cerebro".
---

# /cerebro

Boot do segundo cerebro hibrido. Dois modos de operacao detectados automaticamente.

---

## Deteccao de modo

Verificar se o cerebro ja foi preenchido. Indicadores de **nao configurado** (basta um):
- `CLAUDE.md` secao 1 ainda contem `[Descreva quem voce e`
- `memory/context/business-context.md` ainda contem `[Quem voce e`
- `memory/projects/_index.md` nao tem nenhum projeto real
- `memory/context/pendencias.md` so tem as pendencias de template (as que apontam
  pras etapas do proprio `SETUP.md`)

Resultado:
- Algum indicador presente -> **Modo Setup**
- Nenhum -> **Modo Briefing**

---

## MODO SETUP (primeira vez)

Cerebro nao configurado detectado. Redirecionar automaticamente para `/setup`.

```
Seu segundo cerebro ainda nao esta configurado.
Vou iniciar o setup guiado — eu pergunto, voce responde, e eu monto tudo.
```

Executar a skill `/setup` (em `skills/setup/SKILL.md`).

---

## MODO BRIEFING (ja configurado)

### Fase 1 — Carregar contexto (paralelo, sem output)

Ler em paralelo (NAO exibir conteudo bruto):

**Schema:**
1. `CLAUDE.md`

**Memory operacional:**
2. `memory/context/pendencias.md`
3. `memory/context/deadlines.md`
4. `memory/context/business-context.md`
5. `memory/context/people.md`
6. `memory/context/decisoes/` — mes corrente
7. `memory/projects/_index.md`
8. `memory/sessions/YYYY-MM/` — ultimos 7 dias (arquivos na subpasta do mes; a janela pode cruzar 2 meses)

**Wiki (so catalogo):**
9. `index.md`
10. `log.md` — ultimas 10 entradas

Arquivos ausentes: pular silenciosamente.

### Fase 1.5 — Checagem de integridade

Alertas relevantes:
- Sessao anterior nao salva (commits sem session file)
- Wiki tem paginas envelhecidas (>90 dias sem update)
- Pendencia critica parada >14 dias

### Fase 2 — Briefing compacto

```
=== SEGUNDO CEREBRO — DD/MM/YYYY ===

OPERACIONAL:
  N pendencias (N criticas, N importantes)
  N deadlines proximos (mais urgente: [item] em X dias)
  N projetos ativos / N em construcao / N em exploracao
  Ultima sessao: [data]

CONHECIMENTO (wiki):
  N conceitos · N entidades · N sources · N sinteses

ULTIMOS 7 DIAS:
  <resumo consolidado em 3-5 linhas>

DECISOES RECENTES (mes corrente):
  -> [decisao 1]
  -> [decisao 2]

ALERTAS:
  -> [deadlines <=7d, pendencias criticas paradas, paginas wiki desatualizadas]
  (se nenhum: "Nenhum alerta.")

Cerebro ligado. O que vamos trabalhar?
```

### Pull on demand

Apos briefing, aceitar:
- `mostra pendencias` -> `pendencias.md`
- `mostra deadlines` -> `deadlines.md`
- `mostra decisoes` -> decisoes do mes
- `mostra projetos` -> `_index.md`
- `mostra equipe` -> `people.md`
- `mostra [projeto]` -> `memory/projects/{slug}.md`
- `mostra wiki` -> `index.md` completo
- `mostra [conceito]` -> `wiki/concepts/{slug}.md`
- `mostra log` -> ultimas 20 entradas

Os dados ja foram carregados — nao re-fetch.

### Fallback

- Arquivo ausente -> omitir, continuar
- Tudo falha -> "Nao consegui carregar o cerebro. Verificar diretorio do repo."

### Comportamento

- Tom direto, sem preambulo
- Aguardar instrucao apos o briefing
