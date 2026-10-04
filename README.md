# Guild Hall

Plugin do Claude Code com agentes e skills **universais**, independentes de projeto, organizados como classes de RPG.

Visão geral visual: abra [`docs/index.html`](docs/index.html) no navegador (ou a página publicada no Claude: https://claude.ai/artifact/KUe63e4HmUWxtKqdUs5bix).

## Estrutura

```
.claude-plugin/
  plugin.json        # manifesto do plugin
  marketplace.json   # permite instalar este repo como marketplace
agents/              # subagentes (um .md por agente)
skills/              # skills (uma pasta por skill, com SKILL.md)
docs/index.html      # página com todas as classes
```

## Agentes

| Agente | O que faz |
|---|---|
| `loremaster` | Lê um projeto (somente leitura) e gera `docs/PROJETO.md`: guia rápido, regras de negócio com evidência, stack, arquitetura, deploy e problemas conhecidos. |
| `ranger` | Pesquisa referências de fluxos e telas (Mobbin, Lazyweb, Refero ou web), salva em `docs/pesquisa-fluxos/` e opcionalmente no Figma. Faz perguntas pausando e sendo retomado pelo agente principal (`STATUS: AGUARDANDO_RESPOSTA` → `SendMessage` com as respostas). |
| `seer` | Abre o app no navegador, percorre um fluxo em celular e desktop e compara com o design system e as referências. Não altera código. |
| `inquisitor` | Revisa código pronto de back-end e front-end (segurança, acessibilidade, desempenho, ADRs, design system, testes). Somente leitura. |

## Skills

| Skill | O que faz |
|---|---|
| `guildmaster` | Explica como todas as peças do plugin se relacionam e em que ordem usar. Comece por aqui: `/guildmaster`. |
| `tactician` | Modo A: planejamento técnico de back-end e front-end com ADRs em `docs/adr/`. Modo B: design system em `docs/design-system.md` + página de referência publicada como Artifact no estilo do produto. |
| `blacksmith` | Regras ao programar back-end: segurança, legibilidade, desempenho, testes, seguindo os ADRs. |
| `enchanter` | Regras ao programar front-end: design system, acessibilidade, responsividade, estados de tela, desempenho, segurança, testes. |
| `summoner` | Faz o projeto rodar (Docker Compose, híbrido ou nativo), cria Dockerfiles/compose/scripts, gera a skill de startup do projeto e prepara o deploy. |

## Instalação

Dentro do Claude Code (em qualquer máquina com acesso a este repositório):

```
/plugin marketplace add AlissonMM/guild-hall
/plugin install guild-hall@alissonmm-toolkit
```

## Princípios

- **Skill** = conhecimento e procedimento reutilizável (roda no contexto de quem a carrega).
- **Subagente** = trabalho pesado, barulhento ou paralelizável, com contexto isolado.
- **Chat principal** = decisões com o usuário e trabalho muito acoplado.
- As passagens entre fases acontecem por **arquivos** (ex.: `pesquisa-fluxos.md` → `decisoes-fluxo.md`).
- Agentes não trazem contexto de projeto embutido: leem o `CLAUDE.md` e detectam a stack na hora.
