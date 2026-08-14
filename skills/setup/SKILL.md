---
name: setup
description: >
  Setup guiado do segundo cerebro. A AI conduz a conversa, faz perguntas,
  cria e preenche todos os arquivos. O usuario so responde.
  Detectado automaticamente quando o cerebro esta vazio.
  Triggers: "setup", "/setup", "configura o cerebro", "primeira vez".
---

# /setup

Setup guiado e conversacional. Voce conduz, o usuario so responde.
Ao final, o cerebro esta 100% funcional — CLAUDE.md preenchido, projetos mapeados,
contexto operacional pronto, primeira ingestao feita.

---

## Principios

- **Uma pergunta por vez.** Nunca despeje 10 perguntas de uma so vez.
- **Crie arquivos conforme avanca.** Nao espere o final — a cada fase, persista o que coletou.
- **Confirme antes de gravar.** Mostre o que vai escrever, pergunte "ficou bom?", so entao salve.
- **Linguagem casual.** Trate como onboarding de produto, nao como formulario de imposto.
- **Se o usuario der resposta curta, expanda voce.** Ele diz "startup de AI" → voce monta o paragrafo e pede confirmacao.
- **Pule o que nao se aplica.** Se ele nao trabalha com ninguem, pule people.md. Se nao tem deadlines, pule deadlines.md.

---

## Deteccao automatica

Se `/cerebro` detectar que o cerebro nao esta configurado (arquivos sao template puro),
redirecionar para esta skill automaticamente.

Indicadores de "nao configurado":
- `CLAUDE.md` secao 1 contem `[Descreva quem voce e`
- `memory/context/business-context.md` contem `[Quem voce e`
- `memory/projects/_index.md` nao tem nenhum projeto real

---

## Fase 0 — Boas-vindas

```
Esse e o seu segundo cerebro — um repositorio que qualquer AI pode ler para
te ajudar com contexto completo sobre quem voce e, o que esta fazendo e o
que sabe.

Vou te guiar pelo setup. Eu pergunto, voce responde, e eu vou montando tudo.
Leva uns 15-20 minutos. No final, tudo fica pronto pra usar.

Vamos comecar?
```

Aguardar confirmacao. Qualquer resposta afirmativa (ou apenas Enter) → prosseguir.

---

## Fase 1 — Perfil pessoal (→ CLAUDE.md secao 1)

Perguntar **uma por vez**, em sequencia natural:

1. "Como voce se chama?"
2. "Quantos anos voce tem e onde mora?"
3. "O que voce faz? (estuda, trabalha, empreende... pode ser mais de uma coisa)"
4. "Em que areas voce quer construir conhecimento? (ex: programacao, financas, medicina, design, marketing...)"
5. "Qual seu nivel tecnico com programacao? (nunca programei / basico / intermediario / avancado)"
6. "Qual idioma prefere para conversas? E para codigo/nomes tecnicos?"

Apos coletar, **montar o texto e mostrar**:

```
Montei seu perfil:

---
[texto gerado]
---

Ficou bom? Quer mudar algo?
```

Se aprovado:
- Atualizar `CLAUDE.md` secao 1 com o perfil
- Atualizar `CLAUDE.md` secao 8 (stack) com idioma e nivel tecnico
- Atualizar titulo do `CLAUDE.md` de `[SEU NOME]` para o nome real

---

## Fase 2 — Projetos (→ memory/projects/)

```
Agora vamos mapear seus projetos — tudo que voce esta construindo,
estudando, ou planejando. Pode ser trabalho, projeto pessoal, estudo,
qualquer coisa.

Me conta o primeiro. Qual o nome e o que e?
```

Para cada projeto, perguntar:

1. "O que e esse projeto em 2-3 frases?"
2. "Qual o status? (ativo / em construcao / so explorando a ideia / pausado)"
3. "Tem mais alguem envolvido ou e so voce?"
4. "Tem algum prazo ou proximo marco importante?"
5. "Qual a principal pendencia agora?"

Apos capturar cada projeto:
- Criar `memory/projects/{slug}.md` com template preenchido
- Mostrar: "Salvei [projeto]. Tem mais algum projeto?"

Repetir ate o usuario dizer que acabou.

Ao finalizar:
- Atualizar `memory/projects/_index.md` com todos os projetos nas tabelas corretas
- Atualizar CLAUDE.md secao 1 se os projetos adicionam contexto relevante ao perfil

---

