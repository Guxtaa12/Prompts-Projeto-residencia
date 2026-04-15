# Prompts de Desenvolvimento: Agente Operacional Autônomo para Diretoria

---

## Prompt 1: Infraestrutura Básica, Docker e Estrutura do Backend
**Objetivo:** Configurar o esqueleto do projeto, ambiente de desenvolvimento e banco de dados relacional.

**Texto do Prompt:**
> "Atue como um Engenheiro de Software Sênior. Vamos construir o backend de uma aplicação corporativa chamada 'Agente Operacional Autônomo para Diretoria'. A stack será Node.js + TypeScript. Quero que você crie o setup inicial seguindo Clean Architecture.
> 
> Tarefas:
> 1. Crie o `docker-compose.yml` para subir a aplicação Node e um banco PostgreSQL.
> 2. Defina a estrutura de pastas do projeto (ex: src/domain, src/useCases, src/infra, src/presentation).
> 3. Crie o script de inicialização do servidor Express.
> 4. Configure os middlewares essenciais de segurança e infraestrutura: Helmet, CORS, e Winston para logs estruturados.
> 5. Crie a configuração inicial do TypeORM ou Prisma (sua escolha baseada em melhor tipagem) para conectar ao PostgreSQL.
> 
> Por favor, forneça apenas os arquivos de configuração, o `package.json` com as dependências necessárias e a estrutura de pastas. Não implemente as regras de negócio ainda."

---

## Prompt 2: Banco de Dados Relacional e Autenticação JWT
**Objetivo:** Criar as entidades relacionais e proteger as rotas.

**Texto do Prompt:**
> "Continuando o projeto, agora vamos focar na camada de Autenticação e no Banco Relacional.
> 
> Tarefas:
> 1. Crie os Models/Schemas para as tabelas no banco relacional:
>    - `Usuarios` (id UUID, nome, email, senha_hash, role, created_at).
>    - `Documentos` (id UUID, nome_arquivo, data_upload, status_processamento, texto_extraido).
>    - `Analises` (id UUID, documento_id, json_resposta, prompt_utilizado, modelo_ia, timestamp).
>    - `LogsAuditoria` (id UUID, analise_id, usuario_id, acao_executada, justificativa, timestamp).
> 2. Implemente o caso de uso (Use Case) de Registro de Usuário e Login.
> 3. A senha deve ser criptografada com `bcrypt`.
> 4. O Login deve retornar um token JWT.
> 5. Crie um middleware de autenticação (JWT) para proteger rotas da API e outro middleware para controle de acesso baseado em roles (ex: admin, analista, auditor).
> 6. Trate os erros de autenticação (401) e validação de input usando a biblioteca `Zod` (erro 400).
> 
> Me mostre os schemas do banco, os Use Cases de Auth e o middleware."

---

## Prompt 3: Pipeline de Upload e Processamento de Texto
**Objetivo:** Extrair o texto dos PDFs e armazenar diretamente no banco relacional.

**Texto do Prompt:**
> "Agora vamos implementar a rota de Upload e o processamento do arquivo.
> 
> Tarefas:
> 1. Crie a rota `POST /api/v1/documentos/upload` que receba um arquivo PDF via `multipart/form-data`.
> 2. Implemente o serviço que usa `pdf-parse` (ou similar) para extrair o texto completo do PDF.
> 3. Salve esse texto na coluna `texto_extraido` da tabela `Documentos`.
> 4. Atualize o status do documento para 'PROCESSADO'.
> 5. A rota deve retornar um status `202 Accepted` e o `documento_id`.
> 
> Mostre os controllers, as rotas e os serviços de extração de documento."

---

## Prompt 4: O Agente de IA (Análise de Contexto Longo)
**Objetivo:** O coração da aplicação: enviar o texto extraído para o LLM e gerar a resposta estruturada.

