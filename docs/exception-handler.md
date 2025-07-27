# Spring Boot – Tratamento Global de Exceções com `@ControllerAdvice`

---

## Problema
Como tratar exceções em uma API **Spring Boot** de forma centralizada, evitando duplicação de código e garantindo respostas consistentes para erros?

---

## Solução
Utilize **`@ControllerAdvice`** combinado com **`@ExceptionHandler`** para capturar exceções globalmente e personalizar as respostas HTTP.

---

## Exemplo básico
```java
@ControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(NotFoundException.class)
    public ResponseEntity<String> handleNotFound(NotFoundException e) {
        return ResponseEntity.status(404).body(e.getMessage());
    }
}
```

- **`@ControllerAdvice`** → intercepta exceções lançadas por qualquer `@Controller` ou `@RestController`.
- **`@ExceptionHandler`** → define qual tipo de exceção será tratado.
- **`ResponseEntity`** → permite retornar status HTTP e corpo da resposta.

---

## Melhorando a Resposta com DTO
```java
public record ErrorResponse(int status, String error, String message, LocalDateTime timestamp) {}

@ControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(NotFoundException.class)
    public ResponseEntity<ErrorResponse> handleNotFound(NotFoundException e) {
        ErrorResponse error = new ErrorResponse(
            HttpStatus.NOT_FOUND.value(),
            "Resource Not Found",
            e.getMessage(),
            LocalDateTime.now()
        );
        return ResponseEntity.status(HttpStatus.NOT_FOUND).body(error);
    }
}
```

---

## Exemplo com múltiplas exceções

### Validação de Campos
```java
@ExceptionHandler(MethodArgumentNotValidException.class)
public ResponseEntity<ErrorResponse> handleValidation(MethodArgumentNotValidException e) {
    String message = e.getBindingResult().getAllErrors().get(0).getDefaultMessage();
    ErrorResponse error = new ErrorResponse(
        HttpStatus.BAD_REQUEST.value(),
        "Validation Error",
        message,
        LocalDateTime.now()
    );
    return ResponseEntity.status(HttpStatus.BAD_REQUEST).body(error);
}
```

---

### Exceção Genérica
```java
@ExceptionHandler(Exception.class)
public ResponseEntity<ErrorResponse> handleGeneric(Exception e) {
    ErrorResponse error = new ErrorResponse(
        HttpStatus.INTERNAL_SERVER_ERROR.value(),
        "Internal Server Error",
        e.getMessage(),
        LocalDateTime.now()
    );
    return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).body(error);
}
```

---

## Estrutura de resposta JSON padrão
Exemplo de resposta para erro `404 Not Found`:
```json
{
  "status": 404,
  "error": "Resource Not Found",
  "message": "Produto não encontrado",
  "timestamp": "2025-07-27T15:30:00"
}
```

---

## Boas práticas
✅ Sempre retorne **status HTTP corretos**  
✅ Crie um **objeto padrão de erro** (JSON consistente)  
✅ Trate **validações (Bean Validation)** no `@ExceptionHandler`  
✅ Evite expor detalhes sensíveis no erro (stack trace)  
✅ Use logs para monitorar erros críticos  

---

## Referências
- [Spring Boot Docs – Exception Handling](https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-controller/ann-advice.html)
- [Bean Validation](https://hibernate.org/validator/)