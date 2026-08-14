# [SEU NOME] — Segundo Cerebro

> Este arquivo e o **schema** do meu segundo cerebro. Todo agente (Claude Code, Cursor,
> ChatGPT, Gemini, qualquer LLM) deve ler isto **primeiro** antes de operar no repo.
>
> Baseado no padrao LLM-Wiki do Karpathy + sistema /cerebro do Bruno Okamoto.
> Hibrido entre **conhecimento que compounding** (`wiki/`) e **operacional do dia-a-dia** (`memory/`).

---

## 1. Quem sou eu

<!-- PREENCHA NA ETAPA 1 DO SETUP.md -->
<!-- Exemplo:
Brasileiro, 22 anos, baseado em Sao Paulo. Estudante de engenharia na USP.
Interessado em machine learning e startups deep-tech.
Construindo simultaneamente:
1. **Projeto A** — descricao curta
2. **Projeto B** — descricao curta
-->

[Descreva quem voce e, o que faz, o que esta construindo. 3-5 linhas.]

Prefiro **avaliacao franca a hedging diplomatico**.
Quero ser desafiado, nao bajulado. Quando pedir feedback, de feedback.

---

## 2. Como o repo esta organizado

Quatro camadas, cada uma com ciclo de vida diferente:

| Camada | Caminho | Ciclo | O que vive aqui |
|--------|---------|-------|-----------------|
| **Raw** | `raw/` | imutavel | PDFs, transcripts, papers, screenshots — fontes brutas |
| **Wiki** | `wiki/` | meses/anos | conhecimento compounding: entidades, conceitos, sinteses |
| **Memory** | `memory/` | dias/semanas | operacional: pendencias, projetos ativos, sessoes, deadlines |
| **Skills** | `skills/` | estavel | scripts de automacao (opcional) |

Regra mental: **se a informacao tem prazo de validade, vai em `memory/`. Se vai te servir daqui a 2 anos, vai em `wiki/`.**

---

## 3. Convencoes de arquivos

> **Cada pasta tem um `_index.md` proprio explicando a convencao dela.**

### Wiki
- Uma pagina por entidade/conceito. Nome em `kebab-case.md` (`machine-learning.md`, `joao.md`).
- Frontmatter YAML obrigatorio com `type`, `tags`, `created`, `updated`, `sources`.
- Wikilinks `[[nome-da-pagina]]` para Obsidian compatibility.
- Cross-refs explicitos no final em secao `## Ver tambem`.
- Toda alegacao factual com fonte deve apontar para um arquivo em `wiki/sources/`.

### Memory
- `pendencias.md`, `deadlines.md`, `business-context.md`, `people.md` — singletons, sempre o mesmo arquivo.
- `decisoes/YYYY-MM.md` — um arquivo por mes.
- `projects/{slug}.md` — um arquivo por projeto, slugificado.
- `sessions/YYYY-MM/YYYY-MM-DD.md` — um arquivo por dia, agrupado em subpasta por mes (`2026-06/`). Multiplas sessoes no mesmo dia viram subsecoes `## Sessao HH:MM`.

### Log e indice (raiz)
- `log.md` — append-only, formato `## [YYYY-MM-DD HH:MM] tipo | titulo`.
- `index.md` — catalogo da wiki, atualizado a cada ingest.

---

## 4. Operacoes (workflows)

### Ingest (uma fonte nova)
1. Coloque arquivo em `raw/` (ou cole texto/URL no chat).
2. LLM le a fonte completa.
3. LLM cria/atualiza `wiki/sources/{slug}.md` com resumo + key takeaways + citacao.
4. LLM identifica entidades/conceitos mencionados -> cria/atualiza paginas em `wiki/entities/` e `wiki/concepts/`.
5. LLM atualiza `index.md` (adiciona nova pagina).
6. LLM appende em `log.md`: `## [data] ingest | {titulo da fonte}`.
7. Se a fonte gera acao operacional (deadline, decisao, pendencia) -> tambem atualiza `memory/`.

