# Manipulação de List com Java Streams

---

## Problema
Como **filtrar**, **transformar** e **coletar** dados de uma lista em Java de forma simples e funcional sem loops explícitos?

---

## Solução
Use **Java Streams** para aplicar **operações encadeadas** sobre coleções como `List`, permitindo código mais legível e expressivo.

---

## Exemplo prático
```java
List<ProdutoDto> ativos = produtos.stream()
    .filter(Produto::isAtivo) // mantém apenas ativos
    .map(this::toDto)        // converte para DTO
    .collect(Collectors.toList()); // retorna como List
```

---

### Como funciona
- `stream()` → cria um fluxo a partir da lista original.
- `filter(...)` → mantém apenas os elementos que atendem a uma condição.
- `map(...)` → transforma cada elemento (ex.: converter entidade em DTO).
- `collect(...)` → coleta o resultado em uma coleção (`List`, `Set`) ou outro tipo de resultado.

---

## Operações mais comuns com Streams

| Operação       | Descrição                                  | Exemplo                                      |
|---------------|--------------------------------------------|---------------------------------------------|
| `filter`      | Filtra elementos com base em condição     | `.filter(p -> p.getPreco() > 100)`         |
| `map`         | Transforma cada elemento                  | `.map(Produto::getNome)`                   |
| `sorted`      | Ordena a lista                            | `.sorted(Comparator.comparing(Produto::getPreco))` |
| `limit`       | Limita a quantidade de elementos          | `.limit(5)`                                 |
| `skip`        | Ignora os primeiros N elementos           | `.skip(2)`                                  |
| `distinct`    | Remove duplicados                         | `.distinct()`                               |
| `forEach`     | Executa ação para cada elemento           | `.forEach(System.out::println)`            |
| `collect`     | Converte o Stream em uma coleção          | `.collect(Collectors.toList())`            |

---

## Outros exemplos úteis

### Filtrar e ordenar
```java
List<Produto> caros = produtos.stream()
    .filter(p -> p.getPreco() > 500)
    .sorted(Comparator.comparing(Produto::getPreco))
    .collect(Collectors.toList());
```

### Obter apenas nomes
```java
List<String> nomes = produtos.stream()
    .map(Produto::getNome)
    .collect(Collectors.toList());
```

### Somar valores
```java
double total = produtos.stream()
    .mapToDouble(Produto::getPreco)
    .sum();
```

---

## Boas práticas para uso de Streams
✅ **Prefira Streams para processamento em lote**, mantendo código mais limpo.  
✅ **Evite streams em loops críticos de performance**, use métodos tradicionais se necessário.  
✅ **Não reutilize Streams** → uma vez consumido, não pode ser reaberto.  
✅ **Considere `parallelStream()` com cautela** → só compensa em grandes coleções e operações CPU-bound.  
✅ **Combine com Optional** para lidar com valores ausentes.  

---

## Referências
- [Documentação oficial Java Streams](https://docs.oracle.com/javase/8/docs/api/java/util/stream/package-summary.html)
- [Collectors Utility Class](https://docs.oracle.com/javase/8/docs/api/java/util/stream/Collectors.html)