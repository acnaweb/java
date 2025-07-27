# Lombok – Simplificação de Código no Java

---

## Problema
Como evitar código repetitivo em classes Java, como **getters, setters, construtores, equals, hashCode e toString**, mantendo a legibilidade e produtividade?

---

## Solução
Use **Project Lombok**, uma biblioteca que gera automaticamente métodos comuns em tempo de compilação por meio de anotações.

---

## Dependência Maven
```xml
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <version>1.18.30</version>
    <scope>provided</scope>
</dependency>
```

> **Dica:** Em IDEs como IntelliJ e Eclipse, instale o plugin do Lombok para suporte completo.

---

## Exemplo básico com Lombok
```java
@Data
@NoArgsConstructor
@AllArgsConstructor
@Entity
public class Produto {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String nome;
    private Double preco;
}
```

### O que as anotações fazem
- `@Data` → Gera automaticamente:
  - Getters e Setters
  - `equals()` e `hashCode()`
  - `toString()`
- `@NoArgsConstructor` → Construtor sem argumentos
- `@AllArgsConstructor` → Construtor com todos os argumentos

---

## Outras anotações úteis do Lombok
| Anotação        | Função                                  |
|-----------------|-----------------------------------------|
| `@Getter`       | Gera apenas getters                   |
| `@Setter`       | Gera apenas setters                   |
| `@Builder`      | Implementa o padrão Builder           |
| `@Value`        | Cria classe imutável (equivalente a `final` + getters) |
| `@Slf4j`        | Cria um logger (log com SLF4J)        |

---

## Exemplo avançado com Builder
```java
@Builder
public class ProdutoDto {
    private Long id;
    private String nome;
    private Double preco;
}
```

### Uso:
```java
ProdutoDto dto = ProdutoDto.builder()
    .id(1L)
    .nome("Produto Teste")
    .preco(99.90)
    .build();
```

---

## Exemplo com Logger usando @Slf4j
```java
@Slf4j
@Service
public class ProdutoService {

    public void salvar(Produto produto) {
        log.info("Salvando produto: {}", produto.getNome());
    }
}
```

---

## Exemplo com Imutabilidade usando @Value
```java
@Value
public class ClienteDto {
    Long id;
    String nome;
    String email;
}
```

- `@Value` torna a classe imutável, equivalente a:
  - `final` em todos os campos
  - Getters gerados automaticamente
  - Construtor obrigatório
  - `toString()`, `equals()`, `hashCode()`

---

## Boas práticas
✅ Use Lombok para **diminuir boilerplate**  
✅ Combine com **DTOs** para respostas de API  
✅ Evite `@Data` em entidades JPA **com relacionamentos complexos** → use `@Getter` e `@Setter` seletivamente para evitar problemas de serialização  
✅ Use `@Value` para objetos imutáveis (DTOs, responses)  

---

## Referências
- [Project Lombok Site Oficial](https://projectlombok.org/)
- [Documentação Lombok GitHub](https://github.com/projectlombok/lombok)