# Guild Hall

[🇬🇧 English](README.md) · 🇧🇷 **Português**

Plugin do Claude Code · v0.2.1

Nove agentes e skills do Claude organizados como classes de RPG. Cada classe tem um papel no grupo, e elas trocam informações por arquivos dentro do seu projeto, para ninguém perguntar a mesma coisa duas vezes.

Versão visual desta página: abra [`index.html`](index.html) no navegador (ou a página publicada no Claude: https://claude.ai/artifact/KUe63e4HmUWxtKqdUs5bix).

## Recrutar a guilda

```
/plugin marketplace add AlissonMM/guild-hall
/plugin install guild-hall@alissonmm-toolkit
```

Rode dentro do `claude` no terminal, ou use **+ › Plugins** no app desktop. Depois, abra uma conversa nova e comece por `/guildmaster`.

Para atualizar depois de uma mudança no repositório: `claude plugin update guild-hall@alissonmm-toolkit` (ou **Update now** no painel de plugins) e abra uma conversa nova.

## As classes

Skills rodam no chat principal e podem conversar com você. Agentes trabalham isolados e devolvem só o resultado.

| Classe | Tipo | Papel | Esmalte |
|---|---|---|---|
| Guildmaster | Skill | organiza | Or (ouro) |
| Loremaster | Agente | conhece o mundo | Azure (azul) |
| Ranger | Agente | explora | Vert (verde) |
| Tactician | Skill | planeja | Purpure (púrpura) |
| Blacksmith | Skill | forja o back-end | Sable (negro) |
| Enchanter | Skill | dá forma ao front-end | Murrey (amora) |
| Summoner | Skill | invoca o projeto | Tenné (laranja) |
| Seer | Agente | enxerga o resultado | Celeste (azul-céu) |
| Inquisitor | Agente | fiscaliza | Gules (vermelho) |

### Guildmaster · Skill · organiza
Explica como o grupo trabalha: o que cada classe faz, em que ordem chamar e quais arquivos uma passa para a outra.
- **Chamar:** `/guildmaster`
- **Escreve:** nada

### Loremaster · Agente · conhece o mundo
Lê o projeto sem opinar e mantém o documento de referência em qualquer fase: o que está planejado, em construção e implementado, regras de negócio com evidência (arquivo:linha), stack, deploy e problemas conhecidos. A cada chamada, conta o que mudou.
- **Chamar:** "use o loremaster para documentar este projeto"
- **Escreve:** `docs/PROJETO.md`

### Ranger · Agente · explora
Pesquisa fluxos e telas de apps reais no Mobbin, Lazyweb, Refero ou na web e salva as referências, também no Figma se você quiser. Pausa para perguntar e é retomado pelo chat principal.
- **Chamar:** "use o ranger para pesquisar o fluxo de checkout"
- **Escreve:** `docs/pesquisa-fluxos/`

### Tactician · Skill · planeja
Modo A: stack, arquitetura e decisões de back-end e front-end, sempre recomendando uma opção. Modo B: design system com página de referência publicada no estilo do próprio produto.
- **Chamar:** `/tactician`
- **Escreve:** `docs/adr/`, `docs/design-system.md`

### Blacksmith · Skill · forja o back-end
Regras ao programar back-end: segurança (OWASP), legibilidade, desempenho, migrações e testes. Pergunta só sobre decisões caras de desfazer.
- **Chamar:** carrega sozinha, ou `/blacksmith`
- **Escreve:** código e novos ADRs

### Enchanter · Skill · dá forma ao front-end
Regras ao programar front-end: só tokens do design system, acessibilidade WCAG 2.2 AA, mobile-first, estados de tela, desempenho e segurança contra XSS.
- **Chamar:** carrega sozinha, ou `/enchanter`
- **Escreve:** código e novos ADRs

### Summoner · Skill · invoca o projeto
Diagnostica a máquina, recomenda Docker Compose, híbrido ou nativo, cria o que faltar, sobe tudo e gera a skill de startup do projeto. Também prepara o deploy.
- **Chamar:** `/summoner` · "suba o projeto"
- **Escreve:** `docker-compose.yml`, `.claude/skills/startup-*`, `docs/deploy.md`

### Seer · Agente · enxerga o resultado
Abre o app no navegador, percorre o fluxo em 375px e 1280px e compara com o design system e as referências. Aponta erros de console, rede e acessibilidade.
- **Chamar:** "use o seer no fluxo de login em http://localhost:4200"
- **Escreve:** só o relatório

### Inquisitor · Agente · fiscaliza
Revisa o código pronto de back-end e front-end: segurança, acessibilidade, desempenho, ADRs, design system e testes. Entrega os achados por severidade e um veredito.
- **Chamar:** "use o inquisitor nas mudanças"
- **Escreve:** só o relatório

## A campanha

A ordem recomendada para um projeto novo. Cada passo deixa um arquivo que o próximo lê.

1. **Tactician · Modo A**: define stack e arquitetura de back-end e front-end com você e registra cada decisão em `docs/adr/`.
2. **Ranger**: pesquisa como apps conhecidos resolvem os fluxos principais do seu produto.
3. **Tactician · Modo B**: cria o design system a partir dos fluxos pesquisados e publica a página de referência.
4. **Blacksmith e Enchanter**: você e o chat principal constroem o back-end e as telas seguindo os ADRs e o design system.
5. **Summoner**: sobe o projeto na sua máquina e gera a skill de startup para as próximas vezes.
6. **Seer, Inquisitor e Loremaster**: a cada funcionalidade, verificação no navegador, revisão do código e atualização da documentação.
7. **Summoner · deploy**: quando for publicar, escolhe o destino, prepara HTTPS, segredos e backups, e faz o deploy com a sua confirmação.
8. **Loremaster**: antes de publicar, revisa o documento inteiro e aponta as divergências entre plano e código. Ele também pode ser chamado logo no início, para documentar o plano.

### Projeto existente que você não conhece
1. **Loremaster** documenta o projeto.
2. **Tactician A** registra o que já existe como ADR.
3. **Tactician B** extrai o estilo atual, se não houver design system.
4. **Summoner** aprende a subir o projeto.
5. Segue como na campanha, a partir do passo 4.

### Nova funcionalidade com tela
1. **Ranger** traz as referências.
2. Você decide o que adotar.
3. **Blacksmith** e **Enchanter** constroem.
4. **Seer** e **Inquisitor** conferem.
5. **Loremaster** atualiza a documentação.

## Os pergaminhos

Os arquivos que as classes deixam no seu projeto. São a memória compartilhada do grupo.

| Arquivo | Quem escreve | Quem lê |
|---|---|---|
| `docs/PROJETO.md` | Loremaster | todas as outras classes |
| `docs/pesquisa-fluxos/<tema>.md` | Ranger | Tactician, Enchanter, Seer, Blacksmith |
| `docs/adr/*.md` | Tactician, Blacksmith, Enchanter | Blacksmith, Enchanter, Seer, Inquisitor, Loremaster |
| `docs/design-system.md` + página | Tactician | Enchanter, Seer, Inquisitor |
| `docker-compose.yml`, `.claude/skills/startup-*`, `docs/deploy.md` | Summoner | Seer (URLs do app rodando), Loremaster |

## Regras da guilda

Combinados que o chat principal segue ao chamar os agentes.

- **O ranger pausa.** Quando ele responder `STATUS: AGUARDANDO_RESPOSTA`, as perguntas vão para você exatamente como vieram, e o mesmo agente é retomado com as suas respostas.
- **O loremaster é imparcial.** O briefing leva só escopo, formato e local de saída. Nenhum resumo ou opinião sobre o sistema. Pode ser chamado em qualquer fase do projeto.
- **O seer precisa do app de pé.** Ele recebe a URL, o fluxo e os dados de teste. Se o app não estiver rodando, chame antes a skill de startup ou o summoner.
- **O inquisitor tem escopo.** Mudanças não commitadas, um branch ou pastas. Achados Críticos e Altos chegam a você antes de seguir.

## Estrutura do repositório

```
.claude-plugin/
  plugin.json        # manifesto do plugin
  marketplace.json   # permite instalar este repo como marketplace
agents/              # agentes (um .md por agente)
skills/              # skills (uma pasta por skill, com SKILL.md)
index.html           # versão visual deste README
README.md            # versão em inglês
README.pt-BR.md      # esta versão, em português
```

## Princípios

- **Skill** = conhecimento e procedimento reutilizável (roda no contexto de quem a carrega).
- **Agente** = trabalho pesado, barulhento ou paralelizável, com contexto isolado.
- **Chat principal** = decisões com o usuário e trabalho muito acoplado.
- As passagens entre fases acontecem por **arquivos** dentro do projeto.
- Nenhuma classe traz contexto de projeto embutido: todas leem o projeto atual e detectam a stack na hora.
- Toda classe nova entra no Guildmaster, nos dois READMEs (inglês e português), no `index.html` e no "About" do repositório.

---

Guild Hall · github.com/AlissonMM/guild-hall
