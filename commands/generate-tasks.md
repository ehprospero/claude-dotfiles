---
description: Gera um plano task-a-task (proposta OpenSpec, ou um plano simples) pra uma feature, com um nível de assistência explícito controlando quantas tasks são marcadas owner:agent vs owner:human.
argument-hint: <no|low|high|full> <descrição da feature ou ticket>
---

Interprete `$ARGUMENTS`: a primeira palavra separada por espaço é o **nível de assistência**, tudo depois disso é a **descrição da feature/ticket**.

1. Valide que o nível é um de `no`, `low`, `high`, `full` (sem diferenciar maiúsculas/minúsculas). Se estiver faltando, escrito errado, ou for qualquer outra coisa, pare e pergunte qual dos quatro usar — não chute um default aqui. Esse command existe especificamente pra deixar o nível explícito; se você queria o default, o `krabs` já te dá isso sem precisar desse command.
2. Delegue pro subagente `sandy` (via `Agent`) com exatamente:
   - O nível de assistência, verbatim (em minúsculas).
   - A descrição da feature/ticket, verbatim.
   - O diretório de trabalho atual como o repo alvo.
3. Devolva a saída do sandy como está — proposta/plano mais `tasks.md`, pronto pra revisar. Não implemente nada você mesmo; esse command só planeja.

Esse é o jeito canônico e explícito de gerar uma lista de tasks: é o único ponto de entrada que obriga o nível de assistência a ser declarado em vez de vir por default. O `krabs` chama o `sandy` do mesmo jeito como parte do pipeline completo (com default `high` a menos que seja dito o contrário) — use esse command diretamente quando você só quiser o plano/proposta sem disparar a implementação ainda, ou quando quiser um nível diferente de `high` sem ter que avisar o `krabs` primeiro.