**Texto do Prompt:**
> "Chegou a hora de implementar o core do Agente de IA.
> 
> Tarefas:
> 1. Crie a rota `POST /api/v1/agente/analisar` que recebe um JSON: `{ "documento_id": "uuid", "foco_analise": "financeiro | operacional | juridico" }`.
> 2. Busque o `texto_extraido` do documento no banco de dados.
> 3. Construa o prompt estruturado enviando o texto completo para o LLM (OpenAI, Gemini, etc). O prompt DEVE exigir que a saída seja um JSON estrito e incluir instruções sobre o 'foco da análise'.
> 4. O schema de saída esperado do LLM é: `{ "resumo": "...", "riscos": [], "oportunidades": [], "recomendacoes": [], "impacto_financeiro": {}, "justificativas": [] }`.
> 5. Crie uma validação rígida da resposta da IA. Se o LLM não retornar um JSON válido, acione um fallback (retornar 'Informação não encontrada' ou tentar novamente). Adicione tratamento de erro caso o texto extraído exceda o limite de tokens da API do LLM.
> 6. Salve a resposta gerada na tabela `Analises` no banco relacional."

---

## Prompt 5: Trilha de Auditoria e Rate Limiting
**Objetivo:** Garantir rastreabilidade corporativa e segurança.

**Texto do Prompt:**
> "Para finalizar o backend corporativo, precisamos de auditoria e segurança.
> 
> Tarefas:
> 1. Crie um 'Interceptor' ou middleware que registre na tabela `LogsAuditoria` sempre que uma análise for gerada ou um documento visualizado. Deve salvar quem fez (usuario_id), qual análise (analise_id), a ação e a justificativa.
> 2. Crie a rota `GET /api/v1/auditoria/historico` para buscar os logs, permitindo filtros via Query Params (por usuário, documento ou data).
> 3. Implemente Rate Limiting na API globalmente e nas rotas específicas do LLM (para evitar estourar cota e custos).
> 4. Certifique-se de que todos os erros (500) globais estão sendo capturados e formatados de forma segura (sem vazar stack trace) através de um middleware de Error Handler customizado."

---

## Prompt 6: Setup do Frontend React + Auth UI
**Objetivo:** Iniciar o client-side e plugar a autenticação.

**Texto do Prompt:**
> "Atue como um Desenvolvedor Frontend Sênior. Vamos construir o client-side com React + TypeScript, utilizando Vite, e TailwindCSS (ou Material UI, escolha a que tiver melhor integração rápida).
> 
> Tarefas:
> 1. Crie a estrutura base do projeto React.
> 2. Configure um `AuthProvider` (Context API ou Zustand) para gerenciar o estado global de autenticação (token JWT e dados do usuário). Implemente a lógica de manter a sessão persistente (localStorage/sessionStorage).
> 3. Crie os componentes de roteamento, incluindo `PrivateRoute` para bloquear acesso não autorizado.
> 4. Desenvolva as telas de `Login` e `Registro` (estilização moderna e corporativa), integrando com a API REST construída anteriormente e usando Zod + React Hook Form para validação no client-side."

---

## Prompt 7: Dashboard, Upload e Integração de Análise
**Objetivo:** Interface principal de operação e visualização das análises.

**Texto do Prompt:**
> "Agora vamos construir as telas principais do sistema logado.
> 
> Tarefas:
> 1. Crie um layout de `Dashboard` com Sidebar e Header.
> 2. Desenvolva a tela de `Upload de Documentos`. Ela deve ter drag-and-drop, indicador de loading/progresso e avisar quando o status retornar 202.
> 3. Desenvolva a tela de `Geração de Análise`. O usuário escolhe o documento processado, seleciona o 'foco da análise' e aciona o Agente de IA.
> 4. Desenvolva o componente de `Visualização de Análise` que renderiza o JSON retornado pela IA de forma visual (Cards para resumo, listas para riscos/oportunidades, destaque para impacto financeiro).
> 5. Crie a tela de `Histórico de Auditoria` consumindo a rota de logs em uma tabela com paginação e filtros.
> 6. Garanta que todas as requisições API tratem o status 401 fazendo logout automático do usuário, e lidem de forma elegante com status 500 ou demoras no processamento da IA (esqueletos de loading)."
