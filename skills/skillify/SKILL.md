---
name: skillify
description: >
  Processo padrao de criacao de novas skills no segundo cerebro.
  Faz DRY check (skill parecida ja existe?) e MECE check (preenche lacuna real?)
  antes de criar. So apos confirmacao, gera os 3 arquivos necessarios.
  Triggers: "skillify", "/skillify", "criar skill", "nova skill".
---

# /skillify

Meta-skill — cria novas skills com checagem de duplicacao e cobertura.
Substitui criar `SKILL.md` na mao.

---

## Fase 1 — Carregar contexto (sem output)

Ler em paralelo:

1. `skills/_index.md` — catalogo
2. Todos os `skills/*/SKILL.md` existentes — frontmatter + corpo

Objetivo: ter mapa mental completo do que ja existe antes de propor algo novo.

---

## Fase 2 — Coletar proposta

Se o usuario ja descreveu a skill no prompt -> usar essa descricao.
Se nao -> perguntar:

```
Nova skill — me conta:
  1. Nome (kebab-case)
  2. O que ela faz (1-2 linhas)
  3. Quando deve ser disparada (trigger natural na conversa)
```

---

## Fase 3 — DRY check

Pergunta-guia: **existe skill parecida que poderia ser parametrizada em vez de criar nova?**

Verificar para cada skill existente:
- O escopo se sobrepoe?
- Adicionar um parametro/modo na skill existente resolveria sem inflar?
- A diferenca proposta e real ou superficial?

Se DRY falha (skill X poderia ser estendida) -> propor estender X.

---

## Fase 4 — MECE check

Duas perguntas, uma por dimensão:

**Mutuamente exclusiva:** o escopo colide com alguma skill existente?
- Se sim -> DRY já pegou. Reforçar a sugestão de Opção A.

**Coletivamente exaustivo:** essa skill fecha uma lacuna real no registry?
- O registry ficaria menos completo sem ela?
- Há casos de uso recorrentes que hoje ninguém cobre?
- Se não -> skill é supérflua, não criar.

Se a skill passa nos dois critérios -> prosseguir para Fase 5 com Opção B.
Se falha em qualquer um -> propor alternativa (estender existente ou descartar).

---

## Fase 5 — Propor e aguardar confirmacao

Output em um dos dois formatos:

**Opcao A — estender skill existente:**
```
DRY violado: skill `/X` ja cobre [escopo]. Sugestao: adicionar parametro `Y` em `/X`
em vez de criar `/nova`.

Confirma? (s/n)
```

**Opcao B — criar nova:**
```
DRY ok / MECE ok. Criar skill nova:

  nome:        /nome-skill
  description: [1 linha]
  triggers:    [lista]
  fases:       [esqueleto em bullets]

Confirma? (s/n)
```

**Nao prosseguir sem confirmacao explicita.**

---

## Fase 6 — Criar arquivos (so apos confirmacao)

Em uma unica operacao (paralelo), criar:

### 1. `skills/{nome}/SKILL.md`

Estrutura padrao:

```markdown
---
name: {nome}
description: >
  {1-3 linhas explicando o que faz e quando dispara}
  Triggers: "{trigger1}", "/{nome}", "{trigger2}".
---

# /{nome}

{paragrafo de abertura — 1-2 linhas}

---

## Fase 1 — {nome da fase}

{instrucoes}

---

## Fase 2 — {nome da fase}

{instrucoes}

---

## Regras

- {regra 1}
- {regra 2}
```

Usar `## Fase N` quando tem etapas sequenciais (ex: `/cerebro`).
Usar `## Passo N` quando e checklist linear (ex: `/flush` passos 1-8).
Usar bullets sem numeracao quando a skill e curta e nao tem fases.

### 2. `.claude/commands/{nome}.md`

Conteudo fixo:

```
Leia o arquivo `skills/{nome}/SKILL.md` e execute o roteiro descrito nele integralmente.
```

Uma linha so. E o wrapper que liga o slash command ao SKILL.md.

### 3. Atualizar `skills/_index.md`

Adicionar linha na tabela:

```markdown
| `/{nome}` | {descricao curta — mesma vibe das existentes} |
```

Manter ordem logica (skills relacionadas proximas).

---

## Fase 7 — Confirmar

Output final:

```
Skill `/{nome}` criada.

Arquivos:
  skills/{nome}/SKILL.md
  .claude/commands/{nome}.md
  skills/_index.md (entrada adicionada)

Testar: digite `/{nome}` ou um dos triggers naturais.
```

**Nao commitar.** Deixar para o usuario decidir (geralmente cai no `/flush`).

---

## Regras

- **Sempre rodar DRY check antes** — duplicar skill e dificil de reverter
- **Sempre aguardar confirmacao** antes de criar arquivos
- **3 arquivos em paralelo** — nao criar um por vez
- **Wrapper e sempre 1 linha** — nunca embutir logica em `.claude/commands/`
- **Frontmatter obrigatorio** — `name`, `description` com triggers
- **Tom direto** — sem preambulo no output
- **Nao commitar** — fluxo de commit pertence ao `/flush`
