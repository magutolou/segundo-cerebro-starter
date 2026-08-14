# Segundo Cerebro — Starter

Um repositorio de conhecimento pessoal que qualquer AI (Claude Code, Cursor, ChatGPT,
Gemini) consegue ler pra te ajudar com contexto real sobre quem voce e, o que voce
esta fazendo e o que voce ja aprendeu.

Sao so arquivos markdown. Nao tem app, nao tem login, nao tem servidor. Voce e dono
de tudo e funciona offline.

---

## Comeco rapido

```bash
git clone https://github.com/magutolou/segundo-cerebro-starter.git segundo-cerebro
cd segundo-cerebro
```

Depois abra o Claude Code nessa pasta e rode:

```
/setup
```

A AI conduz o resto: pergunta uma coisa por vez, preenche seu perfil, mapeia seus
projetos e monta o cerebro inteiro com voce. Uns 15-20 minutos.

**Nunca instalou o Claude Code, ou nao usa terminal?** Comece pelo
[SETUP.md](SETUP.md) — a Etapa 0 cobre a instalacao do zero (Git, Node, Claude Code,
GitHub) passo a passo, e as etapas 1 a 4 dao a versao manual pra quem so tem
ChatGPT/Claude web.

> **Importante logo apos clonar:** apague a pasta `.git` e rode `git init` de novo,
> pra o repositorio ser seu e nao ficar amarrado a quem te passou o starter. Esta
> explicado na Etapa 0e do [SETUP.md](SETUP.md).

---

## Como esta organizado

| Camada | Ciclo de vida | O que vive aqui |
|--------|---------------|-----------------|
| `raw/` | imutavel | fontes brutas: PDFs, transcripts, screenshots |
| `wiki/` | meses/anos | conhecimento que acumula: conceitos, entidades, sinteses |
| `memory/` | dias/semanas | operacional: pendencias, deadlines, projetos, sessoes |
| `skills/` | estavel | automacoes que a AI executa |

Regra mental: **se a informacao tem prazo de validade, vai em `memory/`. Se vai te
servir daqui a 2 anos, vai em `wiki/`.**

O [CLAUDE.md](CLAUDE.md) e o schema que a AI le primeiro — ele explica as convencoes
de cada pasta e como a AI deve se comportar no repo. Cada pasta tem tambem um
`_index.md` proprio detalhando a convencao dela.

---

## As skills

| Skill | Quando | O que faz |
|-------|--------|-----------|
| `/setup` | uma vez | onboarding guiado, preenche o cerebro conversando |
| `/cerebro` | inicio de sessao | briefing de pendencias, deadlines, projetos |
| `/ingest` | ao ler algo bom | vira source page + atualiza conceitos, index e log |
| `/flush` | fim de sessao | persiste em memory/wiki, cria a nota do dia, commita |
| `/skillify` | sob demanda | cria uma skill nova com DRY/MECE check |
| `/audit` | mensal | auditoria de saude do cerebro |

Detalhes de cada uma em [skills/_index.md](skills/_index.md). Se o atalho `/nome` nao
funcionar, chame pelo nome em linguagem natural ("liga o cerebro", "ingere isso",
"fecha a sessao").

Sem Claude Code, as skills ainda servem: cada `SKILL.md` e um roteiro que voce pode
colar no prompt de qualquer AI.

---

## Precisa saber programar?

Nao. O cerebro e so markdown. Git e a unica parte "tecnica", e da pra usar o GitHub
Desktop se voce nao gosta de terminal. O [SETUP.md](SETUP.md) cobre os dois caminhos.

---

*Template do sistema segundo-cerebro, criado por Guilherme. Faca dele o que quiser.*
