# Testes Unitários com Mockito (Given-When-Then)

---

## ✅ Problema
Como testar **algoritmos e lógica de negócio em services** no Spring Boot, garantindo confiabilidade e isolamento das dependências externas?

---

## ✅ Solução
- Use **JUnit 5** + **Mockito**.
- Aplique o padrão **Given-When-Then** para melhorar a legibilidade:
  - **Given**: Contexto ou pré-condições (mock, dados iniciais).
  - **When**: Ação a ser testada.
  - **Then**: Validação do resultado e interações.

---

## Dependências
Já incluídas no `spring-boot-starter-test`:
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
</dependency>
```

---

## ✅ Exemplo 1 – Algoritmo simples (sem mocks)

### Service
```java
@Service
public class CalculadoraService {
    public int somar(int a, int b) {
        return a + b;
    }

    public int fatorial(int n) {
        if (n < 0) throw new IllegalArgumentException("Número negativo");
        return n <= 1 ? 1 : n * fatorial(n - 1);
    }
}
```

### Teste com Given-When-Then
```java
@ExtendWith(MockitoExtension.class)
class CalculadoraServiceTest {

    @InjectMocks
    private CalculadoraService calculadoraService;

    @Test
    void deveSomarDoisNumeros() {
        // given
        int a = 5;
        int b = 3;

        // when
        int resultado = calculadoraService.somar(a, b);

        // then
        assertEquals(8, resultado);
    }

    @Test
    void deveCalcularFatorial() {
        // given
        int numero = 5;

        // when
        int resultado = calculadoraService.fatorial(numero);

        // then
        assertEquals(120, resultado);
    }

    @Test
    void deveLancarExcecaoParaNumeroNegativo() {
        // given
        int numero = -1;

        // when & then
        assertThrows(IllegalArgumentException.class, () -> calculadoraService.fatorial(numero));
    }
}
```

---

## ✅ Exemplo 2 – Service com dependência (mockado)

### Service
```java
@Service
public class PedidoService {
    private final ProdutoRepository produtoRepository;

    public PedidoService(ProdutoRepository produtoRepository) {
        this.produtoRepository = produtoRepository;
    }

    public double calcularTotal(Long produtoId, int quantidade) {
        Produto produto = produtoRepository.findById(produtoId)
            .orElseThrow(() -> new RuntimeException("Produto não encontrado"));
        return produto.getPreco() * quantidade;
    }
}
```

### Teste com Mockito (Given-When-Then)
```java
@ExtendWith(MockitoExtension.class)
class PedidoServiceTest {

    @Mock
    private ProdutoRepository produtoRepository;

    @InjectMocks
    private PedidoService pedidoService;

    @Test
    void deveCalcularTotalCorretamente() {
        // given
        Produto produto = new Produto(1L, "Produto X", 10.0);
        given(produtoRepository.findById(1L)).willReturn(Optional.of(produto));

        // when
        double total = pedidoService.calcularTotal(1L, 3);

        // then
        assertEquals(30.0, total);
        then(produtoRepository).should(times(1)).findById(1L);
    }

    @Test
    void deveLancarExcecaoQuandoProdutoNaoEncontrado() {
        // given
        given(produtoRepository.findById(99L)).willReturn(Optional.empty());

        // when & then
        assertThrows(RuntimeException.class, () -> pedidoService.calcularTotal(99L, 2));
        then(produtoRepository).should(times(1)).findById(99L);
    }
}
```

---

## ✅ Boas práticas
✔ Use **given-when-then** para clareza.  
✔ Verifique interações com `then(mock).should(times(n))`.  
✔ Teste **cenários positivos e negativos**.  
✔ Não carregue o contexto Spring (`@SpringBootTest`) para unit tests → use `MockitoExtension`.  
✔ Nomeie testes de forma descritiva (`deveCalcularTotalCorretamente`).  

---

## Referências
- [Mockito Docs](https://site.mockito.org/)
- [JUnit 5 User Guide](https://junit.org/junit5/docs/current/user-guide/)