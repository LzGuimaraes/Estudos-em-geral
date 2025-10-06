# 🔐 Estudo de Spring Security

> Documento de estudo sobre autenticação, autorização e segurança em aplicações Spring Boot.

---

## 📘 Sumário
- [1. O que é o Spring Security](#1-o-que-é-o-spring-security)
- [2. Conceitos Fundamentais](#2-conceitos-fundamentais)
- [3. Configuração Básica](#3-configuração-básica)
- [4. Fluxo de Autenticação](#4-fluxo-de-autenticação)
- [5. Criação de Usuário e Senha Criptografada](#5-criação-de-usuário-e-senha-criptografada)
- [6. Autorização com Roles](#6-autorização-com-roles)
- [7. Autenticação com JWT](#7-autenticação-com-jwt)
- [8. Filtragem de Requisições](#8-filtragem-de-requisições)
- [9. Tratamento de Erros e Respostas Personalizadas](#9-tratamento-de-erros-e-respostas-personalizadas)
- [10. Boas Práticas de Segurança](#10-boas-práticas-de-segurança)
- [11. Recursos e Leituras Recomendadas](#11-recursos-e-leituras-recomendadas)

---

## 1. O que é o Spring Security

Spring Security é o módulo do **Spring Framework** responsável por:
- 🔑 Autenticação (login)
- 🧩 Autorização (controle de acesso)
- 🔒 Proteção contra ataques comuns (CSRF, XSS, Session Fixation)

Ele se integra ao Spring Boot de forma automática e é altamente configurável.

---

## 2. Conceitos Fundamentais

| Conceito | Descrição |
|-----------|------------|
| **Authentication** | Processo de verificar a identidade do usuário (login) |
| **Authorization** | Verifica o que o usuário autenticado pode fazer |
| **Principal** | Representa o usuário autenticado |
| **GrantedAuthority / Role** | Define os privilégios do usuário |
| **SecurityContextHolder** | Armazena as informações de segurança do usuário ativo |
| **FilterChain** | Cadeia de filtros que processam a autenticação e autorização |

---

## 3. Configuração Básica

Adicione a dependência no `pom.xml`:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

Ao iniciar a aplicação:

- O Spring cria um usuário padrão: user

- E uma senha gerada automaticamente (visível no log)

## 4. Fluxo de Autenticação

O **fluxo de autenticação** no Spring Security define o passo a passo que o framework segue para validar um usuário e manter seu estado de segurança ativo durante as requisições.
---
### 🧭 Visão Geral do Fluxo

1. O **usuário envia credenciais** (login e senha) para o endpoint de autenticação (`/login` ou `/auth/login`).
2. O **AuthenticationManager** recebe essas credenciais e delega a validação para o **AuthenticationProvider**.
3. O **UserDetailsService** busca o usuário no banco de dados pelo `username`.
4. O **PasswordEncoder** compara a senha enviada com a senha criptografada armazenada.
5. Se estiver tudo certo, o **SecurityContextHolder** armazena o usuário autenticado no contexto de segurança.
6. O **JWT** é gerado e devolvido ao cliente.
7. Em cada nova requisição, o **JwtAuthFilter** intercepta, valida o token e restaura o contexto do usuário autenticado.

---

### ⚙️ Componentes Envolvidos no Fluxo
| Componente | Responsabilidade |
|-------------|------------------|
| **AuthenticationManager** | Gerencia o processo de autenticação. Recebe o token de autenticação (usuário e senha) e delega para o provedor correto. |
| **AuthenticationProvider** | Implementa a lógica real de autenticação (validação de credenciais). |
| **UserDetailsService** | Busca o usuário no banco de dados e retorna um objeto `UserDetails`. |
| **UserDetails** | Interface que representa o usuário autenticado (implementada na sua entidade `UserModel`). |
| **PasswordEncoder** | Compara e criptografa senhas (ex: `BCryptPasswordEncoder`). |
| **SecurityContextHolder** | Armazena o usuário autenticado e suas permissões em uma thread local. |
| **JwtService** | Gera e valida o token JWT. |
| **JwtAuthFilter** | Intercepta requisições HTTP, extrai e valida o token JWT antes de chegar ao controller. |

### 🧩 Etapas Detalhadas

#### 🟦 4.1 Envio de Credenciais
O cliente envia um `POST` com `username` e `password`:

```json
POST /auth/login
{
  "username": "luiz",
  "password": "1234"
}
```

#### 🟨 4.2 Criação do Token de Autenticação

No controller, é criado um objeto do tipo `UsernamePasswordAuthenticationToken`:
```java
Authentication authentication = new UsernamePasswordAuthenticationToken(
    loginDTO.getUsername(),
    loginDTO.getPassword());
```
Esse objeto ainda não está autenticado — ele serve apenas para transportar as credenciais até o AuthenticationManager.

#### 🟩 4.3 Autenticação via AuthenticationManager
```java
authenticationManager.authenticate(authentication);
```
👉 O `AuthenticationManager` verifica qual `AuthenticationProvider` consegue lidar com esse tipo de autenticação.
No caso padrão, ele utiliza `DaoAuthenticationProvider`.

#### 🟪 4.4 Validação pelo UserDetailsService
O `DaoAuthenticationProvider` usa o `UserDetailsService` que você implementou:
```java
@Override
public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
    return userRepository.findByUsername(username)
        .orElseThrow(() -> new UsernameNotFoundException("Usuário não encontrado"));
}
```
➡️ Ele busca o usuário no banco e retorna um objeto UserDetails contendo:

- `username`
- `password` (criptografada)
- `authorities` (roles/permissões)

#### 🟧 4.5 Validação de Senha
Após encontrar o usuário, o Spring compara a senha enviada com a senha armazenada usando o
`PasswordEncoder`:
```java
passwordEncoder.matches(rawPassword, encodedPassword);
```
⚙️ O `BCryptPasswordEncoder` aplica um hash seguro e com salt, tornando quase impossível quebrar a senha por força bruta.
#### 🟥 4.6 Criação do Contexto de Segurança
Se as credenciais estiverem corretas, o Spring cria um objeto `Authentication` completo (autenticado) e armazena no contexto:
```java
SecurityContextHolder.getContext().setAuthentication(authentication);
```
Assim, durante toda a requisição, você pode obter o usuário autenticado com:
#### 🟫 4.7 Geração do JWT (após autenticação)
Após validar o login, o sistema gera um **token JWT**:
```java
String token = jwtService.generateToken((UserModel) user);
```
Esse token contém:

- O nome do usuário `(subject)`
- A role `(claim)`
- A data de expiração `(exp)`
- Uma assinatura digital `(signature)`

Ele é retornado ao cliente no corpo da resposta ou no cabeçalho.
#### 🟦 4.8 Envio do Token em Requisições Futuras
O cliente envia o token em cada requisição protegida:
```md
Authorization: Bearer <seu_token_jwt>
```
#### 🟩 9. Validação do Token pelo JwtAuthFilter

```java
String authHeader = request.getHeader("Authorization");

if (authHeader != null && authHeader.startsWith("Bearer ")) {
    String token = authHeader.substring(7);
    String username = jwtService.extractUsername(token);

    if (username != null && SecurityContextHolder.getContext().getAuthentication() == null) {
        UserDetails user = userService.loadUserByUsername(username);
        if (jwtService.isTokenValid(token, user)) {
            UsernamePasswordAuthenticationToken authToken =
                new UsernamePasswordAuthenticationToken(user, null, user.getAuthorities());
            SecurityContextHolder.getContext().setAuthentication(authToken);
        }
    }
}
```
👉 Assim, o usuário não precisa mais enviar login/senha em cada requisição — basta o token válido.
```txt
[Usuário] --login--> [AuthController]
      ↓
[AuthenticationManager] 
      ↓
[UserDetailsService] → [Banco de Dados]
      ↓
[PasswordEncoder] → valida senha
      ↓
[SecurityContextHolder] → armazena autenticação
      ↓
[JwtService] → gera token
      ↓
[Cliente] → guarda token → envia em cada requisição
      ↓
[JwtAuthFilter] → valida token e reautentica usuário
```
---

## 5. Criação de Usuário e Senha Criptografada
```java
Authentication auth = SecurityContextHolder.getContext().getAuthentication();
String username = auth.getName();
```
Use o `BCryptPasswordEncoder` para criptografar senhas.

### Exemplo:
```java
@Configuration
@EnableWebSecurity
public class SecurityConfig {

    @Bean
    public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
        http
            .csrf(csrf -> csrf.disable())
            .authorizeHttpRequests(auth -> auth
                .anyRequest().authenticated()
            )
            .httpBasic(Customizer.withDefaults());

        return http.build();
    }

    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }
}
```

#### Criptografando senha manualmente:
```java
String senhaCriptografada = new BCryptPasswordEncoder().encode("123456");
```

## 6. Autorização com Roles

Crie uma entidade de usuário com roles (ex: `ADMIN`, `USER`):

```java
@Entity
public class UserModel implements UserDetails {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String username;
    private String password;

    @Enumerated(EnumType.STRING)
    private Role role;

    @Override
    public Collection<? extends GrantedAuthority> getAuthorities() {
        return List.of(new SimpleGrantedAuthority("ROLE_" + role.name()));
    }
}
```

#### Enum:

```java
public enum Role {
    ADMIN, USER
}
```

#### Restrição por rota:

```java
http.authorizeHttpRequests(auth -> auth
    .requestMatchers("/admin/**").hasRole("ADMIN")
    .requestMatchers("/user/**").hasAnyRole("USER", "ADMIN")
    .anyRequest().authenticated()
);
```

## 7. Autenticação com JWT

#### Fluxo JWT:

1. O usuário envia e-mail e senha.

2. O servidor valida e gera um token JWT.

3. O cliente envia o token no cabeçalho Authorization: Bearer <token>.

4. O filtro do Spring valida o token a cada requisição.

#### Dependência:
```xml 
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt-api</artifactId>
    <version>0.11.5</version>
</dependency>
```

#### Exemplo de geração de token:

```java
String token = Jwts.builder()
    .setSubject(user.getUsername())
    .setExpiration(new Date(System.currentTimeMillis() + 86400000))
    .signWith(SignatureAlgorithm.HS256, secretKey)
    .compact();
```

#### Validação no filtro:

```java
public class JwtAuthFilter extends OncePerRequestFilter {
    @Override
    protected void doFilterInternal(HttpServletRequest request,
                                    HttpServletResponse response,
                                    FilterChain filterChain)
                                    throws ServletException, IOException {

        String authHeader = request.getHeader("Authorization");

        if (authHeader != null && authHeader.startsWith("Bearer ")) {
            String token = authHeader.substring(7);
            // validar token e setar usuário autenticado
        }

        filterChain.doFilter(request, response);
    }
}
```

## 8. Filtragem de Requisições
A Filter Chain do Spring processa as requisições em ordem.
Você pode adicionar filtros customizados com:
```java
http.addFilterBefore(new JwtAuthFilter(), UsernamePasswordAuthenticationFilter.class);
```

## 9. Tratamento de Erros e Respostas Personalizadas
Para personalizar erros de autenticação:

```java
http.exceptionHandling(ex -> ex
    .authenticationEntryPoint((req, res, excep) -> {
        res.sendError(HttpServletResponse.SC_UNAUTHORIZED, "Token inválido ou ausente");
    })
);
```

## 10. Boas Práticas de Segurança

- ✅ Sempre use BCryptPasswordEncoder

- 🚫 Nunca exponha tokens JWT no front-end (localStorage)

- 🧱 Desative csrf apenas em APIs REST

- 🕒 Defina tempo de expiração curto para tokens

- 🧩 Use variáveis de ambiente para segredos (application.properties)

- 🔄 Sempre valide o payload e roles dentro do token
