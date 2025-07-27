# Spring Data JPA – Paginação e Ordenação

---

## Problema
Como implementar **paginação** e **ordenação** em uma API utilizando **Spring Data JPA** sem precisar escrever consultas complexas?

---

## Solução
O Spring Data fornece a interface **`Pageable`**, que pode ser usada em repositórios para retornar resultados paginados e ordenados automaticamente.

---

## Exemplo básico de uso
```java
@GetMapping
public Page<Produto> listar(Pageable pageable) {
    return repository.findAll(pageable);
}
```

---

## Como funciona
- O Spring Data JPA converte automaticamente parâmetros da URL em um objeto `Pageable`.
- Retorna um objeto `Page<T>` com:
  - `getContent()` → lista dos itens
  - `getTotalElements()` → total de registros
  - `getTotalPages()` → total de páginas
  - `getNumber()` → página atual
  - `getSize()` → tamanho da página

---

### Exemplo de chamada HTTP
```
GET /produtos?page=0&size=10&sort=nome,asc
```
- `page=0` → primeira página  
- `size=10` → 10 registros por página  
- `sort=nome,asc` → ordena por nome crescente  

---

## Repositório
```java
public interface ProdutoRepository extends JpaRepository<Produto, Long> {
    Page<Produto> findByNomeContaining(String nome, Pageable pageable);
}
```

---

## Customizando Pageable
```java
@GetMapping("/buscar")
public Page<Produto> buscar(@RequestParam String nome, Pageable pageable) {
    return repository.findByNomeContaining(nome, pageable);
}
```

---

## Paginação com DTO
```java
public Page<ProdutoDto> listar(Pageable pageable) {
    return repository.findAll(pageable)
                     .map(this::toDto);
}
```

---

## Boas práticas
✅ Use **paginação sempre que houver grandes volumes de dados**  
✅ Prefira **Page<T>** ou **Slice<T>** em vez de listas simples  
✅ Combine com **Projections ou DTOs** para respostas otimizadas  
✅ Sempre valide valores de `page` e `size` (para evitar consultas pesadas)  

---

## Exemplo completo com ordenação padrão
```java
@GetMapping
public Page<Produto> listar(
    @PageableDefault(size = 10, sort = "nome", direction = Sort.Direction.ASC) Pageable pageable) {
    return repository.findAll(pageable);
}
```

---

## Referências
- [Spring Data JPA Docs](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#repositories.paging-and-sorting)
- [Baeldung – Pagination with Spring Data JPA](https://www.baeldung.com/spring-data-jpa-pagination-sorting)