## Fase 3 — Contexto operacional (→ memory/context/)

### 3a. Pendencias

```
O que esta em aberto na sua vida agora? Tarefas, coisas que voce precisa
resolver. Pode despejar tudo — eu organizo por urgencia depois.
```

Coletar lista. Classificar em criticas / importantes / backlog.
Mostrar a classificacao: "Organizei assim. Faz sentido?"

Se aprovado → salvar `memory/context/pendencias.md`.

### 3b. Deadlines

```
Tem algum prazo importante nos proximos meses?
(provas, entregas, lancamentos, viagens, eventos...)
```

Se sim → preencher `memory/context/deadlines.md`.
Se nao → deixar arquivo vazio com header.

### 3c. People

```
Voce trabalha com mais alguem nos seus projetos?
Se sim, me conta quem — nome, papel, e se tem alguma acao pendente com a pessoa.
```

Se sim → preencher `memory/context/people.md`.
Se nao → pular (deixar template).

### 3d. Business Context

Gerar automaticamente baseado em tudo que coletou nas fases 1-3.
Mostrar para aprovacao.

Se aprovado → salvar `memory/context/business-context.md`.

---

## Fase 4 — Primeira ingestao (→ wiki/)

```
Agora vamos testar o sistema de conhecimento. Pensa em algo que voce leu
ou assistiu recentemente que valeu a pena — um artigo, video, podcast,
paper, livro... qualquer coisa.

Pode me mandar o link, colar o texto, ou me dizer o nome que eu busco.
```

Se o usuario fornecer fonte:
- Rodar o pipeline de `/ingest` completo
- Ao final: "Pronto, sua primeira fonte esta na wiki. A partir de agora,
  toda vez que ler algo bom, e so rodar `/ingest`."

Se o usuario nao tiver fonte agora:
- "Sem problema. Quando tiver algo, roda `/ingest` ou me manda o link."
- Pular para proxima fase.

---

## Fase 5 — Ferramentas e preferencias (→ CLAUDE.md secao 8)

```
Ultimas perguntas rapidas:
- Que editor voce usa? (VS Code, Cursor, outro)
- Usa Obsidian pra visualizar notas?
- Tem GitHub configurado pra backup?
```

Atualizar `CLAUDE.md` secao 8 com as respostas.

Se usa Obsidian → adicionar dica: "Abre essa pasta como vault no Obsidian.
Wikilinks e graph view ja funcionam."

Se tem GitHub → orientar a criar repo privado e dar o push inicial.

---

## Fase 6 — Fechamento

### Atualizar arquivos finais

- `log.md` → append primeira entrada real: `## [data] setup | segundo cerebro configurado`
- `index.md` → atualizar se houve ingest na Fase 4
- `AGENTS.md` → atualizar para referenciar o CLAUDE.md preenchido

### Commit inicial

```bash
git add .
git commit -m "setup: segundo cerebro configurado"
```

Se o usuario tem GitHub configurado, perguntar se quer push.

### Mensagem final

```
Pronto! Seu segundo cerebro esta configurado.

Resumo:
  - Perfil preenchido em CLAUDE.md
  - N projetos mapeados
  - Pendencias e deadlines registrados
  - [Se fez ingest] Primeira fonte ingerida na wiki
  - [Se tem Obsidian] Abra a pasta como vault pra ver o graph

Como usar no dia a dia:
  /cerebro  → carrega contexto no inicio da sessao
  /ingest   → quando ler algo novo
  /flush    → antes de fechar a sessao
  /skillify → quando quiser criar uma skill nova
  /audit    → uma vez por mes, saude do cerebro

Qualquer AI que abrir esse repo vai saber quem voce e e o que esta fazendo.
Quanto mais voce usa, mais inteligente fica.
```

---

## Regras

- **Nunca criar arquivo sem mostrar e aprovar.** O usuario deve ver o que vai ser salvo.
- **Uma fase por vez.** Nao pule fases. Se o usuario quiser pular, tudo bem — pergunte antes.
- **Seja conversacional.** Isso e onboarding, nao documentacao.
- **Se o usuario parecer perdido, de exemplo.** "Por exemplo: 'estou montando um app de receitas com meu amigo Pedro, a gente quer lancar em agosto'."
- **Adapte a profundidade.** Se o usuario tem 2 projetos, vai rapido. Se tem 8, tenha paciencia.
- **Tom direto, sem ser frio.** Casual mas eficiente.
