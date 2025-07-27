# Configuração do Banco de Dados MySQL no Spring Boot

---

## Problema
Como configurar uma aplicação **Spring Boot** para se conectar a um banco de dados **MySQL**, garantindo criação automática do schema e integração com JPA/Hibernate?

---

## Solução
No arquivo `application.properties` (ou `application.yml`), configure as propriedades de conexão e comportamento do Hibernate:

---

### Configuração no `application.properties`
```properties
spring.datasource.url=jdbc:mysql://localhost:3306/api?createDatabaseIfNotExist=true
spring.datasource.username=root
spring.datasource.password=root_pwd

spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
spring.jpa.database-platform=org.hibernate.dialect.MySQLDialect
```

---

## Explicação das Propriedades
- **spring.datasource.url** → URL JDBC para o banco MySQL  
  - `createDatabaseIfNotExist=true` → cria o banco se não existir
- **spring.datasource.username** → usuário do banco
- **spring.datasource.password** → senha do banco
- **spring.jpa.hibernate.ddl-auto** → define como o Hibernate gerencia as tabelas:
  - `none` → não altera nada
  - `validate` → valida schema existente
  - `update` → atualiza conforme as entidades
  - `create` → cria tabelas a cada execução
  - `create-drop` → cria e apaga ao finalizar a app
- **spring.jpa.show-sql** → exibe SQL no console
- **spring.jpa.database-platform** → Dialeto do banco (MySQL neste caso)

---

## Boas Práticas
✅ Em produção, **evite `update` ou `create`**, use **migrações** com **Flyway** ou **Liquibase**.  
✅ Utilize **variáveis de ambiente** para credenciais (não hardcode).  
✅ Configure **pool de conexões** (HikariCP) para performance.  

---

## Exemplo usando `application.yml`
```yaml
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/api?createDatabaseIfNotExist=true
    username: root
    password: root_pwd
  jpa:
    hibernate:
      ddl-auto: update
    show-sql: true
    database-platform: org.hibernate.dialect.MySQLDialect
```

---

## Referências
- [Spring Boot Data Access](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#data)
- [Hibernate Dialects](https://docs.jboss.org/hibernate/orm/current/javadocs/org/hibernate/dialect/package-summary.html)