---
name: guildmaster
description: Guia de uso do plugin claude-agents-toolkit. Explica quais agentes e skills existem, quando usar cada um, em que ordem e como eles se passam informações por arquivos (docs/PROJETO.md, docs/pesquisa-fluxos/, docs/adr/). Use quando o usuário perguntar como usar o toolkit, quais agentes ou skills existem, por onde começar um projeto, ou quando uma tarefa envolver mais de uma peça do toolkit.
---

# Guia do claude-agents-toolkit

Este plugin reúne agentes e skills **universais**: nenhum deles traz contexto de um projeto específico. Todos descobrem o contexto lendo o projeto atual e trocam informações por **arquivos** dentro dele.

Cada peça tem o nome de uma classe de RPG que lembra o seu papel no grupo: o **guildmaster** organiza, o **loremaster** conhece o mundo, o **ranger** explora, o **tactician** planeja, o **blacksmith** constrói e o **inquisitor** fiscaliza.

## 1. Peças disponíveis

| Peça | Tipo | Para que serve | Como chamar |
|---|---|---|---|
| `loremaster` | Agente | Lê o projeto (somente leitura) e gera `docs/PROJETO.md`: guia rápido, regras de negócio com evidência, stack, arquitetura, deploy, problemas conhecidos. Atualiza de forma incremental pelo commit. | "use o loremaster para documentar este projeto" |
| `ranger` | Agente | Pesquisa referências de fluxos e telas (Mobbin, Lazyweb, Refero ou web), salva em `docs/pesquisa-fluxos/<tema>.md` e opcionalmente no Figma. | "use o ranger para pesquisar o fluxo de <x>" |
| `tactician` | Skill | Entrevista inicial do back-end: stack, arquitetura, banco, autenticação, API. Registra as decisões em `docs/adr/`. | `/tactician` |
| `blacksmith` | Skill | Regras ao programar back-end: segurança, legibilidade, desempenho, testes, seguindo os ADRs. | Carrega sozinha ao codar back-end, ou `/blacksmith` |
| `inquisitor` | Agente | Revisa o código de back-end pronto (segurança, desempenho, ADRs, testes). Somente leitura. | "use o inquisitor nas mudanças" |
| `guildmaster` | Skill | Este guia. | `/guildmaster` |

## 2. Arquivos de passagem (a "memória" compartilhada)

| Arquivo | Quem escreve | Quem lê |
|---|---|---|
| `docs/PROJETO.md` | loremaster | ranger, tactician, blacksmith, inquisitor |
| `docs/pesquisa-fluxos/<tema>.md` | ranger | tactician, blacksmith (para saber que endpoints as telas precisam) |
| `docs/adr/*.md` | tactician (e blacksmith, ao registrar novas decisões) | blacksmith, inquisitor, loremaster |

Regra: antes de começar qualquer tarefa com estas peças, verifique se esses arquivos existem e leia os que forem relevantes. Eles evitam perguntas repetidas ao usuário.

## 3. Fluxos de trabalho recomendados

### Projeto novo
1. `/tactician`: define stack e arquitetura com o usuário e cria `docs/adr/`.
2. (Se houver telas) **ranger**: pesquisa referências dos fluxos principais.
3. Implementação no chat principal, com a **blacksmith** ativa.
4. **inquisitor** ao fim de cada funcionalidade.
5. **loremaster** quando o projeto tiver corpo, para gerar `docs/PROJETO.md`.

### Projeto existente que você ainda não conhece
1. **loremaster**: gera `docs/PROJETO.md`.
2. `/tactician`: registra a stack e a arquitetura existentes como ADRs e define só o que falta.
3. Daí em diante, igual ao projeto novo (passos 3 e 4).

### Nova funcionalidade com tela
1. **ranger**: referências do fluxo.
2. O usuário decide no chat principal o que adotar.
3. Implementação com a **blacksmith** (endpoints) e o front-end do projeto.
4. **inquisitor**.
5. **loremaster** em modo incremental, para atualizar `docs/PROJETO.md`.

## 4. Regras para o agente principal

- **ranger faz perguntas por pausa e retomada.** Quando ele retornar `STATUS: AGUARDANDO_RESPOSTA`, mostre as perguntas ao usuário exatamente como vieram (com `AskUserQuestion`), sem responder por ele, e retome **o mesmo agente** com `SendMessage` enviando as respostas literais. Repita até `STATUS: CONCLUIDO`.
- **loremaster é imparcial.** No briefing, envie somente escopo, formato e local de saída. Não inclua resumos ou opiniões sobre o sistema.
- **inquisitor** recebe o escopo da revisão (mudanças não commitadas, um branch ou pastas). Repasse ao usuário os achados Críticos e Altos antes de seguir.
- Skills (`tactician`, `blacksmith`) rodam no chat principal e podem perguntar diretamente ao usuário.

## 5. Mantendo este guia

Sempre que um agente ou skill for adicionado, alterado ou removido do plugin, atualize as seções 1 a 4 deste arquivo e a tabela do `README.md` do repositório.
