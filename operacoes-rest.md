# Operações REST com Spring Boot (`@GetMapping`, `@PostMapping`, `@PutMapping`, `@DeleteMapping`)

---

## Problema
Como implementar um **CRUD REST** em **Spring Boot** usando as anotações específicas para cada tipo de operação HTTP?

---

## Solução
Spring Boot disponibiliza anotações para mapear métodos HTTP a operações comuns:

- `@GetMapping` → **Leitura** (buscar dados)
- `@PostMapping` → **Criação** (salvar novos dados)
- `@PutMapping` → **Atualização** (atualizar recursos existentes)
- `@DeleteMapping` → **Exclusão** (remover recursos)

### Exemplo: CRUD para Produto
```java
@RestController
@RequestMapping("/produtos")
public class ProdutoController {

    @GetMapping("/{id}")
    public Produto findById(@PathVariable Long id) {
        // Lógica para buscar produto pelo ID
        return new Produto(id, "Produto Exemplo", 100.0);
    }

    @PostMapping
    public Produto create(@RequestBody Produto produto) {
        // Lógica para criar novo produto
        return produto;
    }

    @PutMapping("/{id}")
    public Produto update(@PathVariable Long id, @RequestBody Produto produto) {
        // Lógica para atualizar produto existente
        produto.setId(id);
        return produto;
    }

    @DeleteMapping("/{id}")
    public String delete(@PathVariable Long id) {
        // Lógica para remover produto pelo ID
        return "Produto com ID " + id + " removido com sucesso!";
    }
}
```

### Modelo da Classe Produto
```java
public class Produto {
    private Long id;
    private String nome;
    private Double preco;

    // Construtores, Getters e Setters
}
```

---

## Rotas e Operações

| Método HTTP | Rota              | Operação           |
|-------------|-------------------|--------------------|
| `GET`       | `/produtos/{id}` | Buscar produto     |
| `POST`      | `/produtos`      | Criar produto      |
| `PUT`       | `/produtos/{id}` | Atualizar produto  |
| `DELETE`    | `/produtos/{id}` | Excluir produto    |

---

## Alternativas

1. **Usar `@RequestMapping(method = ...)`**
   ```java
   @RequestMapping(value = "/{id}", method = RequestMethod.GET)
   public Produto findById(@PathVariable Long id) { ... }
   ```

2. **Usar PATCH para atualizações parciais**
   ```java
   @PatchMapping("/{id}")
   public Produto partialUpdate(@PathVariable Long id, @RequestBody Map<String, Object> updates) { ... }
   ```

3. **Utilizar ResponseEntity para status HTTP**
   ```java
   @PostMapping
   public ResponseEntity<Produto> create(@RequestBody Produto produto) {
       return ResponseEntity.status(HttpStatus.CREATED).body(produto);
   }
   ```