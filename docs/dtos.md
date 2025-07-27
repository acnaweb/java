# DTO Mapper – Conversão entre Entidades e DTOs no Java

---

## Problema
Como converter de forma simples e eficiente **entidades** para **DTOs** (e vice-versa) em aplicações Java, garantindo isolamento entre camadas sem repetir código desnecessário?

---

## Por que usar DTOs?
✅ Evita expor entidades diretamente na API  
✅ Reduz problemas de **Lazy Loading**  
✅ Permite **customizar dados de resposta**  
✅ Facilita **validação** e **segurança**  

---

## Solução
Utilize um **DTO Mapper**, que pode ser implementado **manualmente** ou com **frameworks** para automatizar o processo.

---

## 1. Conversão Manual
Implementação direta no código, boa para projetos simples.

### Exemplo com métodos tradicionais
```java
public ProdutoDto toDto(Produto model) {
    return new ProdutoDto(model.getId(), model.getNome(), model.getPreco());
}

public Produto toModel(ProdutoDto dto) {
    Produto p = new Produto();
    p.setId(dto.id());
    p.setNome(dto.nome());
    p.setPreco(dto.preco());
    return p;
}
```

---

## 2. Uso de `record` para DTO
Em vez de classes tradicionais, use **records** (Java 16+) para criar DTOs imutáveis.

### Exemplo
```java
public record ProdutoDto(Long id, String nome, Double preco) { }
```

---

## 3. Conversão com Framework – ModelMapper
Para grandes aplicações, use **ModelMapper**, que faz mapeamento automático.

### Dependência Maven
```xml
<dependency>
    <groupId>org.modelmapper</groupId>
    <artifactId>modelmapper</artifactId>
    <version>3.1.1</version>
</dependency>
```

### Configuração e uso
```java
import org.modelmapper.ModelMapper;

@Service
public class ProdutoMapper {
    private final ModelMapper modelMapper = new ModelMapper();

    public ProdutoDto toDto(Produto model) {
        return modelMapper.map(model, ProdutoDto.class);
    }

    public Produto toModel(ProdutoDto dto) {
        return modelMapper.map(dto, Produto.class);
    }
}
```

---

## Conversão de Listas com Streams
```java
public List<ProdutoDto> toDtoList(List<Produto> produtos) {
    return produtos.stream()
        .map(this::toDto)
        .toList(); // Java 16+
}
```

---

## Comparativo
| Abordagem        | Vantagens                     | Desvantagens                   |
|-------------------|--------------------------------|---------------------------------|
| **Manual**       | Controle total, simples      | Mais código repetitivo         |
| **ModelMapper**  | Automação, menos código      | Sobrecarga e menor performance |

---

## Boas práticas
✅ Prefira **manual** em sistemas pequenos  
✅ Use **ModelMapper** em projetos complexos ou com muitos DTOs  
✅ Para listas, combine com Streams e métodos utilitários  

---

🔗 [Documentação oficial do ModelMapper](https://modelmapper.org/)