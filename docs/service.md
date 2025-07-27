# Service no Spring Boot

---

## Problema
Como separar a **lógica de negócio** da camada de **controladores (Controller)** em uma aplicação Spring Boot?

---

## Solução
A camada **Service** concentra as regras de negócio e orquestra a comunicação com o repositório e outras camadas.

No Spring Boot, essa camada é representada por classes anotadas com `@Service`.

---

### Exemplo de implementação
```java
@Service
public class ProdutoService {

    private final ProdutoRepository repository;

    public ProdutoService(ProdutoRepository repository) {
        this.repository = repository;
    }

    public ProdutoDto findById(Long id) {
        return repository.findById(id)
            .map(this::toDto)
            .orElseThrow(() -> new RuntimeException("Produto não encontrado"));
    }

    private ProdutoDto toDto(Produto produto) {
        return new ProdutoDto(produto.getId(), produto.getNome(), produto.getPreco());
    }
}
```

---

## Como funciona
- `@Service` indica que a classe faz parte da camada de serviços.
- Injeta dependências do repositório via **construtor** (boa prática para testes).
- Implementa a **regra de negócio** (ex.: conversão para DTO, validações, exceções).

---

## Boas práticas
✅ **Não misturar regras de negócio no Controller**  
✅ **Utilizar DTOs** para expor apenas dados necessários  
✅ **Tratar exceções** com `@ControllerAdvice`  
✅ **Injeção via construtor** (evita problemas com testes e beans)

---

## Alternativas
- **`@Component`** (genérico), mas `@Service` é mais semântico para lógica de negócio.
- **Use interfaces** para a Service Layer → facilita testes e trocas de implementação.
- **Camada Facade** para orquestrar múltiplos serviços.

---

## Resumo
A camada Service é essencial para organizar aplicações Spring Boot seguindo boas práticas, tornando a aplicação mais escalável, testável e manutenível.