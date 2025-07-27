# Documentação Interativa da API com Springdoc OpenAPI e Swagger

---

## Problema
Como gerar **documentação interativa** para APIs Spring Boot, permitindo testes de endpoints diretamente no navegador?

---

## Solução
Utilize **Springdoc OpenAPI** para integrar **Swagger UI** ao seu projeto Spring Boot.

---

### Links úteis
- 📘 [Springdoc OpenAPI](https://springdoc.org/)  
- 📘 [OpenAPI Specification (Swagger)](https://swagger.io/specification/)  

---

## Configuração no Spring Boot

### 1. Adicione a dependência no `pom.xml` (dependência oficial):
```xml
<dependency>
    <groupId>org.springdoc</groupId>
    <artifactId>springdoc-openapi-starter-webmvc-ui</artifactId>
    <version>2.5.0</version>
</dependency>
```

---

### 2. Configure o caminho da UI no `application.properties`:
```properties
springdoc.swagger-ui.path=/
```

Isso fará com que a interface Swagger seja carregada na raiz do projeto.

---

### 3. Acesse a documentação:
```
http://localhost:8080/
```

A UI interativa será exibida, permitindo:  
✅ Visualizar endpoints  
✅ Testar requisições (GET, POST, PUT, DELETE)  
✅ Enviar payloads e ver respostas  

---

## Exemplo de API Documentada
```java
@RestController
@RequestMapping("/produtos")
public class ProdutoController {

    @GetMapping("/{id}")
    public Produto findById(@PathVariable Long id) {
        return new Produto(id, "Produto Exemplo", 100.0);
    }

    @PostMapping
    public Produto create(@RequestBody Produto produto) {
        return produto;
    }
}
```

---

## Resultado
- Swagger UI lista automaticamente todos os endpoints da aplicação.
- Não precisa de configuração manual para cada rota (Springdoc faz isso por padrão).
- Gera documentação compatível com **OpenAPI 3**.

---

## Alternativas
- **Swagger Codegen** → gera clientes/servidores a partir do arquivo OpenAPI.
- **Redoc** → alternativa para visualização da documentação.
- **SpringFox** (descontinuado em projetos recentes).