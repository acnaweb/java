# Java Optional – Benefícios e Como Usar

---

## Problema
Como lidar com valores **nulos** em Java sem causar `NullPointerException` e escrever código mais legível, seguro e funcional?

---

## Solução
Use a classe `Optional<T>` (desde **Java 8**), que representa um **valor que pode ou não estar presente**, evitando verificações manuais com `null`.

---

## Benefícios do Optional
✅ **Evita `NullPointerException`** → Força tratamento explícito de ausência de valor  
✅ **Código mais legível** → Elimina `if (obj != null)` espalhados  
✅ **Melhor expressividade semântica** → Indica claramente que o valor pode ser opcional  
✅ **Facilita programação funcional** → Uso com `map`, `filter`, `flatMap`  
✅ **Encapsula lógica de valor ausente** → Centraliza a decisão de fallback  
✅ **Melhora a testabilidade** → Evita branchs excessivas para nulos  

---

## Exemplos de Uso

### 1. Criação de Optional
```java
Optional<String> nome = Optional.of("João"); // valor não nulo
Optional<String> vazio = Optional.empty(); // sem valor
Optional<String> seguro = Optional.ofNullable(possivelNulo); // aceita nulo
```

---

### 2. Acessando valores
```java
String valor = nome.orElse("Valor padrão");
String valor2 = nome.orElseGet(() -> "Gerado dinamicamente");
```

---

### 3. Verificação e ações
```java
if (nome.isPresent()) {
    System.out.println("Nome: " + nome.get());
}

// Forma funcional
nome.ifPresent(System.out::println);
```

---

### 4. Transformação com map()
```java
Optional<String> nome = Optional.of("João");
Optional<Integer> tamanho = nome.map(String::length); // Optional<5>
```

---

### 5. Filtrando valores
```java
Optional<String> nome = Optional.of("Maria");
nome.filter(n -> n.startsWith("M"))
    .ifPresent(System.out::println);
```

---

## Quando usar Optional
✅ Retornos de métodos onde o valor pode não existir (ex.: buscas em repositórios)  
✅ Melhorar clareza sobre valores opcionais  
❌ Evitar em atributos de entidades JPA  
❌ Evitar em parâmetros obrigatórios  

---

## Exemplo com Spring Data
```java
public Optional<Produto> findById(Long id) {
    return repository.findById(id);
}

Produto produto = repository.findById(id)
    .orElseThrow(() -> new RuntimeException("Produto não encontrado"));
```

---

## Métodos mais usados do Optional

| Método            | Descrição                                                   | Exemplo                                   |
|-------------------|------------------------------------------------------------|-------------------------------------------|
| `orElse(T)`       | Retorna valor ou padrão se vazio                          | `nome.orElse("Padrão")`                 |
| `orElseGet(Supplier)` | Retorna valor ou gera dinamicamente se vazio          | `nome.orElseGet(() -> "Gerado")`        |
| `map(Function)`   | Aplica função ao valor se presente                        | `nome.map(String::length)`              |
| `flatMap(Function)` | Similar ao map, mas evita Optional aninhado             | `nome.flatMap(this::buscarOutro)`       |
| `filter(Predicate)` | Retorna Optional vazio se não corresponder à condição   | `nome.filter(n -> n.startsWith("A"))`   |
| `ifPresent(Consumer)` | Executa ação se valor presente                        | `nome.ifPresent(System.out::println)`   |
| `orElseThrow()`   | Lança exceção se vazio                                    | `nome.orElseThrow(() -> new Exception())`|

---

## Referências
- [Documentação oficial do Optional](https://docs.oracle.com/javase/8/docs/api/java/util/Optional.html)