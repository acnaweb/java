# Integração via Webhook no Spring Boot

---

## ✅ Problema
Como enviar **eventos da aplicação para serviços externos** via HTTP de forma simples e confiável no **Spring Boot**?

---

## ✅ Solução
Use o **RestTemplate** (ou **WebClient** para projetos reativos) para enviar **requisições HTTP POST** para uma URL de Webhook.

---

## ✅ Dependência (caso não tenha)
O Spring Boot Starter Web já inclui o `RestTemplate`.  
Para **WebClient**, é necessário o Spring WebFlux:
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-webflux</artifactId>
</dependency>
```

---

## ✅ Exemplo com `RestTemplate`
```java
@Service
public class WebhookService {

    private final RestTemplate restTemplate = new RestTemplate();

    public void enviarEvento(Object payload) {
        String url = "https://webhook.site/seu-endpoint";
        restTemplate.postForEntity(url, payload, Void.class);
    }
}
```

---

### Payload Exemplo
```java
public record Evento(String tipo, String mensagem) {}
```

Chamando o serviço:
```java
Evento evento = new Evento("CREATE", "Novo produto adicionado");
webhookService.enviarEvento(evento);
```

---

## ✅ Exemplo com Headers (JSON)
```java
HttpHeaders headers = new HttpHeaders();
headers.setContentType(MediaType.APPLICATION_JSON);

HttpEntity<Evento> request = new HttpEntity<>(evento, headers);
restTemplate.postForEntity("https://webhook.site/seu-endpoint", request, Void.class);
```

---

## ✅ Alternativa com `WebClient` (Reativo)
```java
WebClient webClient = WebClient.builder()
    .baseUrl("https://webhook.site")
    .build();

webClient.post()
    .uri("/seu-endpoint")
    .contentType(MediaType.APPLICATION_JSON)
    .bodyValue(evento)
    .retrieve()
    .bodyToMono(Void.class)
    .block();
```

---

## ✅ Timeout no RestTemplate
Evite bloqueios configurando timeout:
```java
HttpComponentsClientHttpRequestFactory factory = new HttpComponentsClientHttpRequestFactory();
factory.setConnectTimeout(3000);
factory.setReadTimeout(5000);
RestTemplate restTemplate = new RestTemplate(factory);
```

---

## ✅ Melhores práticas
✔ **Timeouts** para evitar travamentos  
✔ **Retries e fallback** (ex.: Resilience4j)  
✔ **Execução assíncrona** para não impactar requisições  
✔ **Log de falhas** para auditoria  
✔ **Filas (RabbitMQ/Kafka)** para garantir entrega  

---

## ✅ Segurança
- Utilize HTTPS para comunicação segura  
- Assine o payload (HMAC) para validar integridade  
- Valide o endpoint antes de enviar dados sensíveis  

---

## Referências
- [Spring RestTemplate Docs](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/client/RestTemplate.html)
- [Spring WebClient Docs](https://docs.spring.io/spring-framework/docs/current/reference/html/web-reactive.html#webflux-client)