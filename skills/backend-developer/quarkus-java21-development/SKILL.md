---
name: quarkus-java21-development
description: >-
  Usa este skill cuando el Backend Developer implemente o modifique microservicios
  utilizando Quarkus y Java 21 (endpoints REST, CDI, configuración y modelo de concurrencia).
---

# Desarrollo de Microservicios con Quarkus 3.x y Java 21

Directrices técnicas para implementar microservicios bancarios limpios, predecibles y de alto rendimiento.

## Reglas de Implementación

1. **Java 21 Idiomático y Seguro**:
   - Usar `record` con constructor compacto de validación para DTOs y Value Objects; usar `sealed interface` + `switch` exhaustivo para resultados de casos de uso.
   - Aplicar `@RunOnVirtualThread` únicamente en métodos síncronos/imperativos (nunca sobre retornos `Uni<T>`/`Multi<T>`). Evitar `synchronized` alrededor de llamadas de red/BD para impedir *carrier thread pinning* (usar `ReentrantLock` si se requiere exclusión en memoria).
2. **Quarkus REST, Jackson y CDI**:
   - Inyección por constructor en beans `@ApplicationScoped` (sin `@Inject` en campos mutables).
   - Configurar Jackson para precisión financiera: `quarkus.jackson.write.write-bigdecimal-as-plain=true` (evita notación científica `1E+2`) y `quarkus.jackson.read.use-big-decimal-for-floats=true`.
   - Validar entradas con Bean Validation (`@Valid`, `@NotNull`, `@Digits`, `@Positive`) y mapear errores con `@ServerExceptionMapper` hacia **RFC 9457 (`Problem Details`)** sin exponer stacktraces.
3. **Clientes Externos, Configuración y MDC**:
   - Declarar clientes aguas abajo con `@RegisterRestClient(configKey = "...")` exigiendo `connect-timeout` y `read-timeout` explícitos en `@ConfigMapping` / `application.properties`.
   - Propagar `X-Correlation-Id` mediante `ClientHeadersFactory` y limpiar `org.jboss.logging.MDC` en un `@ServerResponseFilter` al cerrar el request.

## Validación

- Ejecutar `./mvnw compile` verificando compilación limpia en Java 21 y cero lógica del framework filtrada al paquete de dominio.
