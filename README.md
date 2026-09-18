# push-safe

Subconjunto curado dos meus dotfiles do Claude Code — só os agentes e o comando
que fazem sentido fora da minha máquina.

Tema Bob Esponja: um time de subagentes que leva uma feature da descrição até
código testado e commitado.

## Conteúdo

```
agents/
  krabs.md       # orquestrador — coordena sandy → spongebob → squidward
  sandy.md       # planner — transforma descrição/ticket em plano de tasks
  spongebob.md   # implementer — implementa uma task: teste primeiro, código, lint/build, commit
  squidward.md   # tester — verifica adversarialmente o trabalho do spongebob
commands/
  generate-tasks.md   # /generate-tasks <no|low|high|full> <descrição> — só gera o plano, sem implementar
```

### Agentes

- **krabs** — Executa uma feature de ponta a ponta delegando para `sandy` → `spongebob` → `squidward`
  em sequência. Em repo com OpenSpec, impõe parada obrigatória para revisão humana da proposta antes
  de qualquer código, e arquiva a mudança quando tudo passa. Use para features/correções grandes o
  suficiente para justificar planejamento e verificação independente; não use para uma correção de
  uma linha ou uma pergunta simples.
- **sandy** — Transforma uma descrição de feature/ticket num plano de implementação concreto (e, em
  repo com OpenSpec, conduz o fluxo de `propose`: proposal.md + specs delta + design.md + tasks.md).
  Recebe um **nível de assistência** (`no`/`low`/`high`/`full`) que controla quantas tasks saem
  marcadas `owner:agent` vs `owner:human`.
- **spongebob** — Implementa uma task escopada de um plano (ou uma instrução direta pequena) de ponta
  a ponta: teste primeiro, código mínimo pra passar, lint/build, commit. Melhor usado uma task por vez.
- **squidward** — Verifica de forma adversarial uma implementação contra seu critério de aceite: roda
  a suíte de teste relevante + lint/typecheck/build, escreve testes extra pra bordas que o spongebob
  provavelmente deixou passar. Reporta passa/falha; nunca corrige código de produção sozinho — isso
  volta pro spongebob.

### Comando

- **/generate-tasks `<no|low|high|full>` `<descrição da feature ou ticket>`** — gera só o plano
  (proposta OpenSpec ou plano simples) delegando pro `sandy`, sem disparar implementação. O nível de
  assistência é obrigatório aqui (o command para e pergunta se faltar) — é o ponto de entrada explícito
  quando você não quer o default `high` que o `krabs` usa.

## Como usar

Copie (ou symlink) os arquivos para as pastas globais do Claude Code:

```bash
cp agents/*.md ~/.claude/agents/
cp commands/*.md ~/.claude/commands/
```

Ou, se preferir manter isso versionado e symlinkado (como no meu setup principal):

```bash
ln -s "$(pwd)/agents"/*.md ~/.claude/agents/
ln -s "$(pwd)/commands"/*.md ~/.claude/commands/
```

Depois disso, disponíveis em qualquer sessão do Claude Code:

```
# plano completo, com implementação e verificação:
Agent(subagent_type: "krabs", prompt: "<descrição da feature/ticket>")

# só o plano, nível de assistência explícito:
/generate-tasks high <descrição da feature/ticket>
```
