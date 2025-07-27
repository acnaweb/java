# HTTP Status Codes em APIs Spring Boot

---

## Problema
Como retornar respostas HTTP corretas em uma API **REST** no Spring Boot, indicando **status adequados** para cada tipo de operação (GET, POST, PUT, DELETE)?

---

## Referência Oficial
📘 [MDN HTTP Status Codes](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Status)

---

## Solução
Spring Boot utiliza a classe `ResponseEntity` para construir respostas com **status code**, **headers** e **body**.

### Exemplos práticos:
```java
// Criar um recurso (201 Created)
return ResponseEntity.status(201).body(produto);

// Resposta OK com conteúdo (200 OK)
return ResponseEntity.ok(produto);

// Sem conteúdo (204 No Content)
return ResponseEntity.noContent().build();

// Recurso não encontrado (404 Not Found)
return ResponseEntity.notFound().build();
```

---

## Principais Status HTTP para REST

| Código | Constante              | Significado                               | Exemplo de Uso                              |
|--------|------------------------|-------------------------------------------|---------------------------------------------|
| 200    | OK                     | Requisição bem-sucedida                  | GET /produtos                              |
| 201    | Created                | Recurso criado com sucesso               | POST /produtos                             |
| 204    | No Content             | Sem conteúdo na resposta                 | DELETE /produtos/{id}                      |
| 400    | Bad Request            | Dados inválidos enviados pelo cliente    | POST com JSON inválido                     |
| 404    | Not Found              | Recurso não encontrado                   | GET /produtos/{id} inexistente             |
| 409    | Conflict               | Conflito ao processar a requisição       | Criar recurso duplicado                    |
| 500    | Internal Server Error  | Erro inesperado no servidor              | Exceção não tratada                        |

---

## Exemplo Completo em Spring Boot
```java
@PostMapping
public ResponseEntity<Produto> create(@RequestBody Produto produto) {
    Produto salvo = produtoService.salvar(produto);
    return ResponseEntity.status(HttpStatus.CREATED).body(salvo);
}

@GetMapping("/{id}")
public ResponseEntity<Produto> findById(@PathVariable Long id) {
    return produtoService.buscar(id)
            .map(ResponseEntity::ok)
            .orElse(ResponseEntity.notFound().build());
}

@DeleteMapping("/{id}")
public ResponseEntity<Void> delete(@PathVariable Long id) {
    produtoService.remover(id);
    return ResponseEntity.noContent().build();
}
```

---

## Boas Práticas para Status HTTP em APIs REST
1. **Use o status correto para cada operação:**
   - POST → 201 Created
   - GET → 200 OK
   - PUT/PATCH → 200 OK (ou 204 No Content se não retornar body)
   - DELETE → 204 No Content
2. **Validação de dados:** retornar 400 Bad Request quando houver erro de entrada.
3. **Autenticação e Autorização:**
   - 401 Unauthorized → Falta de autenticação
   - 403 Forbidden → Sem permissão para acessar o recurso
4. **Use ResponseEntity para flexibilidade:**
   Permite configurar status, headers e body.
5. **Evite sempre retornar 500:** trate exceções e retorne mensagens adequadas.

---

## Referências Adicionais
- [MDN: HTTP Response Status Codes](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status)
- [Spring Boot ResponseEntity Docs](https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-controller/ann-responsebody.html)