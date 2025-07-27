# Spring Data JPA – Projections (Projeções de Dados)

---

## Problema
Como retornar apenas alguns **campos específicos** de uma entidade no **Spring Data JPA** sem precisar carregar todos os dados ou criar DTOs manualmente?

---

## Solução
Use **Projections** do Spring Data JPA, que permitem definir interfaces ou classes para retornar subconjuntos de atributos, melhorando a performance e evitando dados desnecessários.

---

## Benefícios das Projections
✅ **Evita sobrecarga** carregando apenas os campos necessários  
✅ **Não precisa escrever queries SQL**  
✅ **Pode usar interfaces ou classes** para mapear os dados  
✅ **Compatível com métodos de repositório e queries nativas**  

---

## Exemplo básico com Interface Projection
```java
public interface ProdutoView {
    String getNome();
    BigDecimal getValor();
}
```

---

### Repositório usando Projection
```java
public interface ProdutoRepository extends JpaRepository<Produto, Long> {
    List<ProdutoView> findByValorGreaterThan(BigDecimal valor);
}
```

Quando chamar:
```java
List<ProdutoView> produtos = produtoRepository.findByValorGreaterThan(new BigDecimal("100.00"));
for (ProdutoView p : produtos) {
    System.out.println(p.getNome() + " - " + p.getValor());
}
```
✅ Retorna apenas **nome** e **valor** sem carregar todos os atributos da entidade.

---

## Tipos de Projections no Spring Data
| Tipo           | Exemplo                                                    |
|---------------|-----------------------------------------------------------|
| **Interface** | `public interface ProdutoView { String getNome(); }`      |
| **DTO (Classe)** | `public record ProdutoDto(String nome, BigDecimal valor)` |
| **Dynamic**   | Retorna diferentes projeções dinamicamente via parâmetro  |

---

### Projeção com Classe (DTO Projection)
```java
public record ProdutoDto(String nome, BigDecimal valor) {}

@Query("SELECT new com.exemplo.dto.ProdutoDto(p.nome, p.valor) FROM Produto p")
List<ProdutoDto> buscarProdutos();
```

---

## Projeção Aninhada (Nested Projection)
Também é possível projetar **relacionamentos**:
```java
public interface PedidoView {
    String getCliente();
    List<ItemView> getItems();

    interface ItemView {
        String getProduto();
        Integer getQuantidade();
    }
}
```

---

## Boas práticas
✅ Use projections quando precisa de **dados parciais** para performance  
✅ Prefira **DTO Projections** para APIs externas (mais controle)  
✅ Para consultas complexas, use `@Query` com **JPQL** ou **Native Query**  

---

## Referências
- [Baeldung – Spring Data JPA Projections](https://www.baeldung.com/spring-data-jpa-projections)
- [Spring Data JPA Docs](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/)