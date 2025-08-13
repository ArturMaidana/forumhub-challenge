# FórumHub - API REST

## Descrição

FórumHub é uma API REST desenvolvida em Java com Spring Boot para gerenciar um fórum de discussões. A API permite aos usuários criar, listar, atualizar e excluir tópicos, implementando um sistema completo de CRUD com autenticação JWT.

## Tecnologias Utilizadas

- **Java 17**
- **Spring Boot 3.2.0**
- **Spring Security**
- **Spring Data JPA**
- **H2 Database** (banco em memória)
- **JWT (JSON Web Token)** para autenticação
- **Maven** para gerenciamento de dependências

## Funcionalidades

### Autenticação
- Login com JWT
- Proteção de rotas com Spring Security
- Tokens com expiração de 2 horas

### Gestão de Tópicos
- **CREATE**: Criar novos tópicos
- **READ**: Listar todos os tópicos (com paginação)
- **READ**: Visualizar detalhes de um tópico específico
- **UPDATE**: Atualizar tópicos existentes
- **DELETE**: Excluir tópicos

### Validações
- Prevenção de tópicos duplicados
- Validação de campos obrigatórios
- Tratamento global de exceções

## Estrutura do Projeto

```
src/
├── main/
│   ├── java/com/alura/forumhub/
│   │   ├── controller/          # Controllers REST
│   │   ├── domain/              # Entidades e DTOs
│   │   │   ├── topico/
│   │   │   └── usuario/
│   │   ├── infra/               # Configurações de infraestrutura
│   │   │   ├── security/        # Configurações de segurança
│   │   │   └── exception/       # Tratamento de exceções
│   │   └── repository/          # Repositórios JPA
│   └── resources/
│       ├── application.properties
│       └── data.sql             # Dados iniciais
└── test/                        # Testes unitários
```

## Configuração e Execução

### Pré-requisitos
- Java 17 ou superior
- Maven 3.6 ou superior

### Executando a aplicação

1. Clone o repositório
2. Navegue até o diretório do projeto
3. Execute o comando:
```bash
mvn spring-boot:run
```

A aplicação estará disponível em `http://localhost:8080`

### Console H2
O console do banco H2 está disponível em: `http://localhost:8080/h2-console`
- **URL**: `jdbc:h2:mem:testdb`
- **Usuário**: `sa`
- **Senha**: `password`

## Endpoints da API

### Autenticação

#### POST /login
Realiza login e retorna token JWT

**Request Body:**
```json
{
  "login": "admin",
  "senha": "123456"
}
```

**Response:**
```json
{
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
}
```

### Tópicos

#### POST /topicos
Cria um novo tópico

**Headers:**
```
Authorization: Bearer {token}
```

**Request Body:**
```json
{
  "titulo": "Dúvida sobre Spring Boot",
  "mensagem": "Como configurar o Spring Security?",
  "curso": "Spring Boot"
}
```

**Response:** `201 Created`
```json
{
  "id": 1,
  "titulo": "Dúvida sobre Spring Boot",
  "mensagem": "Como configurar o Spring Security?",
  "dataCriacao": "2024-01-15T10:30:00",
  "status": "NAO_RESPONDIDO",
  "autor": "João Silva",
  "curso": "Spring Boot"
}
```

#### GET /topicos
Lista todos os tópicos com paginação

**Headers:**
```
Authorization: Bearer {token}
```

**Query Parameters:**
- `page`: número da página (padrão: 0)
- `size`: tamanho da página (padrão: 10)
- `sort`: campo para ordenação (padrão: dataCriacao)

**Response:** `200 OK`
```json
{
  "content": [
    {
      "id": 1,
      "titulo": "Dúvida sobre Spring Boot",
      "mensagem": "Como configurar o Spring Security?",
      "dataCriacao": "2024-01-15T10:30:00",
      "status": "NAO_RESPONDIDO",
      "autor": "João Silva",
      "curso": "Spring Boot"
    }
  ],
  "pageable": {
    "pageNumber": 0,
    "pageSize": 10
  },
  "totalElements": 1
}
```

#### GET /topicos/{id}
Retorna detalhes de um tópico específico

**Headers:**
```
Authorization: Bearer {token}
```

**Response:** `200 OK`
```json
{
  "id": 1,
  "titulo": "Dúvida sobre Spring Boot",
  "mensagem": "Como configurar o Spring Security?",
  "dataCriacao": "2024-01-15T10:30:00",
  "status": "NAO_RESPONDIDO",
  "autor": "João Silva",
  "curso": "Spring Boot"
}
```

#### PUT /topicos/{id}
Atualiza um tópico existente

**Headers:**
```
Authorization: Bearer {token}
```

**Request Body:**
```json
{
  "titulo": "Dúvida sobre Spring Security",
  "mensagem": "Como configurar autenticação JWT?"
}
```

**Response:** `200 OK`
```json
{
  "id": 1,
  "titulo": "Dúvida sobre Spring Security",
  "mensagem": "Como configurar autenticação JWT?",
  "dataCriacao": "2024-01-15T10:30:00",
  "status": "NAO_RESPONDIDO",
  "autor": "João Silva",
  "curso": "Spring Boot"
}
```

#### DELETE /topicos/{id}
Exclui um tópico

**Headers:**
```
Authorization: Bearer {token}
```

**Response:** `204 No Content`

## Status dos Tópicos

- `NAO_RESPONDIDO`: Tópico criado mas ainda sem respostas
- `NAO_SOLUCIONADO`: Tópico com respostas mas não solucionado
- `SOLUCIONADO`: Tópico com solução encontrada
- `FECHADO`: Tópico fechado para novas respostas

## Usuários de Teste

A aplicação vem com usuários pré-cadastrados para teste:

| Login | Senha | Nome |
|-------|-------|------|
| admin | 123456 | Administrador |
| usuario1 | 123456 | João Silva |
| usuario2 | 123456 | Maria Santos |

## Validações e Regras de Negócio

1. **Tópicos duplicados**: Não é permitido criar tópicos com mesmo título e mensagem
2. **Campos obrigatórios**: Título, mensagem e curso são obrigatórios
3. **Autenticação**: Todos os endpoints (exceto login) requerem token JWT válido
4. **Paginação**: Lista de tópicos retorna no máximo 10 itens por página

## Tratamento de Erros

A API retorna códigos HTTP apropriados e mensagens de erro estruturadas:

- `400 Bad Request`: Dados inválidos ou tópico duplicado
- `401 Unauthorized`: Token inválido ou ausente
- `404 Not Found`: Recurso não encontrado
- `500 Internal Server Error`: Erro interno do servidor

## Testes

Execute os testes com:
```bash
mvn test
```

## Autor

Desenvolvido como parte do Challenge Back End da Alura.

## Licença

Este projeto é licenciado sob a MIT License.

