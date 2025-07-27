# Spring Data JPA – Relacionamentos entre Entidades

---

## Problema
Como mapear **relacionamentos entre tabelas** no **Spring Data JPA** para representar vínculos entre entidades como `Pedido` e `Item`?

---

## Solução
Use as anotações do **JPA** (`@OneToMany`, `@ManyToOne`, `@JoinColumn`, etc.) para criar associações entre classes que serão refletidas no banco de dados.

---

## Exemplo prático: Pedido e Item

### Entidade Pedido
```java
@Entity
public class Pedido {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String cliente;

    @OneToMany(mappedBy = "pedido", cascade = CascadeType.ALL, orphanRemoval = true)
    private List<Item> items = new ArrayList<>();

    // getters e setters
}
```

### Entidade Item
```java
@Entity
public class Item {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String produto;
    private Integer quantidade;
    private Double preco;

    @ManyToOne
    @JoinColumn(name = "pedido_id")
    private Pedido pedido;

    // getters e setters
}
```

---

## Como funciona
- `@OneToMany` → Relacionamento **um para muitos** (um Pedido tem vários Itens).
  - `mappedBy` → nome do atributo na outra entidade que mapeia este relacionamento.
  - `cascade = CascadeType.ALL` → operações no Pedido se propagam para os Itens.
  - `orphanRemoval = true` → remove itens órfãos automaticamente.
- `@ManyToOne` → Relacionamento **muitos para um** (muitos Itens pertencem a um Pedido).
- `@JoinColumn(name = "pedido_id")` → define a coluna FK na tabela `Item`.

---

## Outros tipos de relacionamento

### One-to-One
```java
@Entity
public class Cliente {
    @OneToOne
    @JoinColumn(name = "endereco_id")
    private Endereco endereco;
}
```

---

### Many-to-Many
```java
@Entity
public class Aluno {
    @ManyToMany
    @JoinTable(
        name = "aluno_curso",
        joinColumns = @JoinColumn(name = "aluno_id"),
        inverseJoinColumns = @JoinColumn(name = "curso_id")
    )
    private Set<Curso> cursos = new HashSet<>();
}

@Entity
public class Curso {
    @ManyToMany(mappedBy = "cursos")
    private Set<Aluno> alunos = new HashSet<>();
}
```

---

## Cardinalidades mais comuns no JPA
| Anotação        | Significado        |
|-----------------|--------------------|
| `@OneToOne`    | Um para um        |
| `@OneToMany`   | Um para muitos    |
| `@ManyToOne`   | Muitos para um    |
| `@ManyToMany`  | Muitos para muitos|

---

## Boas práticas
✅ Sempre usar `mappedBy` para evitar tabelas desnecessárias em relacionamentos bidirecionais  
✅ Utilize `cascade` com cuidado (pode excluir registros não desejados)  
✅ Para listas, inicialize como `new ArrayList<>()` para evitar `NullPointerException`  
✅ Considere **DTOs** para evitar problemas com **Lazy Loading**  
✅ Prefira `FetchType.LAZY` para coleções, evitando carregar dados desnecessários  

---

## Referências
- [Documentação oficial JPA](https://jakarta.ee/specifications/persistence/)
- [Spring Data JPA Reference](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/)