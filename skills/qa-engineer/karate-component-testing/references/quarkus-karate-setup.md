# Configuración de Pruebas de Componentes: Quarkus + Karate DSL

## 1. Recurso de Ciclo de Vida para Mock Externo (`QuarkusTestResourceLifecycleManager`)
Levanta `MockServer` de Karate **antes** de Quarkus e inyecta la URL en el `@RegisterRestClient`:

```java
public class CoreBankingMockResource implements QuarkusTestResourceLifecycleManager {
    private MockServer server;

    @Override
    public Map<String, String> start() {
        server = MockServer.feature("classpath:mocks/core-banking-mock.feature").http(0).build();
        return Map.of("quarkus.rest-client.\"core-banking-api\".url", "http://localhost:" + server.getPort());
    }

    @Override
    public void stop() {
        if (server != null) server.stop();
    }
}
```

## 2. Runner JUnit 5 (`@QuarkusTest` + `@Karate.Test`)

```java
@QuarkusTest
@QuarkusTestResource(CoreBankingMockResource.class)
class ComponentKarateRunner {

    @TestHTTPResource
    URI baseUri;

    @Karate.Test
    Karate runAll() {
        return Karate.run("classpath:features")
                .systemProperty("quarkus.http.test-url", baseUri.toString())
                .relativeTo(getClass());
    }
}
```

## 3. `karate-config.js` Base

```javascript
function fn() {
  var baseUrl = karate.properties['quarkus.http.test-url'] || 'http://localhost:8081';
  var config = { baseUrl: baseUrl };
  karate.configure('connectTimeout', 5000);
  karate.configure('readTimeout', 5000);
  karate.configure('headers', {
    'Accept': 'application/json',
    'Content-Type': 'application/json'
  });
  return config;
}
```
