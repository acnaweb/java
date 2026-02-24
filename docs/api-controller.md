# Controller simples em Spring Boot para teste de rotas (`@RestController` + `@RequestMapping`)

## Problema  
Como criar um controlador REST no **Spring Boot** que disponibilize rotas simples para verificar a saúde da aplicação e responder com mensagens estáticas?

## Solução  
A classe `PingController` usa as anotações:  

- `@RestController`: indica que a classe é um controlador REST, retornando dados diretamente no corpo da resposta.  
- `@RequestMapping("/ping")`: define a URL base `/ping` para todas as rotas do controlador.  
- `@GetMapping`: define endpoints HTTP GET.  

### Código:
```java
@RestController
@RequestMapping("/ping")
public class PingController {

    @GetMapping
    public String ping() {
        return "pong"; // Resposta padrão
    }
}
```

### Comportamento:
- `GET /ping` → `"pong"`  

## Alternativas  

1. **Usar `@Controller` + `@ResponseBody`**  
   Em vez de `@RestController` (que já combina as duas anotações), você pode fazer:  
   ```java
   @Controller
   @RequestMapping("/ping")
   public class PingController {

       @ResponseBody
       @GetMapping
       public String ping() {
           return "pong";
       }
   }
   ```

2. **Retornar JSON em vez de String**  
   Para seguir boas práticas REST, você pode retornar um objeto:  
   ```java
   @GetMapping
   public Map<String, String> ping() {
       return Collections.singletonMap("status", "pong");
   }
   ```

3. **Centralizar rotas com `@RestControllerAdvice` (boa para APIs maiores)**  
   Ideal quando você tem respostas padrão para status ou erros.
