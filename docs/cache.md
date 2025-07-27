# Spring Boot – Cache com @Cacheable, @CacheEvict e @CachePut

---

## ✅ Problema
Como melhorar a performance de uma aplicação evitando reexecução de consultas pesadas ou cálculos custosos?

---

## ✅ Solução
O Spring Boot fornece **Spring Cache**, uma abstração que permite usar anotações como `@Cacheable`, `@CacheEvict` e `@CachePut`.  
Pode ser integrado com implementações como **Caffeine (memória local)**, **Redis (cache distribuído)**, **EhCache**, entre outros.

---

## ✅ Dependências

### Cache em memória local:
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-cache</artifactId>
</dependency>
```

### Redis:
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

---

## ✅ Habilitando o Cache
```java
@SpringBootApplication
@EnableCaching
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

---

## ✅ Uso das Anotações

### **1. @Cacheable** – Armazena no cache
```java
@Service
public class ProdutoService {

    @Cacheable(value = "produtos")
    public Produto buscar(Long id) {
        simulateSlowService();
        return new Produto(id, "Produto " + id);
    }

    private void simulateSlowService() {
        try { Thread.sleep(2000); } catch (InterruptedException e) { }
    }
}
```
✔ Primeira chamada → executa e armazena no cache  
✔ Próximas chamadas → busca diretamente do cache

---

### **2. Personalizando chave**
```java
@Cacheable(value = "produtos", key = "#id")
public Produto buscar(Long id) { ... }
```

---

### **3. Evitando cache em certas condições**
```java
@Cacheable(value = "produtos", unless = "#result == null")
public Produto buscar(Long id) { ... }
```

---

### **4. @CacheEvict** – Remove do cache
```java
@CacheEvict(value = "produtos", key = "#id")
public void remover(Long id) { ... }
```

Para remover todos os registros do cache:
```java
@CacheEvict(value = "produtos", allEntries = true)
public void limparCache() { ... }
```

---

### **5. @CachePut** – Atualiza cache junto com a execução do método
```java
@CachePut(value = "produtos", key = "#produto.id")
public Produto atualizar(Produto produto) {
    return repository.save(produto);
}
```

---

## ✅ Configuração para memória local (Caffeine)
`application.properties`:
```properties
spring.cache.type=caffeine
spring.cache.caffeine.spec=maximumSize=100,expireAfterWrite=10m
```

---

## ✅ Configuração para Redis
`application.properties`:
```properties
spring.cache.type=redis
spring.data.redis.host=localhost
spring.data.redis.port=6379
spring.cache.redis.time-to-live=600000 # 10 minutos
```

---

## ✅ Exemplo com Redis e @Cacheable
```java
@Cacheable(value = "produtos", key = "#id")
public Produto buscar(Long id) {
    return produtoRepository.findById(id).orElseThrow();
}
```

---

## ✅ Boas práticas
✔ Use cache para operações custosas e dados pouco mutáveis  
✔ Defina **TTL (Time to Live)** para evitar dados obsoletos  
✔ Em ambientes distribuídos → prefira **Redis**  
✔ Monitore métricas de cache (hits e misses)  

---

## Referências
- [Spring Cache Docs](https://docs.spring.io/spring-framework/docs/current/reference/html/integration.html#cache)
- [Redis Spring Boot Integration](https://spring.io/projects/spring-data-redis)