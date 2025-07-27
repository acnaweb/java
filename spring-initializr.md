# Spring Initializr – Ferramenta para Criação de Projetos Spring Boot

---

## Problema
Como criar rapidamente um projeto **Spring Boot** configurado com as dependências necessárias para desenvolvimento web, persistência de dados e boas práticas sem ter que configurar tudo manualmente?

---

## Solução
Utilize o **Spring Initializr**, ferramenta oficial do Spring para gerar projetos prontos.

### Acesse:
🌐 [https://start.spring.io/](https://start.spring.io/)

---

### Passos para criar o projeto:
1. **Defina as configurações principais:**
   - **Project:** Maven Project (ou Gradle)
   - **Language:** Java
   - **Spring Boot:** Última versão estável
   - **Project Metadata:**
     - Group: `com.exemplo`
     - Artifact: `meu-projeto`
     - Package: `com.exemplo.meuprojeto`
   - **Packaging:** Jar
   - **Java:** 17 ou superior

2. **Selecione as dependências:**
   - ✅ **Spring Web** → para criar controladores REST
   - ✅ **Spring Data JPA** → para persistência com JPA/Hibernate
   - ✅ **MySQL Driver** → para integração com banco de dados MySQL
   - ✅ **Lombok** → para reduzir boilerplate (getters, setters, construtores)

3. Clique em **Generate** → será baixado um `.zip` com a estrutura pronta.

---

### Estrutura do Projeto Gerado
```
src/
  main/
    java/
      com.exemplo.meuprojeto/
        MeuProjetoApplication.java
    resources/
      application.properties
pom.xml
```

---

## Configuração do `application.properties`
Exemplo de conexão com MySQL:
```properties
spring.datasource.url=jdbc:mysql://localhost:3306/meubanco
spring.datasource.username=root
spring.datasource.password=senha
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
```

---

## Alternativas
- **CLI do Spring Boot:**  
  ```
  spring init --dependencies=web,data-jpa,mysql,lombok meu-projeto
  ```
- **IDE (IntelliJ, STS, VS Code):** Plugins integrados com Spring Initializr
- **Ferramenta para arquitetura:** [JHipster](https://www.jhipster.tech/) (para gerar apps complexos)