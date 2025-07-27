# Integração com Apache Kafka no Spring Boot

---

## ✅ Problema
Como enviar e consumir mensagens do **Apache Kafka** no **Spring Boot**, garantindo escalabilidade e tolerância a falhas?

---

## ✅ Solução
Use **Spring for Apache Kafka**, que oferece suporte para produtores e consumidores com configuração simples e suporte a serialização JSON.

---

## ✅ Dependência
Adicione no `pom.xml`:
```xml
<dependency>
    <groupId>org.springframework.kafka</groupId>
    <artifactId>spring-kafka</artifactId>
</dependency>
```

---

## ✅ Configuração no `application.properties`
```properties
spring.kafka.bootstrap-servers=localhost:9092

# Producer
spring.kafka.producer.key-serializer=org.apache.kafka.common.serialization.StringSerializer
spring.kafka.producer.value-serializer=org.apache.kafka.common.serialization.StringSerializer

# Consumer
spring.kafka.consumer.group-id=grupo-teste
spring.kafka.consumer.auto-offset-reset=earliest
spring.kafka.consumer.key-serializer=org.apache.kafka.common.serialization.StringDeserializer
spring.kafka.consumer.value-serializer=org.apache.kafka.common.serialization.StringDeserializer
```

---

## ✅ Produção de Mensagens (String)
```java
@Service
public class KafkaProducerService {

    @Autowired
    private KafkaTemplate<String, String> kafkaTemplate;

    public void enviarMensagem(String topico, String mensagem) {
        kafkaTemplate.send(topico, mensagem);
    }
}
```

Uso:
```java
kafkaProducerService.enviarMensagem("meu-topico", "Olá, Kafka!");
```

---

## ✅ Produção Assíncrona com Callback
```java
kafkaTemplate.send("meu-topico", "mensagem")
    .addCallback(
        result -> System.out.println("Mensagem enviada com sucesso!"),
        ex -> System.err.println("Erro ao enviar mensagem: " + ex.getMessage())
    );
```

---

## ✅ Consumo de Mensagens
```java
@Service
public class KafkaConsumerService {

    @KafkaListener(topics = "meu-topico", groupId = "grupo-teste")
    public void consumirMensagem(String mensagem) {
        System.out.println("Mensagem recebida: " + mensagem);
    }
}
```

---

## ✅ Envio de Objetos (JSON)
Configuração:
```properties
spring.kafka.producer.value-serializer=org.springframework.kafka.support.serializer.JsonSerializer
spring.kafka.consumer.value-deserializer=org.springframework.kafka.support.serializer.JsonDeserializer
spring.kafka.consumer.properties.spring.json.trusted.packages=*
```

Classe:
```java
public record Evento(String tipo, String descricao) {}
```

Envio:
```java
kafkaTemplate.send("eventos", new Evento("CREATE", "Novo produto"));
```

Consumo:
```java
@KafkaListener(topics = "eventos", groupId = "grupo-teste")
public void consumirEvento(Evento evento) {
    System.out.println("Evento recebido: " + evento.tipo());
}
```

---

## ✅ Configuração Avançada
- **Retries e Timeout**
```properties
spring.kafka.producer.retries=3
spring.kafka.producer.acks=all
spring.kafka.producer.properties.linger.ms=5
```

- **Dead Letter Topics (DLT)** para mensagens com falha:
```java
@KafkaListener(topics = "eventos", groupId = "grupo-teste")
public void consumirComDLT(Evento evento) {
    throw new RuntimeException("Erro simulado");
}
```

Habilitar DLT no container factory.

---

## ✅ Boas práticas
✔ Utilize **chaves** para particionamento consistente  
✔ Configure **retry**, **timeout** e **acks** para confiabilidade  
✔ Para grandes volumes, use **compressão** (gzip, snappy)  
✔ Implemente **DLT** para mensagens com falha  
✔ Monitore **offsets** e **lag** no Kafka  

---

## Referências
- [Spring Kafka Docs](https://spring.io/projects/spring-kafka)
- [Apache Kafka Docs](https://kafka.apache.org/documentation/)