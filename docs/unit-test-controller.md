# Spring Boot – Testes Unitários com MockMvc

---

## Problema
Como testar endpoints de uma **API Spring Boot** sem subir um servidor real, garantindo que as respostas e status HTTP estejam corretos?

---

## Solução
Use **MockMvc**, uma ferramenta do Spring para testes **de integração simulando requisições HTTP**, sem necessidade de abrir portas ou rodar a aplicação completa.

---

## Dependência
`spring-boot-starter-test` já inclui tudo:
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
</dependency>
```

---

## Exemplo básico com MockMvc
```java
@SpringBootTest
@AutoConfigureMockMvc
class ProdutoControllerTest {

    @Autowired
    MockMvc mockMvc;

    @Test
    void testPing() throws Exception {
        mockMvc.perform(get("/ping"))
               .andExpect(status().isOk())
               .andExpect(content().string("pong"));
    }
}
```

### Explicação
- `@SpringBootTest` → carrega o contexto completo da aplicação.
- `@AutoConfigureMockMvc` → habilita o uso do MockMvc.
- `mockMvc.perform(...)` → simula requisição HTTP.
- `andExpect(...)` → validações como status e conteúdo.

---

## Testando POST com JSON
```java
@Test
void testCreateProduto() throws Exception {
    String json = "{\"nome\":\"Produto Teste\",\"preco\":100.0}";

    mockMvc.perform(post("/produtos")
            .contentType(MediaType.APPLICATION_JSON)
            .content(json))
        .andExpect(status().isCreated())
        .andExpect(jsonPath("$.nome").value("Produto Teste"))
        .andExpect(jsonPath("$.preco").value(100.0));
}
```

---

## Testando com parâmetros e query
```java
mockMvc.perform(get("/produtos")
        .param("nome", "Produto"))
    .andExpect(status().isOk());
```

---

## Testando retorno JSON com jsonPath
```java
mockMvc.perform(get("/produtos/1"))
    .andExpect(status().isOk())
    .andExpect(jsonPath("$.id").value(1))
    .andExpect(jsonPath("$.nome").value("Produto Teste"));
```

---

## Mockando camada de serviço com Mockito
Para isolar o controller e não chamar banco:
```java
@WebMvcTest(ProdutoController.class)
class ProdutoControllerUnitTest {

    @Autowired MockMvc mockMvc;
    @MockBean ProdutoService produtoService;

    @Test
    void testList() throws Exception {
        when(produtoService.listar()).thenReturn(List.of(new ProdutoDto("Teste", 100.0)));

        mockMvc.perform(get("/produtos"))
               .andExpect(status().isOk())
               .andExpect(jsonPath("$[0].nome").value("Teste"));
    }
}
```

---

## Boas práticas
✅ Use `@WebMvcTest` para testes unitários focados no controller  
✅ Use `@SpringBootTest` para testes de integração  
✅ Combine com **JUnit 5** e **Mockito**  
✅ Valide dados com `jsonPath` para respostas JSON  
✅ Cubra **status HTTP**, **conteúdo**, **camada mockada**  

---

## Referências
- [Spring Testing Docs](https://docs.spring.io/spring-boot/docs/current/reference/html/features.html#features.testing)
- [Mockito Docs](https://site.mockito.org/)