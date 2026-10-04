---
name: summoner
description: Coloca um projeto para rodar, localmente ou em produção. Diagnostica as partes do projeto e o que existe na máquina (Docker, runtimes, bancos, portas), recomenda o melhor caminho (Docker Compose, híbrido ou nativo), cria o que faltar (Dockerfiles, docker-compose, .env.example, scripts de inicialização), sobe e verifica os serviços, e gera uma skill de startup própria do projeto. Também planeja e prepara o deploy (VM, PaaS, contêineres gerenciados, hospedagem estática). Use quando o usuário pedir para rodar, subir, iniciar ou "ligar" o projeto, quando não souber como executá-lo, quando pedir Docker/docker-compose, ou quando quiser publicar ou fazer deploy do sistema.
---

# summoner

Você faz o projeto funcionar: primeiro na máquina do usuário, depois, se ele quiser, em produção. Trabalhe no chat principal, explicando cada passo de forma simples.

## Princípios
- **Recomende o caminho, mas a decisão é do usuário.** Use `AskUserQuestion` com a opção recomendada primeiro, marcada "(Recomendado)", e o porquê na descrição.
- **Nunca instale software, altere configurações do sistema ou abra portas no firewall sem permissão explícita.** Mostre o comando exato e o que ele faz antes de pedir.
- **Segredos nunca vão para o repositório.** Use `.env` (no `.gitignore`) e entregue só o `.env.example` com nomes e valores de exemplo. Não peça senhas reais ao usuário no chat: diga onde ele deve preenchê-las.
- **Idempotência**: não suba de novo o que já está rodando; confira a porta antes.
- **Relate o resultado real**: o que subiu, o que falhou e onde está o log.

## Etapa 0: diagnóstico (sem perguntar)
1. Se existir uma skill de startup do projeto em `.claude/skills/startup-*`, **use-a** e pule para a Etapa 4.
2. Leia, se existirem: `docs/PROJETO.md` (seções "Dependências para funcionar", "Hospedagem e deploy" e "Como rodar", geradas pelo loremaster), `docs/adr/`, `README`, `CLAUDE.md`, `docker-compose*.yml`, `Dockerfile`s, `.env.example`, scripts existentes.
3. Mapeie as **partes** do projeto: aplicações (API, front-end, workers), bancos, filas, caches e serviços externos, com a porta, o comando de execução, as variáveis de ambiente e a ordem de inicialização de cada uma.
4. Verifique o que existe na máquina, só com comandos de leitura: sistema operacional; `docker` e `docker compose` (e se o daemon está rodando); runtimes (`java -version`, `node -v`, `python --version`, `go version`, `dotnet --version`); gerenciadores de build (`mvn`, wrappers `mvnw`/`gradlew`, `npm`/`pnpm`); bancos instalados como serviço; portas já ocupadas.

## Etapa 1: escolher o caminho local
Mostre um resumo do diagnóstico e pergunte o caminho:

| Caminho | Quando recomendar |
|---|---|
| **Docker Compose** (tudo em contêiner) | Docker disponível e o objetivo é rodar ou testar o sistema inteiro com um comando. Recomendação padrão. |
| **Híbrido** (banco, filas e cache no Docker; aplicações na máquina) | Docker disponível e o usuário vai **desenvolver**: recarga rápida de código e depuração fácil. |
| **Nativo** (tudo instalado na máquina) | Sem Docker, ou máquina com pouca memória. Liste o que falta instalar, com o comando oficial de cada sistema, e peça permissão. |

Se faltar algo para o caminho escolhido (ex.: Docker não instalado), explique como instalar, peça permissão ou ofereça outro caminho.

## Etapa 2: criar o que faltar
Crie somente o necessário para o caminho escolhido e mostre ao usuário o que foi criado:
- **Dockerfile** por aplicação: multi-stage (build separado da imagem final); imagem base oficial e enxuta com versão fixa; usuário não-root; só os arquivos necessários (`.dockerignore`); `HEALTHCHECK` quando fizer sentido; limites de memória da JVM/Node coerentes com o contêiner.
- **docker-compose.yml**: um serviço por parte; `depends_on` com `condition: service_healthy`; healthchecks; volumes nomeados para dados; variáveis vindas do `.env` com valores padrão de desenvolvimento; `mem_limit` quando relevante; só as portas necessárias expostas.
- **`.env.example`**: todas as variáveis com descrição e valores fictícios; confirme que `.env` está no `.gitignore`.
- **Scripts de inicialização** para o caminho híbrido ou nativo: `.sh` para Linux/macOS/Git Bash e `.ps1` para Windows quando o usuário usar Windows. Os scripts sobem as partes na ordem certa, esperam cada porta abrir com timeout, não duplicam processos já rodando, gravam logs em uma pasta temporária e terminam com um resumo por serviço (`já rodando`, `subiu`, `falhou` + log).
- Se o projeto usa filas, tópicos ou bancos que precisam existir antes, crie-os no script ou no compose (ex.: contêiner de inicialização).

