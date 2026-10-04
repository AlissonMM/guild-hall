---
name: guildmaster
description: Guia de uso do plugin guild-hall. Explica quais agentes e skills existem, quando usar cada um, em que ordem e como eles se passam informações por arquivos (docs/PROJETO.md, docs/pesquisa-fluxos/, docs/adr/, docs/design-system.md). Use quando o usuário perguntar como usar o Guild Hall (ou o toolkit), quais agentes ou skills existem, por onde começar um projeto, ou quando uma tarefa envolver mais de uma peça do toolkit.
---

# Guia do Guild Hall

Este plugin reúne agentes e skills **universais**: nenhum deles traz contexto de um projeto específico. Todos descobrem o contexto lendo o projeto atual e trocam informações por **arquivos** dentro dele.

Cada peça tem o nome de uma classe de RPG que lembra o seu papel no grupo: o **guildmaster** organiza, o **loremaster** conhece o mundo, o **ranger** explora, o **tactician** planeja, o **blacksmith** forja o back-end, o **enchanter** dá forma ao front-end, o **summoner** invoca o projeto para o campo (local ou produção), o **seer** enxerga o resultado e o **inquisitor** fiscaliza.

## 1. Peças disponíveis

| Peça | Tipo | Para que serve | Como chamar |
|---|---|---|---|
| `guildmaster` | Skill | Este guia. | `/guildmaster` |
| `loremaster` | Agente | Lê o projeto (somente leitura) e mantém `docs/PROJETO.md`: guia rápido, regras de negócio com evidência, stack, arquitetura, deploy, problemas conhecidos. Funciona em qualquer fase: separa o que está Planejado, Em construção e Implementado, inclui mudanças não commitadas e, a cada chamada, atualiza só o que mudou e relata o que o documento ainda não reflete. | "use o loremaster para documentar este projeto" |
| `ranger` | Agente | Pesquisa referências de fluxos e telas (Mobbin, Lazyweb, Refero ou web), salva em `docs/pesquisa-fluxos/<tema>.md` e opcionalmente no Figma. | "use o ranger para pesquisar o fluxo de <x>" |
| `tactician` | Skill | **Modo A**: planejamento técnico de back-end e front-end (stack, arquitetura, banco, autenticação, API, estilização, estado, testes), com ADRs em `docs/adr/`. **Modo B**: design system em `docs/design-system.md` e página de referência publicada como Artifact no estilo do produto. | `/tactician` (diga se é planejamento ou design system) |
| `blacksmith` | Skill | Regras ao programar back-end: segurança, legibilidade, desempenho, testes, seguindo os ADRs. | Carrega sozinha ao codar back-end, ou `/blacksmith` |
| `enchanter` | Skill | Regras ao programar front-end: design system, acessibilidade, responsividade, estados de tela, desempenho, segurança, testes. | Carrega sozinha ao codar front-end, ou `/enchanter` |
| `summoner` | Skill | Faz o projeto rodar: diagnostica a máquina, recomenda Docker Compose, híbrido ou nativo, cria Dockerfiles/compose/scripts, sobe e verifica os serviços e gera a skill de startup do projeto (`.claude/skills/startup-<projeto>/`). Também planeja e prepara o deploy. | `/summoner` ("suba o projeto", "faça o deploy") |
| `seer` | Agente | Abre o app no navegador, percorre um fluxo em celular e desktop e compara com o design system e as referências. Não altera código. | "use o seer no fluxo de <x> em http://localhost:<porta>" |
| `inquisitor` | Agente | Revisa o código pronto de back-end e front-end (segurança, acessibilidade, desempenho, ADRs, design system, testes). Somente leitura. | "use o inquisitor nas mudanças" |

## 2. Arquivos de passagem (a "memória" compartilhada)

