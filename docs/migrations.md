# Migrations no Spring Boot com Flyway

---

## ✅ Problema
Como gerenciar **migrações de banco de dados** em um projeto **Spring Boot**, garantindo consistência entre ambientes (desenvolvimento, teste e produção)?

---

## ✅ Solução
Use **Flyway**, uma ferramenta que aplica scripts SQL de forma incremental, garantindo versionamento do banco de dados.

---

## ✅ Dependência
Adicione ao `pom.xml`:
```xml
<dependency>
    <groupId>org.flywaydb</groupId>
    <artifactId>flyway-core</artifactId>
</dependency>
```

---

## ✅ Estrutura e Convenção
- Scripts ficam na pasta:
```
src/main/resources/db/migration
```

- Nome dos arquivos:
```
V<versão>__<descricao>.sql
```

Exemplo:
```
V1__create_produto.sql
V2__add_column_status.sql
```

---

## ✅ Exemplo de Migration Inicial
Arquivo: `V1__create_produto.sql`
```sql
CREATE TABLE produto (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    nome VARCHAR(100) NOT NULL,
    preco DECIMAL(10, 2) NOT NULL
);
```

---

## ✅ Exemplo de Migration de Alteração
Arquivo: `V2__add_column_status.sql`
```sql
ALTER TABLE produto ADD COLUMN status VARCHAR(20) DEFAULT 'ATIVO';
```

---

## ✅ Inserts Iniciais
Arquivo: `V3__insert_tipos.sql`
```sql
INSERT INTO status (codigo, descricao) VALUES ('ATIVO', 'Ativo');
INSERT INTO status (codigo, descricao) VALUES ('INATIVO', 'Inativo');
```

---

## ✅ Configuração no `application.properties`
```properties
spring.flyway.enabled=true
spring.flyway.locations=classpath:db/migration
spring.flyway.baseline-on-migrate=true
```

---

## ✅ Comandos Flyway via Maven
- Aplicar migrations:
```bash
mvn flyway:migrate
```
- Validar migrations:
```bash
mvn flyway:validate
```
- Limpar banco (cuidado):
```bash
mvn flyway:clean
```

---

## ✅ Rollback
Flyway **não faz rollback automático**.  
Para reverter, crie um novo script (ex.: `V4__rollback_status.sql`):
```sql
ALTER TABLE produto DROP COLUMN status;
```

---

## ✅ Boas práticas
✔ Sempre versionar scripts no **Git**  
✔ Nunca editar scripts já aplicados → crie nova migration  
✔ Defina **baseline** para bancos legados:
```properties
spring.flyway.baseline-on-migrate=true
```
✔ Use migrations para **inserts iniciais** (LOVs, dados default)  

---

## Referências
- [Flyway Documentation](https://flywaydb.org/documentation/)
- [Spring Boot Flyway Guide](https://www.baeldung.com/database-migrations-with-flyway)