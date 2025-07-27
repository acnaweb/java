# Spring Security – Autenticação com Basic Auth e JWT no Spring Boot

---

## Problema
Como proteger uma API **Spring Boot** garantindo autenticação segura, seja com **Basic Authentication** ou com **JWT (JSON Web Token)** para aplicações modernas?

---

## Solução
Use **Spring Security**, que oferece recursos completos para autenticação e autorização.

---

## 1. Autenticação com Basic Auth

### Dependência
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

### Configuração simples
Por padrão, o Spring Security habilita **Basic Auth** automaticamente.  
No console, será exibida uma senha gerada:
```
Using generated security password: 5a8b6c4e-d...
```

### Customizando usuário e senha
No `application.properties`:
```properties
spring.security.user.name=admin
spring.security.user.password=admin123
spring.security.user.roles=USER
```

### Testando com cURL
```bash
curl -u admin:admin123 http://localhost:8080/produtos
```

---

## 2. Autenticação com JWT no Spring Boot

### Dependências
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt-api</artifactId>
    <version>0.11.5</version>
</dependency>
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt-impl</artifactId>
    <version>0.11.5</version>
    <scope>runtime</scope>
</dependency>
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt-jackson</artifactId>
    <version>0.11.5</version>
    <scope>runtime</scope>
</dependency>
```

---

### Exemplo de implementação JWT

#### Gerando token JWT
```java
public String generateToken(String username) {
    return Jwts.builder()
        .setSubject(username)
        .setIssuedAt(new Date())
        .setExpiration(new Date(System.currentTimeMillis() + 86400000)) // 1 dia
        .signWith(SignatureAlgorithm.HS256, "chave-secreta")
        .compact();
}
```

#### Validando token JWT
```java
public String validateToken(String token) {
    return Jwts.parser()
        .setSigningKey("chave-secreta")
        .parseClaimsJws(token)
        .getBody()
        .getSubject();
}
```

---

### Criando filtro JWT
```java
@Component
public class JwtAuthFilter extends OncePerRequestFilter {

    @Override
    protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response, FilterChain filterChain)
            throws ServletException, IOException {
        String authHeader = request.getHeader("Authorization");
        if (authHeader != null && authHeader.startsWith("Bearer ")) {
            String token = authHeader.substring(7);
            String username = validateToken(token);
            if (username != null) {
                UsernamePasswordAuthenticationToken auth =
                        new UsernamePasswordAuthenticationToken(username, null, List.of(new SimpleGrantedAuthority("USER")));
                SecurityContextHolder.getContext().setAuthentication(auth);
            }
        }
        filterChain.doFilter(request, response);
    }
}
```

---

### Configurando Security para JWT
```java
@Configuration
@EnableWebSecurity
public class SecurityConfig {

    @Autowired
    private JwtAuthFilter jwtAuthFilter;

    @Bean
    public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
        return http.csrf().disable()
            .authorizeHttpRequests(auth -> auth
                .requestMatchers("/auth/**").permitAll()
                .anyRequest().authenticated()
            )
            .addFilterBefore(jwtAuthFilter, UsernamePasswordAuthenticationFilter.class)
            .build();
    }
}
```

---

### Controller para gerar token
```java
@RestController
@RequestMapping("/auth")
public class AuthController {

    @PostMapping("/login")
    public String login(@RequestParam String username, @RequestParam String password) {
        // Validação mock
        if ("user".equals(username) && "123".equals(password)) {
            return generateToken(username);
        }
        throw new RuntimeException("Credenciais inválidas");
    }
}
```

---

### Testando com cURL
```bash
# Gerar token
curl -X POST "http://localhost:8080/auth/login?username=user&password=123"

# Acessar recurso protegido
curl -H "Authorization: Bearer SEU_TOKEN" http://localhost:8080/produtos
```

---

## Boas práticas
✅ Use **HTTPS** sempre  
✅ Armazene **chave secreta** em variáveis de ambiente  
✅ Utilize **refresh tokens** para renovar JWT  
✅ Não use Basic Auth em produção sem criptografia  

---

## Referências
- [Spring Security Docs](https://docs.spring.io/spring-security/reference/)
- [JWT Official Site](https://jwt.io/)