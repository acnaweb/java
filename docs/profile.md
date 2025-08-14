# Spring Framework – Profiles para Configuração por Ambiente

---

## Problema
Como gerenciar **diferentes configurações** para ambientes como **desenvolvimento, teste e produção** no Spring Boot sem alterar manualmente os arquivos a cada deploy?

---

## Solução
Use **Spring Profiles**, que permitem separar configurações por **ambiente** em arquivos dedicados ou anotações no código.

---

## Configuração com Profiles

### Estrutura de arquivos de configuração
- `application.properties` → Configuração padrão
- `application-dev.properties` → Ambiente de desenvolvimento
- `application-test.properties` → Ambiente de testes
- `application-prod.properties` → Ambiente de produção

---

### Exemplo: application-dev.properties
```properties
app.feature.enabled=true
spring.datasource.url=jdbc:mysql://localhost:3306/devdb
spring.datasource.username=${DB_USER:root}
spring.datasource.password=${DB_PASSWORD:root_pwd}
```

---

### Exemplo: application-test.properties
```properties
app.feature.enabled=true
spring.datasource.url=jdbc:mysql://localhost:3306/testdb
spring.datasource.username=${DB_USER:test_user}
spring.datasource.password=${DB_PASSWORD:test_pwd}
```

---

### Exemplo: application-prod.properties
```properties
app.feature.enabled=false
spring.datasource.url=jdbc:mysql://prod-server:3306/proddb
spring.datasource.username=${DB_USER}
spring.datasource.password=${DB_PASSWORD}
```

---

## Variáveis de ambiente
No **Linux/Mac**:
```bash
export DB_USER=prod_user
export DB_PASSWORD=secret123
```

No **Windows (PowerShell)**:
```powershell
$env:DB_USER="prod_user"
$env:DB_PASSWORD="secret123"
```

---

## Ativar um Profile

Via parâmetro Spring Boot (Maven):

```bash
mvn spring-boot:run -Dspring-boot.run.profiles=dev
```
Via parâmetro JVM:
```bash
java -jar <app.jar> -Dspring.profiles.active=dev
```

Ou no **application.properties**:
```properties
spring.profiles.active=dev
```

---

## Uso no Código com @Profile
```java
@Profile("dev")
@Service
public class DevEmailService implements EmailService {
    public void send(String msg) {
        System.out.println("DEV Email enviado: " + msg);
    }
}

@Profile("prod")
@Service
public class ProdEmailService implements EmailService {
    public void send(String msg) {
        // Envia email real
    }
}
```

---

## Verificando Profile Ativo
```java
@Autowired
private Environment env;

public void logProfile() {
    System.out.println("Profile ativo: " + Arrays.toString(env.getActiveProfiles()));
}
```

---

## Boas práticas
✅ Use Profiles para **configuração sensível ao ambiente**  
✅ Combine com **@Profile** para carregar beans específicos  
✅ Utilize **variáveis de ambiente** para credenciais e informações sensíveis  
✅ Em produção, considere **Spring Config Server** ou **Vault** para gestão segura  

---

## Referências
- [Spring Boot Profiles Documentation](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#features.profiles)
- [Spring Externalized Configuration](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#features.external-config)
