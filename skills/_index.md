# skills/

Scripts de automacao para Claude Code (ou outras ferramentas com suporte a skills).

## O que ja vem pronto

Primeira vez (1 skill):

- **`setup/`** — onboarding guiado e conversacional. Pergunta uma coisa por vez, monta
  os textos por voce e preenche o cerebro inteiro: perfil no `CLAUDE.md`, projetos,
  pendencias, deadlines, business context e a primeira ingestao. Cada arquivo so e
  salvo depois da sua aprovacao. Substitui as etapas 1-4 do `SETUP.md`.

Uso: `/setup` uma unica vez, na configuracao inicial. `/cerebro` num cerebro ainda
vazio cai automaticamente nela.

Nucleo do ciclo de trabalho (3 skills):

- **`cerebro/`** — liga a sessao. Carrega `memory/` + catalogo da `wiki/` e da um
  briefing compacto (pendencias, deadlines, projetos, ultimos 7 dias). Detecta
  automaticamente se o cerebro ainda nao foi configurado.
- **`ingest/`** — processa uma fonte nova (paper, artigo, podcast, video) e integra
  na wiki: cria source page, atualiza conceitos/entidades relacionados, atualiza
  `index.md` e `log.md`. Inclui pipeline de captura de transcript pra YouTube/Instagram.
- **`flush/`** — fecha a sessao. Revisa o que aconteceu, atualiza `memory/` e `wiki/`,
  cria a entrada do dia em `memory/sessions/`, atualiza `log.md`, e commita.

Uso: `/cerebro` no inicio, `/ingest` quando ler algo, `/flush` no fim. Sem o atalho
`/`, chame pelo nome em linguagem natural ("liga o cerebro", "ingere isso", "fecha
a sessao").

Manutencao do cerebro (2 skills):

- **`skillify/`** — meta-skill pra criar skills novas. Faz DRY check (ja existe algo
  parecido?) e MECE check (fecha lacuna real?) antes de gerar os 3 arquivos
  (`SKILL.md` + wrapper `.claude/commands/` + entrada no indice). Substitui escrever
  `SKILL.md` na mao.
- **`audit/`** — auditoria de governanca do cerebro. Varre `wiki/` e `memory/` por
  links quebrados, indices dessincronizados, pendencias mortas e drift de estrutura.
  So propoe, nunca aplica sozinho — cada achado espera aprovacao.

Uso: `/skillify` sob demanda, quando quiser criar uma skill nova (nao tem
periodicidade — so quando surge a necessidade). `/audit` **mensal**, ou logo
apos qualquer reorganizacao grande de pastas/arquivos.

## O que NAO vem (mas pode ser adicionado)

- `rotina` — cockpit diario mais robusto, com Top 3 do dia (melhor com MCP de
  Gmail/Calendar).
- Qualquer skill do seu contexto especifico (rotina de estudo, pipeline de trabalho,
  post recorrente). Crie com `/skillify` em vez de escrever o `SKILL.md` na mao.

## Se voce NAO usa Claude Code

Pode ignorar a automacao. Os workflows do CLAUDE.md funcionam manualmente —
voce so pede para a AI fazer via prompt em vez de rodar um comando. Os `SKILL.md`
dentro de cada pasta ainda servem como roteiro pra colar no prompt.

## Como criar uma skill nova (Claude Code)

Jeito curto: rode `/skillify` e responda as perguntas. Ele gera os 3 arquivos
necessarios de uma vez. O resto desta secao e so pra entender o mecanismo.

Cada skill e uma pasta com um `SKILL.md` dentro:
```
skills/
  ingest/
    SKILL.md
```

O `SKILL.md` contem as instrucoes que o Claude Code segue quando voce roda `/nome-da-skill`.
