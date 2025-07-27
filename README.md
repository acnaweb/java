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

## DTO Mapper

- dtos.md

## Spring Data Relationships

- data-relationships.md

## Exception Handler

- exception-handler.md

## Validation
Validação de entrada:

```java
public record ProdutoDto(
    @NotBlank(message = "Nome é obrigatório")
    String nome
) { }
```

## Spring Data Projections

- data-projections.md

## Pagination & Sort

- data-pagination

## Spring Framework Profiles
Configuração por ambiente:

```properties
# application-dev.properties
app.feature.enabled=true
```

Execução:
```bash
-Dspring.profiles.active=dev
```

## Spring Environment Variable

Uso de variáveis externas:

```properties
app.url=${APP_URL:http://localhost}
```

## Docker
Empacotando a aplicação:

```dockerfile
FROM openjdk:17
COPY target/api.jar app.jar
ENTRYPOINT ["java", "-jar", "/app.jar"]
```

## Docker Composer
Gerenciando múltiplos containers:

```yaml
version: '3'
services:
  db:
    image: mysql
    environment:
      MYSQL_ROOT_PASSWORD: root_pwd
      MYSQL_DATABASE: api
```

## Deploy Manual to Cloud
Opções:
- Google Cloud Run
- AWS Elastic Beanstalk
- Azure App Service

## Lombok
Simplifica getters, setters, construtores.

```java
@Data
@NoArgsConstructor
@AllArgsConstructor
@Entity
public class Produto { ... }
```

🔗 https://projectlombok.org/


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
Exemplo com `httpBasic()`:

```java
@Override
protected void configure(HttpSecurity http) throws Exception {
    http.authorizeRequests().anyRequest().authenticated().and().httpBasic();
}
```

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