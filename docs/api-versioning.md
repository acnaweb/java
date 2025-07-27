# API Versioning no Spring Boot – Estratégias e Configuração Dinâmica

---

## ✅ Problema
Como versionar uma API REST em **Spring Boot** para suportar múltiplas versões sem duplicar código e sem hardcode nas rotas?

---

## ✅ Solução
Existem quatro estratégias principais para versionamento de APIs no Spring Boot:
1. **Versionamento por URL** (mais comum)
2. **Por Query Parameter**
3. **Por Header**
4. **Por Content Negotiation (Accept Header)**

Além disso, vamos ver como **evitar hardcode** usando **variáveis externas**.

---

## ✅ 1. Versionamento por URL
A versão faz parte da rota:
```java
@RestController
@RequestMapping("/api/v1/produtos")
public class ProdutoControllerV1 {
    @GetMapping
    public String listar() {
        return "Versão 1";
    }
}
```

Nova versão:
```java
@RestController
@RequestMapping("/api/v2/produtos")
public class ProdutoControllerV2 {
    @GetMapping
    public String listar() {
        return "Versão 2";
    }
}
```

✔ **Mais usada** | ✖ Polui URL

---

## ✅ 2. Versionamento por Query Parameter
```
GET /api/produtos?version=1
```
```java
@GetMapping(value = "/produtos", params = "version=1")
public String v1() { return "Versão 1"; }

@GetMapping(value = "/produtos", params = "version=2")
public String v2() { return "Versão 2"; }
```

✔ Fácil de implementar | ✖ Menos RESTful

---

## ✅ 3. Versionamento por Header
```
GET /api/produtos
Header: X-API-VERSION=1
```
```java
@GetMapping(value = "/produtos", headers = "X-API-VERSION=1")
public String v1() { return "Versão 1"; }

@GetMapping(value = "/produtos", headers = "X-API-VERSION=2")
public String v2() { return "Versão 2"; }
```

✔ URL limpa | ✖ Difícil de debugar

---

## ✅ 4. Versionamento por Content Negotiation (Accept Header)
```
GET /api/produtos
Accept: application/vnd.api.v1+json
```
```java
@GetMapping(value = "/produtos", produces = "application/vnd.api.v1+json")
public String v1() { return "Versão 1"; }

@GetMapping(value = "/produtos", produces = "application/vnd.api.v2+json")
public String v2() { return "Versão 2"; }
```

✔ Padrão avançado | ✖ Complexo para clientes

---

## ✅ Como evitar Hardcode usando variáveis externas

### **1. Configuração no application.properties**
```properties
api.version=v1
```

---

### **2. Controller com Placeholder**
```java
@RestController
@RequestMapping("/api/${api.version}/produtos")
public class ProdutoController {

    @GetMapping
    public String listar() {
        return "API com versão dinâmica";
    }
}
```

Agora, basta alterar `api.version` no **application.properties** ou via variável de ambiente.

---

### **3. Usando Profiles**
**application-dev.properties**
```properties
api.version=v1
```

**application-prod.properties**
```properties
api.version=v2
```

Ativar:
```bash
-Dspring.profiles.active=prod
```

---

### **4. Acessando a versão no código**
```java
@RestController
@RequestMapping("/api/${api.version}/produtos")
public class ProdutoController {

    @Value("${api.version}")
    private String version;

    @GetMapping("/info")
    public String info() {
        return "Versão atual: " + version;
    }
}
```

---

## ✅ Boas práticas
✔ Documente versões no **Swagger**  
✔ Defina ciclo de vida das versões (depreciação)  
✔ Use variáveis externas para evitar alterações no código  
✔ Para APIs públicas, prefira **Headers ou Content Negotiation**  
✔ Para APIs internas, **versionamento por URL** é suficiente  

---

## Referências
- [Spring Docs – Request Mapping](https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-controller/ann-requestmapping.html)
- [RFC 6838 – Media Type Versioning](https://www.rfc-editor.org/rfc/rfc6838)
