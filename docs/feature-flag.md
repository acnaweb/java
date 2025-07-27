# Feature Flags no Spring Boot

---

## ✅ Problema
Como habilitar ou desabilitar **funcionalidades específicas da API** sem alterar código ou redeploy completo?

---

## ✅ Solução
Implemente **Feature Flags** (ou Feature Toggles) para ativar/desativar funcionalidades via **configuração externa**, variáveis de ambiente ou serviços especializados.

---

## ✅ Configuração no `application.properties`
```properties
feature.produtos.enabled=true
feature.clientes.enabled=false
```

Essas flags podem ser sobrescritas por **variáveis de ambiente** ou **Profiles**.

---

## ✅ Exemplo 1 – Usando Interceptor
Intercepta requisições e bloqueia endpoints desativados.

```java
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

### Registrando o Interceptor:
```java
@Configuration
public class WebConfig implements WebMvcConfigurer {

    private final FeatureFlagInterceptor featureFlagInterceptor;

    public WebConfig(FeatureFlagInterceptor featureFlagInterceptor) {
        this.featureFlagInterceptor = featureFlagInterceptor;
    }

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(featureFlagInterceptor);
    }
}
```

---

## ✅ Exemplo 2 – Controle no Controller
Habilita/desabilita métodos via configuração:
```java
@RestController
@RequestMapping("/clientes")
public class ClienteController {

    @Value("${feature.clientes.enabled:false}")
    private boolean clientesEnabled;

    @GetMapping
    public ResponseEntity<?> listar() {
        if (!clientesEnabled) {
            return ResponseEntity.status(HttpStatus.NOT_IMPLEMENTED)
                    .body("Funcionalidade desativada");
        }
        return ResponseEntity.ok(List.of("Cliente A", "Cliente B"));
    }
}
```

---

## ✅ Exemplo 3 – Usando Profiles
Configure por ambiente:
- **application-dev.properties**:
```properties
feature.clientes.enabled=true
```
- **application-prod.properties**:
```properties
feature.clientes.enabled=false
```

Ativar com:
```bash
-Dspring.profiles.active=prod
```

---

## ✅ Exemplo 4 – Variáveis de Ambiente
Execute a aplicação alterando as flags:
```bash
FEATURE_CLIENTES_ENABLED=false java -jar app.jar
```

---

## ✅ Boas práticas
✔ Centralize as **flags** para fácil manutenção.  
✔ Use variáveis externas para alterar sem redeploy.  
✔ Para projetos grandes → considere ferramentas como:
  - [Unleash](https://www.getunleash.io/)
  - [FF4J](https://github.com/ff4j/ff4j)
  - [LaunchDarkly](https://launchdarkly.com/)  
✔ Planeje a **remoção das flags** depois que não forem mais necessárias.  

---

## Referências
- [Martin Fowler – Feature Toggles](https://martinfowler.com/bliki/FeatureToggle.html)
- [Spring Profiles Docs](https://docs.spring.io/spring-boot/docs/current/reference/html/features.html#features.profiles)