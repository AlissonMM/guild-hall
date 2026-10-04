---
name: blacksmith
description: Regras para escrever código de back-end com segurança, legibilidade, bom desempenho e testes, seguindo as decisões registradas em docs/adr/. Use sempre que for criar ou alterar código de back-end (endpoints, serviços, entidades, consultas, autenticação, migrações, configuração de servidor), em qualquer linguagem ou framework.
---

# blacksmith

Siga estas regras sempre que escrever ou alterar código de back-end.

## 1. Antes de escrever código
1. Leia `docs/adr/` (decisões de stack, arquitetura, banco, autenticação, API). Elas **mandam** sobre qualquer preferência sua.
2. Se existir, leia `docs/PROJETO.md` (regras de negócio e funcionamento do sistema).
3. Leia o código vizinho e **siga o estilo e os padrões que já existem** no projeto: nomes, estrutura de pastas, tratamento de erros, formato de DTOs.
4. Antes de usar uma API de framework ou biblioteca de que você não tem certeza da versão atual, consulte a documentação (Context7, se disponível).

## 2. Quando perguntar ao usuário
- **Pergunte** (com `AskUserQuestion`, opção recomendada primeiro) sobre decisões **caras de desfazer** que ainda não estão nos ADRs: novo banco ou serviço externo, mudança no modelo de autenticação, mudança de contrato da API usada por outros sistemas, nova dependência relevante, mudança na estrutura de módulos, regra de negócio ambígua.
- **Decida sozinho** os detalhes pequenos (nomes internos, organização de um método, mensagens de log), seguindo os ADRs e o padrão do projeto.
- Quando uma decisão nova for tomada com o usuário, registre um novo ADR em `docs/adr/` (modelo na skill `tactician`).

## 3. Segurança (obrigatório)
- **Valide toda entrada** na fronteira (tamanho, formato, faixa, campos obrigatórios). Rejeite o que não for esperado.
- **Autorização em todo endpoint**, não só autenticação: o usuário pode fazer esta ação **neste recurso**? Cuidado com acesso a dados de outro usuário trocando o ID (IDOR).
- **Consultas parametrizadas** ou ORM. Nunca concatene entrada do usuário em SQL, comandos de shell ou caminhos de arquivo.
- **Senhas** com hash forte e lento (bcrypt, scrypt ou Argon2). Nunca armazene nem registre senhas em texto.
- **Segredos** (chaves, senhas de banco, segredo JWT) apenas em variáveis de ambiente ou cofre. Nunca no código nem no repositório.
- **Não exponha detalhes internos** em erros (stack trace, SQL, caminhos). Use o formato de erro padrão do projeto.
- **Logs sem dados sensíveis** (senhas, tokens, documentos, cartões).
- **CORS** restrito às origens necessárias; **rate limiting** em login, cadastro e recuperação de senha.
- **Dependências**: prefira bibliotecas mantidas e conhecidas; não adicione dependência sem necessidade.
- **Uploads**: valide tipo e tamanho; nunca use o nome de arquivo enviado como caminho.

## 4. Legibilidade
- Nomes que explicam a intenção; funções pequenas, com uma responsabilidade.
- Regras de negócio na camada de domínio/serviço, não espalhadas em controllers ou consultas.
- Sem código morto, sem duplicação desnecessária, sem abstrações "para o futuro".
- Comentários só para o "porquê" que o código não deixa claro.

## 5. Desempenho
- Evite **consultas N+1** (use join/fetch ou consultas em lote).
- **Pagine** toda listagem que possa crescer.
- Crie **índices** para as colunas usadas em filtros, joins e ordenação frequentes.
- Busque só os dados necessários (projeções/DTOs em vez de entidades inteiras quando fizer diferença).
- Transações curtas; nada de chamadas externas lentas dentro de uma transação de banco.
- Use cache apenas onde houver ganho claro e uma estratégia de invalidação.
- **Sem otimização prematura**: otimize o que for medido ou claramente custoso.

## 6. Banco de dados
- Toda mudança de esquema por **migração** versionada (a ferramenta definida no ADR). Nunca deixe o framework alterar o esquema de produção automaticamente.
- Migrações já aplicadas não são editadas: crie uma nova.

## 7. API
- Siga o padrão definido no ADR (rotas, verbos HTTP, códigos de status, formato de erro, paginação, versionamento).
- Mantenha a documentação OpenAPI atualizada quando o projeto usar.
- Mudanças que quebram o contrato precisam de aprovação do usuário (seção 2).

## 8. Observabilidade
- Logs estruturados nos pontos importantes (início/fim de operações de negócio, erros), com identificadores úteis e sem dados sensíveis.
- Health check disponível quando o projeto rodar como serviço.

## 9. Definição de pronto
Uma funcionalidade só está pronta quando:
1. o código **compila**;
2. tem **testes** cobrindo o caminho principal e os principais erros (unitários e, quando tocar banco ou integrações, de integração), e **os testes passam**;
3. as migrações necessárias foram criadas;
4. você **reportou o resultado real** do build e dos testes (sem dizer que passou se não rodou).

Ao terminar uma funcionalidade, recomende ao usuário rodar o agente **`inquisitor`** para uma revisão de segurança e desempenho.
