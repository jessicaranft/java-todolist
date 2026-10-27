# ✅ Todolist API

API REST de gerenciamento de tarefas (to-do list) desenvolvida em **Java** com **Spring Boot**. Cada usuário se cadastra, se autentica e gerencia apenas as suas próprias tarefas, com validações de datas, título e permissões.

> 🎓 Este projeto foi desenvolvido durante o **curso de Java da [Rocketseat](https://www.rocketseat.com.br/)**.
> Certificado de conclusão: [ver certificado](https://app.rocketseat.com.br/certificates/d486d90a-0c8e-4ff0-aa00-d6dbd87e9356)

---

## 📋 Funcionalidades

- **Cadastro de usuários**, com senha criptografada usando BCrypt e validação de nome de usuário único.
- **Autenticação via Basic Auth**, feita por um filtro (`OncePerRequestFilter`) que protege todas as rotas de `/tasks`.
- **Criação de tarefas** associadas ao usuário autenticado, com as validações:
  - as datas de início e término devem ser posteriores à data atual;
  - a data de início deve ser anterior à data de término;
  - o título deve ter no máximo 50 caracteres.
- **Listagem das tarefas** do usuário autenticado.
- **Atualização parcial de tarefas**: só os campos enviados são alterados, e só o dono da tarefa pode alterá-la.
- **Tratamento global de erros** com `@ControllerAdvice`, que devolve mensagens claras ao cliente.

## 🛠️ Tecnologias utilizadas

| Tecnologia | Uso |
| --- | --- |
| [Java 17](https://openjdk.org/projects/jdk/17/) | Linguagem |
| [Spring Boot 3](https://spring.io/projects/spring-boot) | Framework base da aplicação |
| Spring Web | Criação dos endpoints REST |
| Spring Data JPA | Persistência e repositórios |
| [H2 Database](https://www.h2database.com/) | Banco de dados em memória |
| [Lombok](https://projectlombok.org/) | Geração de getters/setters (`@Data`) |
| [BCrypt (at.favre.lib)](https://github.com/patrickfav/bcrypt) | Hash de senhas |
| Spring Boot DevTools | Reload automático no desenvolvimento |
| [Maven](https://maven.apache.org/) | Gerenciamento de dependências e build |
| [Docker](https://www.docker.com/) | Containerização para deploy |

## 📁 Estrutura do projeto

```
src/main/java/br/com/jessicaranft/todolist
├── TodolistApplication.java        # Classe principal
├── errors/
│   └── ExceptionHandlerController.java  # Tratamento global de exceções
├── filter/
│   └── FilterTaskAuth.java         # Filtro de autenticação (Basic Auth)
├── task/
│   ├── ITaskRepository.java        # Repositório JPA de tarefas
│   ├── TaskController.java         # Endpoints de tarefas
│   └── TaskModel.java              # Entidade Task (tb_tasks)
├── user/
│   ├── IUserRepository.java        # Repositório JPA de usuários
│   ├── UserController.java         # Endpoints de usuários
│   └── UserModel.java              # Entidade User (tb_users)
└── utils/
    └── Utils.java                  # Cópia de propriedades não nulas
```

## 🔌 Endpoints

| Método | Rota | Autenticação | Descrição |
| --- | --- | --- | --- |
| `POST` | `/users/` | ❌ | Cadastra um novo usuário |
| `POST` | `/tasks/` | ✅ Basic Auth | Cria uma tarefa |
| `GET` | `/tasks/` | ✅ Basic Auth | Lista as tarefas do usuário |
| `PUT` | `/tasks/{id}` | ✅ Basic Auth | Atualiza uma tarefa do usuário |

### Exemplos

**Criar usuário**

```bash
curl -X POST http://localhost:8080/users/ \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Jessica",
    "username": "jessica",
    "password": "12345"
  }'
```

**Criar tarefa**

```bash
curl -X POST http://localhost:8080/tasks/ \
  -u jessica:12345 \
  -H "Content-Type: application/json" \
  -d '{
    "title": "Estudar Spring Boot",
    "description": "Revisar JPA e filtros",
    "priority": "ALTA",
    "startAt": "2030-01-10T12:00:00",
    "endAt": "2030-01-10T18:00:00"
  }'
```

**Listar tarefas**

```bash
curl http://localhost:8080/tasks/ -u jessica:12345
```

**Atualizar tarefa**

Use o `id` retornado na criação ou na listagem. O comando abaixo pega o `id` da primeira tarefa da lista automaticamente:

```bash
TASK_ID=$(curl -s http://localhost:8080/tasks/ -u jessica:12345 | grep -o '"id":"[^"]*"' | head -1 | cut -d'"' -f4)

curl -X PUT http://localhost:8080/tasks/$TASK_ID \
  -u jessica:12345 \
  -H "Content-Type: application/json" \
  -d '{ "priority": "BAIXA" }'
```

## 🚀 Como executar

### Pré-requisitos

- Java 17+
- (Opcional) Docker

### Com o Maven Wrapper

```bash
# Linux / macOS
./mvnw spring-boot:run

# Windows
mvnw.cmd spring-boot:run
```

A API ficará disponível em `http://localhost:8080`.

### Com Docker

```bash
docker build -t todolist .
docker run -p 8080:8080 todolist
```

### Console do H2

Com o console do H2 habilitado, você pode inspecionar o banco em `http://localhost:8080/h2-console`:

- **JDBC URL:** `jdbc:h2:mem:todolist`
- **Usuário:** `admin`
- **Senha:** `admin`

> ⚠️ Como o banco é em memória, os dados são perdidos sempre que a aplicação é reiniciada.

## 📚 Aprendizados

Durante o desenvolvimento deste projeto foram praticados:

- Criação de APIs REST com Spring Boot;
- Modelagem de entidades e persistência com JPA/Hibernate;
- Uso de repositórios com Spring Data e *query methods* (`findByUsername`, `findByIdUser`);
- Criação de filtros HTTP para autenticação;
- Criptografia de senhas com BCrypt;
- Validações de regras de negócio e tratamento de exceções;
- Redução de boilerplate com Lombok;
- Containerização da aplicação com Docker (*multi-stage build*).

---

Feito com ☕ por **Jessica Ranft** durante o curso de Java da Rocketseat.
