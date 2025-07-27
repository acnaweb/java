# Java

📘 Guia oficial: https://spring.io/guides/tutorials/rest

- **[Spring Initializr](docs/spring-initializr.md)**  

- **[Controller](docs/api-controller.md)** 

- **[Endpoints (rotas)](docs/endpoints.md)** 


## OpenAPI

- openapi.md

## Http Status Code

- http-status-code.md

## Service

- service.md

## Database

- database.md

## Spring Data Repository

- repository.md

## Optional

- optional.md

## List<?> Stream

- streams.md

## Lombok

- lombok.md

## DTO Mapper

- dtos.md

## Spring Data Relationships

- data-relationships.md

## Exception Handler

- exception-handler.md

## Validation

- validations

## Spring Data Projections

- data-projections.md

## Pagination & Sort

- data-pagination.md

## Spring Framework Profiles

- profiles.md

## Docker (Dockerfile)

- docker.md

## Docker Compose

- docker-compose.md

## Deploy Manual to Cloud

Opções:
- Google Cloud Run
- AWS Elastic Beanstalk
- Azure App Service


## Unit Tests
Teste com MockMvc:

```java
@SpringBootTest
@AutoConfigureMockMvc
class ProdutoControllerTest {
    @Autowired MockMvc mockMvc;

    @Test
    void testPing() throws Exception {
        mockMvc.perform(get("/ping")).andExpect(status().isOk());
    }
}
```

## Spring List Of Values
Uso de herança em entidades:

```java
@Entity
@Inheritance(strategy = InheritanceType.JOINED)
public abstract class Pessoa { ... }
```

## Security

- security.md

## API Versioning

Controle de versão por rota:

```java
@RequestMapping("/api/v1/produtos")
```

## Code Quality - Checkstyle
Ferramenta de análise estática de código.

🔗 https://checkstyle.sourceforge.io/

## Continuous Integration / Delivery / Deployment
Ferramentas:
- GitHub Actions
- Jenkins
- GitLab CI/CD

## Feature Flag

- https://martinfowler.com/bliki/FeatureFlag.html

```properties
feature.produtos.enabled=true
feature.clientes.enabled=false
```

```FeatureFlagInterceptor.java
@Component
public class FeatureFlagInterceptor implements HandlerInterceptor {

    @Value("${feature.clientes.enabled:false}")
    private boolean clientesEnabled;

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler)
        throws Exception {

        if (request.getRequestURI().startsWith("/clientes") && !clientesEnabled) {
            response.setStatus(HttpStatus.NOT_IMPLEMENTED.value());
            response.getWriter().write("Endpoint /clientes está desabilitado por feature flag.");
            return false;
        }
        return true;
    }
}
```

## Cache
Anotação `@Cacheable`:

```java
@Cacheable("produtos")
public Produto buscar(Long id) { ... }
```

## Migrations
Usando Flyway:

```sql
-- V1__create_produto.sql
CREATE TABLE produto (...);
```

🔗 https://www.baeldung.com/database-migrations-with-flyway

## Integration WebWook
Envio de eventos:

```java
restTemplate.postForEntity("https://webhook.site", payload, Void.class);
```

## Integration Kafka
Produção de mensagens:

```java
@Autowired
private KafkaTemplate<String, String> kafka;

kafka.send("topico", "mensagem");
```

## Api Gateway
Utilize:
- Spring Cloud Gateway
- Kong
- NGINX

Configuração de roteamento e segurança de entrada.