| Arquivo | Quem escreve | Quem lê |
|---|---|---|
| `docs/PROJETO.md` | loremaster (a qualquer momento do projeto) | todos os outros |
| `docs/pesquisa-fluxos/<tema>.md` | ranger | tactician (design system), enchanter, seer, blacksmith (endpoints que as telas precisam) |
| `docs/adr/*.md` | tactician (e blacksmith/enchanter, ao registrar novas decisões) | blacksmith, enchanter, seer, inquisitor, loremaster |
| `docs/design-system.md` (+ página Artifact) | tactician (Modo B) | enchanter, seer, inquisitor |
| `docker-compose.yml`, `.env.example`, `.claude/skills/startup-<projeto>/`, `docs/deploy.md` | summoner | seer (URLs do app rodando), loremaster |

Regra: antes de começar qualquer tarefa com estas peças, verifique se esses arquivos existem e leia os que forem relevantes. Eles evitam perguntas repetidas ao usuário.

## 3. Fluxos de trabalho recomendados

### Projeto novo
1. `/tactician` (Modo A): define stack e arquitetura de back-end e front-end e cria `docs/adr/`.
   Opcional: **loremaster** logo depois, para começar `docs/PROJETO.md` a partir do plano.
2. **ranger**: pesquisa os fluxos principais do aplicativo.
3. `/tactician` (Modo B): cria o design system a partir dos fluxos pesquisados e publica a página de referência.
4. Implementação no chat principal: **blacksmith** no back-end, **enchanter** no front-end.
5. `/summoner`: sobe o projeto localmente (e gera a skill de startup) para testar.
6. Ao fim de cada funcionalidade: **seer** (verificação no navegador), **inquisitor** (revisão do código) e **loremaster** (atualiza a documentação).
7. Quando for publicar: `/summoner` (deploy).
8. **loremaster** antes de publicar, para revisar o documento inteiro e as divergências entre plano e código.

### Projeto existente que você ainda não conhece
1. **loremaster**: gera `docs/PROJETO.md`.
2. `/tactician` (Modo A): registra a stack e a arquitetura existentes como ADRs e define só o que falta.
3. `/tactician` (Modo B), se não houver design system: extrai o estilo já implementado no app.
4. `/summoner`: aprende a subir o projeto e gera a skill de startup.
5. Daí em diante, igual ao projeto novo (passos 4 a 7).

### Nova funcionalidade com tela
1. **ranger**: referências do fluxo.
2. O usuário decide no chat principal o que adotar.
3. Implementação com **blacksmith** (endpoints) e **enchanter** (telas).
4. **seer** e **inquisitor**.
5. **loremaster** em modo incremental, para atualizar `docs/PROJETO.md`.

## 4. Regras para o agente principal

- **ranger faz perguntas por pausa e retomada.** Quando ele retornar `STATUS: AGUARDANDO_RESPOSTA`, mostre as perguntas ao usuário exatamente como vieram (com `AskUserQuestion`), sem responder por ele, e retome **o mesmo agente** com `SendMessage` enviando as respostas literais. Repita até `STATUS: CONCLUIDO`.
- **loremaster é imparcial e pode ser chamado a qualquer momento.** No briefing, envie somente escopo, formato e local de saída. Não inclua resumos ou opiniões sobre o sistema. Repasse ao usuário o que mudou no documento e o que ele ainda não reflete.
- **seer precisa do app rodando.** No briefing, envie a URL, o fluxo a verificar e os dados de teste. Ele não sobe o projeto sozinho: antes, use a skill de startup do projeto ou o `/summoner`.
- **inquisitor** recebe o escopo da revisão (mudanças não commitadas, um branch ou pastas). Repasse ao usuário os achados Críticos e Altos antes de seguir.
- Skills (`tactician`, `blacksmith`, `enchanter`, `summoner`) rodam no chat principal e podem perguntar diretamente ao usuário.

## 5. Mantendo este guia

Sempre que um agente ou skill for adicionado, alterado ou removido do plugin, atualize as seções 1 a 4 deste arquivo, o `README.md` (que tem todo o texto da página) e a página `index.html` (na raiz) (que também está publicada como Artifact do Claude).
