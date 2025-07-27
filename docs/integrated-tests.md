# Spring Boot – Testes Integrados de API

---

## Problema
Como validar **a API inteira** (controladores, serviços e banco de dados) em um ambiente real de teste no **Spring Boot**, garantindo que tudo funciona integrado?

---

## Solução
Use **Spring Boot Test** com:
- `@SpringBootTest` → carrega o contexto completo da aplicação.
- `TestRestTemplate` → simula chamadas HTTP reais.
- **Banco em memória (H2)** ou **Testcontainers** para testes próximos ao ambiente real.

---

## Dependências
Adicione no `pom.xml`:
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
</dependency>

<!-- Para Testcontainers (opcional) -->
<dependency>
    <groupId>org.testcontainers</groupId>
    <artifactId>mysql</artifactId>
    <version>1.19.3</version>
    <scope>test</scope>
</dependency>
```

---

## Exemplo com `TestRestTemplate`
```java
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
class ProdutoApiIntegrationTest {

    @Autowired
    private TestRestTemplate restTemplate;

    @Test
    void testCriarProduto() {
        ProdutoDto produto = new ProdutoDto("Produto Teste", 99.90);

        ResponseEntity<ProdutoDto> response = restTemplate.postForEntity("/produtos", produto, ProdutoDto.class);

        assertThat(response.getStatusCode()).isEqualTo(HttpStatus.CREATED);
        assertThat(response.getBody().nome()).isEqualTo("Produto Teste");
    }
}
```

---

## Configuração com H2 (Banco em Memória)
Adicione no `application-test.properties`:
```properties
spring.datasource.url=jdbc:h2:mem:testdb
spring.datasource.driver-class-name=org.h2.Driver
spring.jpa.hibernate.ddl-auto=create-drop
spring.jpa.show-sql=true
```
Isso garante que os testes usem um banco em memória, descartando dados após cada execução.

---

## Exemplo com Testcontainers (MySQL)
```java
@Testcontainers
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
public class ProdutoApiContainerTest {

    @Container
    static MySQLContainer<?> mysql = new MySQLContainer<>("mysql:8.0")
            .withDatabaseName("testdb")
            .withUsername("root")
            .withPassword("root_pwd");

    @DynamicPropertySource
    static void configure(DynamicPropertyRegistry registry) {
        registry.add("spring.datasource.url", mysql::getJdbcUrl);
        registry.add("spring.datasource.username", mysql::getUsername);
        registry.add("spring.datasource.password", mysql::getPassword);
    }

    @Autowired
    private TestRestTemplate restTemplate;

    @Test
    void testListarProdutos() {
        ResponseEntity<String> response = restTemplate.getForEntity("/produtos", String.class);
        assertThat(response.getStatusCode()).isEqualTo(HttpStatus.OK);
    }
}
```

---

## Validação avançada com JSON
```java
ResponseEntity<String> response = restTemplate.getForEntity("/produtos/1", String.class);

assertThat(response.getStatusCode()).isEqualTo(HttpStatus.OK);
assertThat(response.getBody()).contains(""nome":"Produto Teste"");
```

---

## Boas práticas
✅ Use `@SpringBootTest(webEnvironment = RANDOM_PORT)` para testes reais de API  
✅ Prefira **Testcontainers** para maior realismo em testes de banco  
✅ Use **H2** para testes rápidos sem dependência externa  
✅ Sempre **limpe dados** ou use `@DirtiesContext` para isolamento  
✅ Combine com **profiles de teste** (`spring.profiles.active=test`)  

---

## Referências
- [Spring Boot Testing Docs](https://docs.spring.io/spring-boot/docs/current/reference/html/features.html#features.testing)
- [Testcontainers Documentation](https://www.testcontainers.org/)