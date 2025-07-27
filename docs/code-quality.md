# Code Quality – Checkstyle no Spring Boot

---

## ✅ Problema
Como manter **padrões de código Java** consistentes e detectar violações automaticamente em um projeto Spring Boot?

---

## ✅ Solução
Use **Checkstyle**, uma ferramenta de **análise estática de código**, que valida formatação e boas práticas definidas por regras (Google Style, Sun Style ou customizadas).

---

## ✅ O que é Checkstyle?
- Ferramenta que verifica automaticamente se o código segue padrões predefinidos.
- Pode ser executada via **Maven**, **Gradle**, **IDE** ou **CI/CD**.
- Suporta configurações baseadas nos padrões **Google Java Style** ou **Sun Code Conventions**.

---

## ✅ Instalação no Maven
Adicione o plugin no `pom.xml`:
```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-checkstyle-plugin</artifactId>
            <version>3.3.1</version>
            <configuration>
                <configLocation>google_checks.xml</configLocation>
                <encoding>UTF-8</encoding>
                <consoleOutput>true</consoleOutput>
                <failsOnError>true</failsOnError>
            </configuration>
            <executions>
                <execution>
                    <phase>validate</phase>
                    <goals>
                        <goal>check</goal>
                    </goals>
                </execution>
            </executions>
        </plugin>
    </plugins>
</build>
```

---

## ✅ Como rodar
```bash
mvn checkstyle:check
```

---

## ✅ Arquivos de configuração
Você pode usar:
- **google_checks.xml** (padrão do Google)
- **sun_checks.xml**
- Ou criar um arquivo customizado: **checkstyle.xml**

---

### Exemplo de regra customizada
Arquivo `checkstyle.xml`:
```xml
<!DOCTYPE module PUBLIC "-//Checkstyle//DTD Checkstyle Configuration 1.3//EN"
    "https://checkstyle.org/dtds/configuration_1_3.dtd">
<module name="Checker">
    <module name="TreeWalker">
        <module name="LineLength">
            <property name="max" value="120"/>
        </module>
        <module name="Indentation">
            <property name="basicOffset" value="4"/>
        </module>
    </module>
</module>
```

---

## ✅ Integrando com IDE
- **IntelliJ IDEA:**  
  Instale o plugin **Checkstyle-IDEA** e configure com seu arquivo XML.
- **Eclipse:**  
  Use o **Checkstyle Plugin** disponível no marketplace.

---

## ✅ Integração com CI/CD
No pipeline (exemplo GitHub Actions):
```yaml
name: Build and Checkstyle
on: [push, pull_request]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Set up JDK 21
        uses: actions/setup-java@v3
        with:
          java-version: '21'
          distribution: 'temurin'
      - name: Build and Run Checkstyle
        run: mvn verify
```

Se houver violações e `failsOnError=true`, o build falha.

---

## ✅ Boas práticas
✔ Use **failsOnError=true** para impedir merges com código mal formatado.  
✔ Versione o **checkstyle.xml** para padronização entre equipes.  
✔ Combine com **SpotBugs** e **PMD** para cobertura completa de qualidade.  

---

## Referências
- [Checkstyle Official Site](https://checkstyle.sourceforge.io/)
- [Maven Checkstyle Plugin](https://maven.apache.org/plugins/maven-checkstyle-plugin/)
- [Google Java Style Guide](https://google.github.io/styleguide/javaguide.html)