---
name: quarkus-java21-development
description: >-
  Usa este skill cuando el Backend Developer implemente o modifique microservicios
  utilizando Quarkus y Java 21 ciñéndose al contrato OpenAPI existente y propagación de tokens/sesión.
---

# Desarrollo de Microservicios con Quarkus 3.x y Java 21

Directrices técnicas para implementar microservicios bancarios fieles a su **contrato OpenAPI inmutable**.

## Reglas de Implementación

1. **Java 21 Idiomático y Seguro**:
   - Usar `record` con constructor compacto de validación para DTOs y Value Objects; usar `sealed interface` + `switch` exhaustivo para resultados de casos de uso.
   - Aplicar `@RunOnVirtualThread` únicamente en métodos síncronos/imperativos (nunca sobre retornos `Uni<T>`/`Multi<T>`). Evitar `synchronized` alrededor de llamadas de red/BD para impedir *carrier thread pinning* (usar `ReentrantLock` si se requiere exclusión en memoria).
2. **Fidelidad al OpenAPI, Jackson y CDI**:
   - **Prohibido modificar el archivo OpenAPI**: Implementar rutas, cabeceras, parámetros, DTOs y `@ServerExceptionMapper` ajustándose con exactitud a los esquemas y códigos HTTP definidos en el OpenAPI vigente (sin exponer stacktraces).
   - Inyección por constructor en beans `@ApplicationScoped` (sin `@Inject` en campos mutables).
   - Configurar Jackson para precisión financiera: `quarkus.jackson.write.write-bigdecimal-as-plain=true` y `quarkus.jackson.read.use-big-decimal-for-floats=true`.
3. **Propagación de Sesión/Access Token, Clientes Externos y MDC**:
   - Declarar clientes aguas abajo con `@RegisterRestClient(configKey = "...")` exigiendo `connect-timeout` y `read-timeout` explícitos.
   - Propagar el `access token` recibido, el contexto de sesión y cabeceras de correlación hacia microservicios aguas abajo mediante `@RegisterClientHeaders` / `ClientHeadersFactory` (ej. `quarkus-oidc-token-propagation` o propagación de headers según arquitectura del servicio), limpiando `org.jboss.logging.MDC` en `@ServerResponseFilter`.

## Validación

- Ejecutar `./mvnw compile` verificando compilación limpia en Java 21, cero alteraciones al OpenAPI y cero lógica del framework filtrada al paquete de dominio.
