# 📘 Guia Detalhado: Spring Data JPA — Entendimento Completo

## 1. 🧠 Entendendo o JPA no Spring Boot

O **Spring Data JPA** é o módulo do Spring responsável por fazer a **ponte entre o código Java e o banco de dados** relacional (como PostgreSQL, MySQL, etc).  
Ele utiliza a **especificação JPA (Jakarta Persistence API)** e, por padrão, o **Hibernate** como provedor ORM.

---

### 🧩 Visão Geral

Fluxo de dados em uma aplicação típica:

```text
Controller ⇄ Service ⇄ Repository ⇄ Banco de Dados
              ↑
             DTO
```
- **Controller**: recebe a requisição HTTP (com JSON do cliente)
- **DTO**: representa os dados transferidos (Data Transfer Object)
- **Service**: contém a lógica de negócio
- **Repository**: conversa com o banco de dados
- **Model (Entity)**: representa uma tabela no banco

### 🏗️ 1. Como o JPA funciona por baixo dos panos

O JPA converte objetos Java em registros de banco e vice-versa.
Esse processo é chamado de **ORM (Object-Relational Mapping).**

#### 🔄 Exemplo de fluxo:
1. Você cria uma entidade Java anotada com `@Entity`:
```java
@Entity
@Table(name = "users")
public class UserModel {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String nome;
    private String email;
    private int idade;
}
```
2. O Hibernate traduz essa entidade em tabela SQL:
```sql
CREATE TABLE users (
    id BIGINT AUTO_INCREMENT PRIMARY KEY,
    nome VARCHAR(255),
    email VARCHAR(255),
    idade INT
);
```
```java
3. Quando você chama o `save()` no repositório:
```

O JPA faz algo como:
```sql
INSERT INTO users (nome, email, idade) VALUES (?, ?, ?)
```
4. E quando você chama findAll():
```java
SELECT * FROM users;
```
Você **nunca precisa escrever SQL diretamente**, a menos que queira.

## 🧮 2. Como o Spring cria as Queries automaticamente
O JPA Repository possui **métodos automáticos** e **query derivadas**.
Exemplo:
```java
public interface UserRepository extends JpaRepository<UserModel, Long> {
    List<UserModel> findByNome(String nome);
    Optional<UserModel> findByEmail(String email);
    List<UserModel> findByIdadeGreaterThan(int idade);
}
```
Sem precisar escrever SQL, o Spring gera internamente:
```sql
SELECT * FROM users WHERE nome = ?;
SELECT * FROM users WHERE email = ?;
SELECT * FROM users WHERE idade > ?;
```
## ⚙️ 3. Como o Service interage com o Repository
```java
@Service
public class UserService {

    @Autowired
    private UserRepository userRepository;

    public List<UserModel> listarUsuarios() {
        return userRepository.findAll();
    }

    public UserModel criarUsuario(UserDTO dto) {
        UserModel user = new UserModel();
        user.setNome(dto.getNome());
        user.setEmail(dto.getEmail());
        user.setIdade(dto.getIdade());
        return userRepository.save(user);
    }
}
```
➡️ O `Service` recebe dados do `DTO`, converte para `Model`, e o `Repository` salva no banco.

## 🧱 4. Papel do Model e do DTO

| Camada                         | Responsabilidade                                                                                                         |
| ------------------------------ | ------------------------------------------------------------------------------------------------------------------------ |
| **Model (Entity)**             | Representa a estrutura da tabela no banco de dados. Possui anotações JPA (`@Entity`, `@Id`, etc).                        |
| **DTO (Data Transfer Object)** | Usado para trafegar dados entre camadas, sem expor a entidade diretamente. Pode ter menos ou mais campos do que o Model. |

---

## 🧩 5. O que acontece se o DTO não tiver todos os campos?
**👉 O JPA só salva o que você setar no Model.**
Exemplo:
```java
public class UserDTO {
    private String nome;
    private String email;
}
```
E o model tem um campo a mais
```java
@Entity
public class UserModel {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String nome;
    private String email;
    private int idade;
}

```

Se você fizer:

```java
UserModel user = new UserModel();
user.setNome(dto.getNome());
user.setEmail(dto.getEmail());
userRepository.save(user);
```

👉 O campo `idade` vai ser `null` (ou `0`, se `int`) no banco, **a não ser que você defina um valor padrão** ou mantenha o registro existente:

```java
UserModel user = userRepository.findById(id).orElseThrow();
user.setNome(dto.getNome());
user.setEmail(dto.getEmail());
// não muda a idade, mantém a do banco
userRepository.save(user);
```

⚠️ Resumo importante:

- O DTO **não controla o que vai para o banco** — quem faz isso é o **Model**.
- O JPA **só persiste** o que estiver preenchido no Model no momento do `save()`.
- Campos não mapeados no DTO **simplesmente não são alterados**, a menos que você sobrescreva.

## 🧰 6. Query personalizada com @Query

Você também pode escrever SQL ou JPQL manualmente:

## 🧠 7. Ciclo completo de persistência

```text
1. Controller recebe o DTO
2. Service converte DTO → Model
3. Repository salva o Model
4. JPA gera SQL automaticamente
5. Hibernate envia SQL ao banco
6. Banco confirma e devolve resultado
7. JPA converte resultado → Model
8. Service converte Model → DTO de resposta
```

## 🔍 8. Métodos importantes e suas funções

| Método               | Classe          | Descrição                                               |
| -------------------- | --------------- | ------------------------------------------------------- |
| **`save(entity)`**   | `JpaRepository` | Insere ou atualiza um registro.                         |
| **`findAll()`**      | `JpaRepository` | Retorna todos os registros da tabela.                   |
| **`findById(id)`**   | `JpaRepository` | Busca um registro pelo ID.                              |
| **`deleteById(id)`** | `JpaRepository` | Remove um registro específico.                          |
| **`@Query()`**       | `JpaRepository` | Permite definir queries customizadas.                   |
| **`@Modifying`**     | `JpaRepository` | Indica queries de atualização ou exclusão.              |
| **`flush()`**        | `EntityManager` | Força a execução de operações pendentes no banco.       |
| **`persist()`**      | `EntityManager` | Adiciona uma nova entidade ao contexto de persistência. |
| **`merge()`**        | `EntityManager` | Atualiza uma entidade existente.                        |

## 🧱 Fluxo visual resumido

```text
[Cliente JSON]
   ↓
[Controller] → recebe DTO
   ↓
[Service] → converte DTO → Model
   ↓
[Repository] → salva Model no banco
   ↓
[JPA/Hibernate] → gera SQL e executa
   ↓
[Banco de Dados]
   ↑
[Model retornado]
   ↑
[Service] → Model → DTO de resposta
   ↑
[Controller] → retorna JSON
   ↑
[Cliente]
```
