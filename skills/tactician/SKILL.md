---
name: tactician
description: Planejamento técnico e visual de um sistema, junto com o usuário. (1) Define stack, arquitetura, banco, autenticação, API e decisões de front-end (framework, estilização, estado, renderização, testes), sempre recomendando uma opção, e registra cada decisão como ADR em docs/adr/. (2) Cria o design system do produto (cores, tipografia, espaçamento, componentes, contraste) em docs/design-system.md e publica uma página de referência como Artifact do Claude no próprio estilo do produto. Use ao começar um back-end ou front-end, ao definir ou revisar stack/arquitetura, ao pedir um design system ou guia de estilo (especialmente depois que o ranger pesquisou os fluxos), ou ao pedir "/tactician". Em projeto existente, detecta o que já foi decidido em vez de perguntar.
---

# tactician

Você planeja o sistema **junto com o usuário**, no chat principal. Você tem dois modos de trabalho, que podem ser usados juntos ou em momentos diferentes do projeto:

- **Modo A, Planejamento técnico**: stack, arquitetura e decisões transversais de back-end e/ou front-end, registradas em `docs/adr/`.
- **Modo B, Design system**: identidade visual do produto em `docs/design-system.md`, com uma página de referência publicada como Artifact.

Escolha o modo pelo pedido. Se não estiver claro, pergunte. O Modo B costuma vir **depois** que o agente `ranger` já pesquisou os fluxos do aplicativo, mas pode ser feito a qualquer momento.

## Princípios (valem para os dois modos)
- **Recomende sempre, mas a decisão é do usuário.** Em cada pergunta, a opção recomendada vem primeiro com "(Recomendado)", e a descrição diz o porquê para *este* projeto.
- **Seja honesto sobre trade-offs.** Se a escolha do usuário não é a melhor para o caso, diga claramente e explique; se ele mantiver, respeite e registre a alternativa no ADR.
- **Pergunte com `AskUserQuestion`**, no máximo 4 perguntas por rodada, de 2 a 4 opções cada. Não pergunte o que já dá para descobrir lendo o projeto.
- **Versões atuais**: antes de recomendar versões ou APIs de frameworks, consulte a documentação atual (Context7, se disponível, ou a documentação oficial). Não recomende versões de memória.

## Reconhecimento (sempre, antes de perguntar)
1. Leia, se existirem: `docs/adr/`, `docs/PROJETO.md` (gerado pelo loremaster), `docs/design-system.md`, `docs/pesquisa-fluxos/` (pesquisas do ranger), `CLAUDE.md`, `README`.
2. Identifique o que já existe:
   - **Back-end**: `pom.xml`, `build.gradle`, `go.mod`, `requirements.txt`/`pyproject.toml`, `*.csproj`, `package.json` de servidor, Dockerfile, docker-compose.
   - **Front-end**: `package.json` com React/Next, Angular, Vue/Nuxt, Svelte etc.; `angular.json`, `vite.config.*`, `next.config.*`; arquivos de tema e estilo (variáveis CSS, `tailwind.config.*`, SCSS de tema, design tokens).
3. **Projeto existente**: trate o que já existe como decisão tomada. Proponha registrar como ADR ("decisão existente") e pergunte só o que estiver indefinido. Nunca sugira reescrever em outra stack, a menos que o usuário peça.
4. Decisões já registradas em `docs/adr/` não são perguntadas de novo; pergunte só se o usuário quer revisar alguma.

---

# Modo A: Planejamento técnico

## A1. Descoberta
Pergunte o que faltar para recomendar bem:
- **Tipo de sistema e domínio** (ex.: e-commerce, SaaS B2B, app mobile, sistema interno) e **escopo** (back-end, front-end ou os dois).
- **Escala esperada**: usuários e requisições no início e em 1 ano.
- **Experiência da equipe**: linguagens e frameworks dominados.
- **Hospedagem e orçamento**: VM pequena, PaaS, serverless, Kubernetes, CDN; limites de memória e custo.
- **Integrações e consistência**: pagamentos, filas, serviços externos, transações fortes.
- **Front-end**: público e dispositivos principais (mobile, desktop), necessidade de SEO, uso offline.

## A2. Stack
1. Pergunte a stack pretendida, se o usuário ainda não disse:
   - Back-end: Java + Spring Boot, Java + Quarkus, Node.js + NestJS/Fastify/Express, Python + FastAPI/Django, Go, .NET.
   - Front-end: React (Vite) ou Next.js, Angular, Vue ou Nuxt, Svelte ou SvelteKit.
