# Spring Boot – Validação de Entradas com Bean Validation

---

## Problema
Como validar os dados enviados pelo cliente em uma API **Spring Boot**, garantindo consistência e evitando regras de negócio quebradas?

---

## Solução
Use **Bean Validation (Jakarta Validation)** junto com anotações como `@NotBlank`, `@Size`, `@Email`, etc. O Spring Boot já oferece suporte nativo via **Hibernate Validator**.

---

## Exemplo com Record DTO
```java
public record ProdutoDto(
    @NotBlank(message = "Nome é obrigatório")
    String nome
) { }
```

- `@NotBlank` → não permite nulo nem string vazia.
- `message` → define a mensagem de erro personalizada.

---

## Exemplo com múltiplas validações
```java
public record ProdutoDto(
    @NotBlank(message = "Nome é obrigatório")
    @Size(min = 3, max = 50, message = "Nome deve ter entre 3 e 50 caracteres")
    String nome,

    @NotNull(message = "Preço é obrigatório")
    @Positive(message = "Preço deve ser positivo")
    BigDecimal preco
) { }
```

---

## Controlador com validação
```java
@PostMapping
public ResponseEntity<String> criar(@Valid @RequestBody ProdutoDto dto) {
    return ResponseEntity.ok("Produto criado: " + dto.nome());
}
```

- **`@Valid`** → ativa a validação do DTO no momento da requisição.

---

## Tratamento de Erros de Validação
Por padrão, se a validação falhar, o Spring retorna `400 Bad Request` com informações dos campos inválidos.  
Para **personalizar a resposta**, use um **Global Exception Handler**:

```java
@ControllerAdvice
public class ValidationExceptionHandler {

    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<Map<String, String>> handleValidation(MethodArgumentNotValidException ex) {
        Map<String, String> errors = new HashMap<>();
        ex.getBindingResult().getFieldErrors()
            .forEach(error -> errors.put(error.getField(), error.getDefaultMessage()));
        return ResponseEntity.badRequest().body(errors);
    }
}
```

---

## Exemplo de resposta JSON personalizada
```json
{
  "nome": "Nome é obrigatório",
  "preco": "Preço deve ser positivo"
}
```

---

## Outras anotações úteis
| Anotação      | Descrição                              |
|--------------|-----------------------------------------|
| `@NotNull`   | Não pode ser nulo                      |
| `@NotEmpty`  | Não pode ser nulo nem vazio            |
| `@NotBlank`  | Não pode ser nulo nem string vazia     |
| `@Size(min,max)` | Define tamanho mínimo e máximo      |
| `@Email`     | Valida formato de e-mail              |
| `@Positive`  | Apenas números positivos              |

---

## Boas práticas
✅ Use **DTOs** para receber dados da API  
✅ Centralize mensagens no **`messages.properties`** para internacionalização  
✅ Combine validação com **Exception Handler** para resposta padronizada  
✅ Use `@Validated` em **Services** para validação programática  

---

## Referências
- [Jakarta Bean Validation](https://jakarta.ee/specifications/bean-validation/)
- [Spring Boot Validation Docs](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#io.validation)