### Query (perguntar coisa)
1. LLM le `index.md` primeiro para descobrir paginas relevantes.
2. Le so as paginas necessarias (nao a wiki inteira).
3. Responde com citacoes no formato `[wiki/concepts/algo.md]`.
4. Se a resposta foi rica, pergunta: "salvar isto em `wiki/synthesis/`?"

### Lint (saude do segundo cerebro)
Periodicamente (mensal), pedir: *"roda lint na wiki"*. LLM verifica:
- paginas orfas (sem inbound links)
- claims sem fonte
- paginas desatualizadas (>90 dias sem update)
- conceitos mencionados que nao viraram pagina propria

---

## 5. Regras de comportamento para o agente

1. **Antes de criar arquivo novo, sempre checar se ja existe** uma pagina relacionada.
   Atualizar > duplicar.
2. **Wiki e fonte de verdade conceitual.** `business-context.md` em `memory/` e cache compilado;
   se entrar em conflito, a wiki vence.
3. **Nunca sobrescrever sessao existente.** Append, nunca replace.
4. **Commits especificos.** `wiki: adiciona pagina X` melhor que `update`.
5. **Nao inventar fontes.** Se nao tem source no `raw/` ou `wiki/sources/`, marcar como
   `[claim sem fonte]` no texto.
6. **Tom direto.** Sem preambulo, sem "otima pergunta!", sem hedging desnecessario.
7. **Skills prontas neste starter.** `skills/setup` faz o onboarding guiado na primeira vez
   (e `/cerebro` cai nela sozinho se o cerebro estiver vazio). `skills/cerebro`,
   `skills/ingest` e `skills/flush` sao o ciclo do dia a dia — `/cerebro` pra abrir sessao,
   `/ingest` pra processar uma fonte nova, `/flush` pra fechar. `skills/skillify` cria
   skills novas (com DRY/MECE check) e `skills/audit` faz auditoria mensal de saude do
   cerebro. Se o atalho `/` nao estiver
   disponivel, invocar pelo nome em linguagem natural ("liga o cerebro", "ingere isso",
   "fecha a sessao") e seguir o `SKILL.md` correspondente integralmente.

---

## 6. Como usar com diferentes ferramentas

### Claude Code / Cursor / Codex
- Abrir terminal no diretorio do repo. O agente le este CLAUDE.md automaticamente.
- Workflow: ler contexto -> trabalhar -> persistir mudancas.

### ChatGPT / Claude web / Gemini (sem leitura de arquivo)
- Copie o conteudo deste CLAUDE.md + `memory/context/business-context.md` + `memory/context/pendencias.md`.
- Cole como primeira mensagem da conversa.

### Obsidian (browsing pessoal)
- Abrir a pasta como vault.
- Wikilinks `[[]]` funcionam nativamente.
- Graph view mostra a wiki visualmente.

---

## 7. Mapa rapido

| Estou buscando... | Vai aqui |
|-------------------|----------|
| Convencao de uma pasta | `{pasta}/_index.md` |
| O que esta em aberto agora | `memory/context/pendencias.md` |
| Prazos | `memory/context/deadlines.md` |
| Decisoes deste mes | `memory/context/decisoes/YYYY-MM.md` |
| Status de projetos | `memory/projects/_index.md` |
| Contatos / equipe | `memory/context/people.md` |
| Visao panoramica | `memory/context/business-context.md` |
| Conhecimento tecnico | `wiki/concepts/` |
| Pessoas/empresas relevantes | `wiki/entities/` |
| Papers/artigos lidos | `wiki/sources/` |
| Analises e teses minhas | `wiki/synthesis/` |
| Sessoes de trabalho | `memory/sessions/` |
| Catalogo de tudo na wiki | `index.md` (raiz) |
| Historico cronologico | `log.md` (raiz) |

---

## 8. Stack e preferencias

<!-- PREENCHA NA ETAPA 1 DO SETUP.md -->
- **Editor:** [VS Code / Cursor / outro]
- **Git:** [GitHub privado / outro]
- **Idioma default:** [PT-BR / EN / outro]
- **Fuso:** [America/Sao_Paulo / outro]

---

*Criado: YYYY-MM-DD*
*Schema version: 1.0*