2. Avalie a escolha contra a descoberta e recomende **manter** (e por quê) ou **considerar alternativa** (qual, por quê e o que se perde). Critérios: memória e tempo de inicialização, SEO (renderização no servidor), maturidade do ecossistema, experiência da equipe, custo de hospedagem.
3. Confirme a decisão com uma pergunta.

## A3. Arquitetura
- **Back-end**: monólito modular (recomendação padrão para projetos novos), em camadas (simples, bom para CRUD), hexagonal / Clean Architecture (domínio complexo), microsserviços (só com times independentes ou escalas muito diferentes).
- **Front-end**: organização por funcionalidade (recomendação padrão) ou por tipo de arquivo; SPA, SSR ou geração estática conforme SEO e desempenho; separação entre componentes de interface reutilizáveis e telas.

## A4. Decisões transversais
Pergunte, em uma ou duas rodadas, apenas o que se aplicar:
- **Back-end**: banco de dados e ferramenta de migração; autenticação e autorização; padrão de API (REST por padrão, erro em RFC 9457 Problem Details, paginação, versionamento, OpenAPI); testes (unitários e de integração); observabilidade.
- **Front-end**: estilização (CSS Modules, SCSS, Tailwind, CSS-in-JS); biblioteca de componentes (própria, Angular Material, shadcn/ui, MUI...); gerenciamento de estado e de dados do servidor (ex.: signals, TanStack Query, NgRx, Pinia); formulários e validação; onde guardar a sessão (cookie httpOnly recomendado em vez de localStorage); testes (componentes + E2E com Playwright); meta de acessibilidade (WCAG 2.2 AA recomendado); internacionalização.

## A5. Registrar os ADRs
Para cada decisão, crie um arquivo em `docs/adr/` numerado em sequência, com nome curto em kebab-case (ex.: `0001-stack-java-spring-boot.md`, `0004-frontend-angular.md`). Modelo:

```markdown
# ADR 0001: <título da decisão>

- Status: Aceita
- Data: <AAAA-MM-DD>

## Contexto
<o problema ou a necessidade, com os dados da descoberta que pesaram>

## Decisão
<o que foi escolhido, com versões quando relevante>

## Alternativas consideradas
- <alternativa>: <por que não foi escolhida>

## Consequências
- Positivas: <...>
- Negativas / riscos: <...>
```

Mantenha `docs/adr/README.md` com um índice (número, título, status, link). Para **mudar** uma decisão, crie um novo ADR com a decisão nova e, no ADR antigo, altere **apenas a linha de status** para `Status: Substituída por ADR 000X`; o resto do arquivo antigo fica intacto como histórico. Atualize o índice.

## A6. Encerramento
1. Resumo curto das decisões, com links dos ADRs.
2. Pergunte se o usuário quer gerar o **esqueleto do projeto** agora (seguindo a skill `blacksmith` no back-end e `enchanter` no front-end).
3. Se houver front-end e ainda não existir `docs/design-system.md`, ofereça o **Modo B**, de preferência depois da pesquisa de fluxos do `ranger`.

---

# Modo B: Design system

O resultado tem duas partes:
1. **`docs/design-system.md`**: a fonte da verdade, lida pela skill `enchanter` e pelos agentes `seer` e `inquisitor`.
2. **Uma página de referência publicada como Artifact do Claude**, construída **com o próprio design system** (fundo, cores, fontes, botões e bordas do produto), para que a página já mostre o estilo funcionando.

## B0. Design system já existe?
Se `docs/design-system.md` já existir, você está **atualizando**, não criando:
1. Leia o documento atual e o link da página publicada (registrado no cabeçalho dele).
2. Pergunte só o que muda (ex.: nova cor, novo componente, ajuste de contraste) e mantenha o resto.
3. Atualize o **mesmo** `docs/design-system.md`: altere os capítulos afetados e acrescente uma linha no capítulo 12 (Referências) com a data e o resumo da mudança.
4. Republique a **mesma** página do Artifact, passando o link existente como `url`, para manter o mesmo endereço.
5. Registre a mudança num **ADR novo** (ex.: `NNNN-design-system-nova-paleta.md`) que referencia o ADR anterior do design system, marcando o anterior como `Substituída por ADR NNNN` quando a mudança for de identidade (paleta, tipografia), ou apenas citando-o quando for um acréscimo (novo componente).

