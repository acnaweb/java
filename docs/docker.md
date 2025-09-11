# Empacotando Aplicação Spring Boot com Docker (Dockerfile)

---

## Problema
Como empacotar e executar uma aplicação **Spring Boot** em um container **Docker**, garantindo uma imagem leve e otimizada para produção?

---

## Solução
Crie um **Dockerfile** com **multi-stage build** para:
- **Compilar** a aplicação usando Maven
- **Executar** a aplicação com uma imagem JDK otimizada

---

## Exemplo de Dockerfile
```dockerfile
# Etapa 1: Build
FROM maven:3.9.8-eclipse-temurin-21 AS build
WORKDIR /opt/app
COPY . .
RUN mvn clean package -DskipTests

# Etapa 2: Runtime
FROM eclipse-temurin:21-alpine-3.21
RUN mkdir /opt/app
COPY --from=build /opt/app/target/app.jar /opt/app/app.jar
WORKDIR /opt/app
CMD [ "java", "-jar", "app.jar"]
```

---

## Como funciona
1. **Imagem base do build:**  
   `maven:3.9.8-eclipse-temurin-21` → contém Maven + JDK 21 para compilar a aplicação.
2. **WORKDIR /opt/app:** define diretório de trabalho.
3. **COPY . .**: copia código fonte para a imagem.
4. **RUN mvn clean package -DskipTests:** compila e gera `app.jar`.
5. **Imagem base para execução:**  
   `eclipse-temurin:21-alpine-3.21` → leve e otimizada para rodar a aplicação.
6. **COPY --from=build:** copia o JAR compilado para a imagem final.
7. **CMD:** comando para iniciar a aplicação.

---

## Exemplo de .dockerignore
Crie um arquivo `.dockerignore` na raiz do projeto para reduzir tamanho da imagem:
```
target/
.git
.gitignore
Dockerfile
docker-compose.yml
*.md
*.log
```
Isso evita copiar arquivos desnecessários para a imagem.

---

## Build e execução do container
```bash
# Build da imagem
docker build -t minha-app .

# Rodando o container
docker run -p 8080:8080 minha-app
```

---

## Publicando no Docker Hub
1. **Login no Docker Hub:**
```bash
docker login
```
2. **Tag da imagem com seu repositório:**
```bash
docker tag minha-app usuario/minha-app:1.0.0
```
3. **Enviar para Docker Hub:**
```bash
docker push usuario/minha-app:1.0.0
```

---

## Boas práticas
✅ Use **multi-stage build** para imagens menores  
✅ Adicione `.dockerignore` para evitar copiar arquivos desnecessários  
✅ Use variáveis de ambiente para configs sensíveis (`ENV` ou `-e`)  
✅ Prefira imagens **Alpine** para reduzir tamanho  
✅ Defina **versão fixa das imagens** para reprodutibilidade  

---

## Referências
- [Docker Docs](https://docs.docker.com/)
- [Cheet Sheat](https://devhints.io/docker-compose)
- [Spring Boot Containerization](https://spring.io/guides/topicals/spring-boot-docker)
- https://github.com/acnaweb/docker
