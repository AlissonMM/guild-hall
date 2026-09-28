# claude-agents-toolkit

Plugin do Claude Code com agentes e skills **universais**, independentes de projeto.

## Estrutura

```
.claude-plugin/
  plugin.json        # manifesto do plugin
  marketplace.json   # permite instalar este repo como marketplace
agents/              # subagentes (um .md por agente)
skills/              # skills (uma pasta por skill, com SKILL.md)
```

## Instalação

Dentro do Claude Code (em qualquer máquina com acesso a este repositório):

```
/plugin marketplace add AlissonMM/claude-agents-toolkit
/plugin install claude-agents-toolkit@alissonmm-toolkit
```

## Princípios

- **Skill** = conhecimento e procedimento reutilizável (roda no contexto de quem a carrega).
- **Subagente** = trabalho pesado, barulhento ou paralelizável, com contexto isolado.
- **Chat principal** = decisões com o usuário e trabalho muito acoplado.
- As passagens entre fases acontecem por **arquivos** (ex.: `pesquisa-fluxos.md` → `decisoes-fluxo.md`).
- Agentes não trazem contexto de projeto embutido: leem o `CLAUDE.md` e detectam a stack na hora.