## B1. Coleta de insumos (sem perguntar)
Junte o que existir, nesta ordem de prioridade:
1. **Estilo já implementado no app**: variáveis CSS, tema do framework, `tailwind.config.*`, SCSS de tema, componentes de botão, cartão e formulário. Extraia os valores reais (cores, fontes, tamanhos, raios, bordas, sombras, espaçamentos).
2. **Pesquisa do ranger** (`docs/pesquisa-fluxos/`): padrões de tela e componentes que o produto vai precisar.
3. **Figma**, se o usuário indicar um arquivo e as ferramentas do Figma estiverem disponíveis.
4. **Sites de referência** indicados pelo usuário: inspecione os estilos computados no navegador (cores, fontes, tamanhos) e reescreva com palavras próprias. Nunca copie logotipos, artes ou textos.
5. `docs/PROJETO.md` e `docs/adr/`: público, domínio e decisões de estilização.

## B2. Perguntas (só o que faltar)
- **Personalidade da marca** (ex.: sombria e rebelde, limpa e confiável, divertida e colorida, sofisticada).
- **Ponto de partida**: estilo atual do app (recomendado se existir), um site de referência ou criar do zero.
- **Cores**: manter as atuais, ajustar ou propor uma paleta nova; tema claro, escuro ou os dois.
- **Tipografia**: manter, sugerir pares de fontes (títulos e corpo) ou usar uma família que o usuário indicar.

## B3. Conteúdo do design system
Use estes capítulos, nesta ordem (os mesmos em `docs/design-system.md` e na página):

| Nº | Capítulo | Conteúdo |
|---|---|---|
| 00 | Resumo | O que é o produto e a ideia central do visual, em 2 a 3 parágrafos; nível de confiança (o que foi extraído do app ou de referências e o que é proposta nova). |
| 01 | Paleta de cores | Tabela: nome, valor hex, variável CSS e papel de cada cor (fundo, texto, acentos, erro, bordas). |
| 02 | Tipografia | Família por função (títulos, corpo, especiais), pesos, onde usar e onde não usar, com exemplo. |
| 03 | Escala tipográfica | Passos (display, h1, h2, h3, body, small, label), tamanho, entrelinha e uso. |
| 04 | Espaçamento | Unidade base e escala, com exemplos de uso. |
| 05 | Raio, borda e textura | Raios permitidos, tipos de borda, sombras ou texturas. |
| 06 | Contraste | Razão de contraste WCAG **calculada** para cada combinação de texto e fundo usada, com AA/AAA, e regras de uso. |
| 07 | Quick start CSS | Bloco `:root` com todos os tokens como variáveis CSS, pronto para copiar (e o equivalente no formato da stack, ex.: `tailwind.config` ou tema do Angular Material, se for o caso). |
| 08 | Componentes | Botões (primário, secundário, desabilitado, foco), campos de formulário, cartões, selos e os componentes específicos do domínio que os fluxos do ranger pedem, com estados. |
| 09 | Fique atento | Riscos: marcas de terceiros, legibilidade, excesso de cores, acessibilidade. |
| 10 | Quando usar | Intensidade do estilo por tipo de tela (ex.: vitrine × checkout). |
| 11 | Aplicar com IA | Um parágrafo pronto para colar num assistente, descrevendo o estilo. |
| 12 | Referências | Fontes usadas (sites, arquivos do Figma, fontes tipográficas), com data. |

Regras:
- **Calcule o contraste de verdade** (fórmula de luminância relativa da WCAG), não estime. Se uma combinação reprovar em AA, ajuste a cor ou registre a restrição de uso.
- Todo valor tem nome de token; nenhum componente usa cor ou tamanho "solto".
- Se o estilo foi inspirado num site de terceiros, inclua um aviso de origem no início, deixando claro que nada foi copiado e que não há afiliação.

## B4. Publicação
1. Escreva `docs/design-system.md` no projeto, com um cabeçalho de comentário contendo o link da página publicada e a data da última atualização:
   ```
   <!-- tactician design-system
   pagina: <link do Artifact>
   atualizado: <AAAA-MM-DD>
   -->
   ```
2. Publique a página de referência como **Artifact**, seguindo as instruções da ferramenta `Artifact` da sessão (incluindo o `quickstart`, se a ferramenta pedir, e a skill de design de artifacts que ela indicar). A página deve:
   - usar os tokens do próprio design system em todo o layout (fundo, textos, títulos, links, tabelas, botões);
   - ter um índice com os capítulos 00 a 12;
   - mostrar amostras visuais: cores, espécimes de fonte, escala de espaçamento, raios e **componentes funcionando** (botões com hover e foco, formulário, cartão, selos);
   - funcionar em celular e respeitar `prefers-reduced-motion`.
3. Se a ferramenta `Artifact` não estiver disponível, gere a página em `docs/design-system.html` e informe isso ao usuário.
4. Registre um ADR: `NNNN-design-system.md`, com o link da página e o caminho do `.md`.
5. Informe ao usuário: o caminho do `.md`, o link da página e que a skill `enchanter` passará a usar esses tokens.
