# Gerenciamento de Múltiplos Containers com Docker Compose (API + Banco de Dados)

---

## Problema
Como executar **múltiplos containers** (por exemplo, uma API Spring Boot e um banco de dados MySQL) de forma coordenada, evitando a necessidade de executar manualmente vários `docker run`?

---

## Solução
Use **Docker Compose**, que permite definir e orquestrar múltiplos serviços em um único arquivo `docker-compose.yml`.

---

## Exemplo de docker-compose.yml
```yaml
version: "3.9"

services:
  api:
    build: .
    container_name: springboot-api
    ports:
      - "8080:8080"
    depends_on:
      - db
    environment:
      SPRING_DATASOURCE_URL: jdbc:mysql://db:3306/${MYSQL_DATABASE}
      SPRING_DATASOURCE_USERNAME: ${MYSQL_USER}
      SPRING_DATASOURCE_PASSWORD: ${MYSQL_PASSWORD}
    networks:
      - app-network

  db:
    image: mysql:8.0
    container_name: mysql-db
    environment:
      MYSQL_ROOT_PASSWORD: ${MYSQL_ROOT_PASSWORD}
      MYSQL_DATABASE: ${MYSQL_DATABASE}
      MYSQL_USER: ${MYSQL_USER}
      MYSQL_PASSWORD: ${MYSQL_PASSWORD}
    ports:
      - "3307:3306"
    volumes:
      - db_data:/var/lib/mysql
    networks:
      - app-network

volumes:
  db_data:

networks:
  app-network:
```

---

## Exemplo de arquivo `.env`
```env
MYSQL_ROOT_PASSWORD=root_pwd
MYSQL_DATABASE=meubanco
MYSQL_USER=root
MYSQL_PASSWORD=root_pwd
```

O Docker Compose lê automaticamente o arquivo `.env` localizado no mesmo diretório do `docker-compose.yml`.

---

## Comandos para usar
```bash
# Subir os containers
docker-compose up -d

# Ver logs da API
docker-compose logs -f api

# Derrubar tudo
docker-compose down
```

---

## Escalando serviços
Você pode escalar a API para rodar múltiplas instâncias:
```bash
docker-compose up --scale api=3 -d
```
Isso criará 3 containers da API rodando em paralelo.

---

## Acessando os serviços
- API: `http://localhost:8080`
- Banco: `localhost:3307` (para acesso externo)

---

## Boas práticas
✅ Use **volumes** para persistência de dados  
✅ Configure variáveis sensíveis via arquivo `.env`  
✅ Utilize **networks** para comunicação interna segura  
✅ Evite expor portas desnecessárias em produção  
✅ Use **healthchecks** para monitorar serviços críticos  

---

## Referências
- [Docker Compose Documentation](https://docs.docker.com/compose/)
- [Docker Environment Variables](https://docs.docker.com/compose/environment-variables/)