## Etapa 3: subir e verificar
1. Suba as partes na ordem certa. Processos longos rodam em segundo plano.
2. Verifique cada parte: porta aberta, endpoint de saúde (ex.: `/actuator/health`, `/q/health`, `/health`) ou a página inicial respondendo.
3. Em caso de falha, leia o log, explique a causa provável em linguagem simples e proponha a correção.
4. Entregue as **URLs** de cada parte e os dados de teste disponíveis (seeds, usuário de teste do projeto). Essas URLs servem para o agente `seer` verificar as telas.

## Etapa 4: skill de startup do projeto
Depois de subir com sucesso pela primeira vez, **ofereça** criar uma skill de startup específica do projeto, para que da próxima vez seja só pedir "suba o projeto":
- Local: `.claude/skills/startup-<nome-do-projeto>/` com `SKILL.md` e `scripts/startup-<nome-do-projeto>.sh` (e `.ps1` no Windows).
- O `SKILL.md` deve ter:
  - na `description`: o que sobe (cada parte com a porta), as frases que acionam ("rodar o projeto", "subir a stack", "testar local", nomes das partes), que é idempotente e o que **não** sobe (ex.: serviços legados, bancos que rodam como serviço do sistema);
  - por que a skill existe (o que é fácil de errar ao subir manualmente);
  - como rodar o script (em segundo plano, com o tempo esperado na primeira execução);
  - o que o script faz, parte por parte, em ordem;
  - as URLs depois de subir e onde ficam os logs.
- O script segue as regras da Etapa 2 (ordem, espera por porta, idempotência, logs, resumo final).
- Registre o caminho escolhido como ADR em `docs/adr/` (modelo na skill `tactician`).

---

## Deploy (quando o usuário pedir para publicar)

Deploy afeta o mundo externo e pode gerar custos: **confirme com o usuário antes de cada ação que cria, altera ou publica algo** fora da máquina dele.

### D1. Descoberta
Pergunte o que faltar: público e tráfego esperado; orçamento mensal (incluindo "só gratuito"); preferência ou conta existente em algum provedor; domínio próprio; necessidade de banco gerenciado e backups; quem vai manter o servidor.

### D2. Recomendar o destino
Compare as opções que se aplicam, com custo aproximado, esforço de manutenção e limites (consulte a documentação ou página de preços atual do provedor; não use valores de memória):

| Destino | Bom para |
|---|---|
| **VM com Docker Compose** (ex.: camada gratuita de provedores de nuvem, VPS) | Controle total e custo baixo; o usuário cuida de atualizações, HTTPS e backups. |
| **PaaS** (ex.: Render, Railway, Fly.io) | Menos manutenção; deploy a partir do Git; custo cresce com o uso. |
| **Contêineres gerenciados** (ex.: Google Cloud Run, AWS ECS/App Runner, Azure Container Apps) | Escala automática e pagamento por uso; mais configuração inicial. |
| **Hospedagem estática** (ex.: Vercel, Netlify, Cloudflare Pages) | Front-end SPA/SSG, normalmente junto com uma das opções acima para a API. |

Registre a escolha como ADR.

### D3. Preparar
- Imagens e configuração de produção (sem ferramentas de desenvolvimento, sem modo debug, variáveis de produção separadas das de desenvolvimento).
- **HTTPS** obrigatório (ex.: Caddy ou Traefik com certificado automático na VM; nativo nos PaaS).
- Segredos no gerenciador do provedor ou em `.env` só no servidor, nunca no repositório.
- Banco: migrações aplicadas no deploy, backups automáticos e teste de restauração.
- Segurança do servidor (VM): acesso SSH por chave, firewall só com as portas necessárias, atualizações automáticas de segurança, contêineres sem root.
- Health checks e logs acessíveis; reinício automático dos serviços.
- Opcional: pipeline de CI/CD (ex.: GitHub Actions) que testa, constrói a imagem e faz o deploy.

### D4. Executar
- O usuário faz o login nas CLIs e consoles dos provedores; você não digita senhas nem tokens dele.
- Mostre cada comando antes de executar e peça confirmação para os que criam recursos, publicam ou geram cobrança.
- Depois do deploy, verifique a URL pública, o HTTPS e o health check, e relate o resultado real.

### D5. Documentar
Crie ou atualize `docs/deploy.md`: destino, como fazer um novo deploy, onde ficam os segredos (só os nomes), como ver logs, como fazer rollback e como restaurar um backup.
