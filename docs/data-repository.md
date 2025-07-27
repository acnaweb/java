# Spring Data Repository – Persistência Simplificada

---

## Problema
Como implementar **operações de persistência** (CRUD) em uma aplicação Spring Boot **sem escrever consultas SQL manualmente**?

---

## Solução
Use **Spring Data JPA**, que fornece a interface `JpaRepository` com operações prontas e suporte para **queries derivadas** baseadas em nomes de métodos.

---

### Exemplo de Repositório
```java
public interface ProdutoRepository extends JpaRepository<Produto, Long> {
    List<Produto> findByNomeContaining(String nome);
}
```

---

### Como funciona
- `ProdutoRepository` estende `JpaRepository<Produto, Long>`, fornecendo métodos prontos como:
  - `findAll()`, `findById(id)`, `save(entity)`, `deleteById(id)`
- Método customizado `findByNomeContaining(String nome)`:
  - Gera uma query equivalente a:
    ```sql
    SELECT * FROM produto WHERE nome LIKE %:nome%
    ```

---

## Boas práticas
✅ Sempre usar interfaces, não classes concretas  
✅ Nomear métodos seguindo convenções do **Spring Data** (`findBy`, `existsBy`, `countBy`)  
✅ Retornar `Optional<T>` para buscas únicas  
✅ Paginação com `Pageable`:
```java
Page<Produto> findByNomeContaining(String nome, Pageable pageable);
```

---

## Exemplo de Uso no Service
```java
@Service
public class ProdutoService {

    private final ProdutoRepository repository;

    public ProdutoService(ProdutoRepository repository) {
        this.repository = repository;
    }

    public List<Produto> buscarPorNome(String nome) {
        return repository.findByNomeContaining(nome);
    }
}
```

---

## Alternativas
- **Queries personalizadas com `@Query`**:
```java
@Query("SELECT p FROM Produto p WHERE p.nome LIKE %:nome%")
List<Produto> buscarPorNome(@Param("nome") String nome);
```

- **Native Query** (SQL puro):
```java
@Query(value = "SELECT * FROM produto WHERE nome LIKE %:nome%", nativeQuery = true)
List<Produto> buscarPorNomeNativo(@Param("nome") String nome);
```

---

## Recursos avançados
- **Paginação e Ordenação**
```java
Page<Produto> findByNomeContaining(String nome, Pageable pageable);
```

- **Projeções (DTO direto do repositório)**
```java
@Query("SELECT new com.exemplo.dto.ProdutoDto(p.id, p.nome) FROM Produto p")
List<ProdutoDto> buscarProdutosResumo();
```

---

## Referências
- [Spring Data JPA Docs](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/)