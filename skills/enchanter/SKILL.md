---
name: enchanter
description: Regras para construir front-end (telas, componentes, estilos, formulários, integração com a API) com acessibilidade, responsividade, bom desempenho e segurança, seguindo o design system (docs/design-system.md), as decisões em docs/adr/ e as pesquisas de fluxo do ranger. Use sempre que for criar ou alterar código de front-end, em qualquer framework (React, Next.js, Angular, Vue, Svelte etc.).
---

# enchanter

Siga estas regras sempre que escrever ou alterar código de front-end.

## 1. Antes de escrever código
1. Leia `docs/adr/` (framework, estilização, estado, renderização, testes). As decisões registradas **mandam** sobre qualquer preferência sua.
2. Leia `docs/design-system.md` (tokens de cor, tipografia, espaçamento, raios e componentes). Se não existir e a tarefa for visual, avise o usuário e ofereça criar com a skill `tactician` (Modo B) antes de inventar um estilo.
3. Se a tela vier de uma pesquisa do agente `ranger`, leia `docs/pesquisa-fluxos/<tema>.md` e siga as decisões que o usuário tomou sobre ela.
4. Se existir, leia `docs/PROJETO.md` (regras de negócio, rotas da API, permissões).
5. Leia os componentes vizinhos e **siga os padrões que já existem**: estrutura de pastas, nomes, forma de chamar a API, tratamento de erros.
6. Antes de usar uma API de framework de que você não tem certeza da versão atual, consulte a documentação (Context7, se disponível).

## 2. Quando perguntar ao usuário
- **Pergunte** (com `AskUserQuestion`, opção recomendada primeiro) sobre: comportamento de fluxo não definido (o que acontece depois de salvar, para onde redirecionar), textos importantes da interface, novo componente que não existe no design system, nova dependência relevante, mudança de rota pública, campo ou endpoint que a API ainda não tem.
- **Decida sozinho** os detalhes pequenos, seguindo o design system e o padrão do projeto.
- Se precisar de algo que a API não oferece, descreva o contrato esperado (rota, método, campos) em vez de inventar.
- Decisões novas e relevantes viram ADR em `docs/adr/` (modelo na skill `tactician`).

## 3. Design system
- Use **sempre os tokens** (variáveis CSS, tema, classes utilitárias configuradas). Nunca escreva cor, fonte, tamanho ou raio "soltos" no componente.
- Reaproveite os componentes base (botão, campo, cartão) em vez de recriar estilos.
- Respeite as regras de uso do design system (ex.: qual acento usar em cada situação, intensidade por tipo de tela).

## 4. Acessibilidade (meta: WCAG 2.2 AA)
- HTML semântico: `button` para ações, `a` para navegação, títulos em ordem, `main`, `nav`, `header`, listas de verdade.
- Todo campo com `label` associado; mensagens de erro ligadas ao campo (`aria-describedby`) e anunciadas.
- Tudo funciona **só com teclado**, com foco visível e ordem lógica; modais prendem e devolvem o foco.
- Imagens com `alt` significativo (ou vazio, se decorativas); ícones sem texto têm nome acessível.
- Contraste dentro do aprovado no design system; informação nunca transmitida só por cor.
- Respeite `prefers-reduced-motion`.

## 5. Responsividade
- Comece pelo celular (mobile-first) e teste nas larguras de celular (≈375px), tablet (≈768px) e desktop (≈1280px).
- Sem rolagem horizontal da página; alvos de toque com pelo menos 24×24px (recomendado 44×44px).

## 6. Estados de tela
Toda tela ou componente que busca dados tem os estados: **carregando**, **vazio**, **erro** (com ação para tentar de novo) e **sucesso**. Formulários têm validação no cliente **e** tratam os erros que vêm do servidor.

## 7. Desempenho
- Carregue telas sob demanda (lazy loading de rotas) e evite dependências pesadas sem necessidade.
- Imagens com tamanho adequado, formatos modernos, `width`/`height` definidos e carregamento tardio abaixo da dobra.
- Evite renderizações desnecessárias e buscas repetidas (use o cache de dados do servidor definido no ADR).
- Pagine ou virtualize listas longas.
- Fique atento aos Core Web Vitals (LCP, INP, CLS).

## 8. Segurança
- **Nunca** insira HTML vindo do usuário ou da API sem sanitizar (`innerHTML`, `dangerouslySetInnerHTML`, `[innerHTML]`, `v-html`, `bypassSecurityTrust*`).
- **Nenhum segredo no front-end**: tudo que vai para o navegador é público. Chaves privadas ficam no back-end.
- Sessão e tokens conforme o ADR (preferência: cookie `httpOnly`); não guarde dados sensíveis em `localStorage`.
- A validação no cliente é só para a experiência do usuário: a regra de verdade é aplicada no back-end.
- Esconder um botão não é controle de acesso: a permissão é verificada na API.
- Links externos com `rel="noopener noreferrer"`.

## 9. Legibilidade
Componentes pequenos e com uma responsabilidade; lógica de negócio e chamadas à API fora dos componentes de apresentação; nomes claros; sem código morto; comentários só para o "porquê".

## 10. Definição de pronto
Uma tela ou funcionalidade só está pronta quando:
1. o projeto **compila** sem erros novos;
2. há **testes** do comportamento principal (componentes e, para fluxos críticos, E2E) e **eles passam**;
3. a tela foi **vista funcionando** no navegador nas larguras de celular e desktop;
4. você **reportou o resultado real** do build, dos testes e da verificação.

Ao terminar, recomende ao usuário:
- o agente **`seer`**, para a verificação visual e de fluxo no navegador;
- o agente **`inquisitor`**, para a revisão de segurança, acessibilidade e desempenho.
