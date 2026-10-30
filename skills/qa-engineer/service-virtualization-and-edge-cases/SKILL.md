---
name: service-virtualization-and-edge-cases
description: >-
  Usa este skill cuando el QA Engineer necesite aislar dependencias externas
  mediante mocks en Karate/WireMock y probar propagación de sesión/tokens, bordes bancarios o fallos de red.
---

# Virtualización de Servicios y Casos Borde Bancarios

Procedimiento para aislar el microservicio bajo prueba frente a sistemas externos y validar propagación de sesión/`access token`, resiliencia y reglas límite.

## Pasos

1. **Ciclo de Vida de Mocks y Verificación de Propagación**:
   - Iniciar `com.intuit.karate.http.MockServer` (o WireMock) **antes** del arranque de Quarkus mediante `QuarkusTestResourceLifecycleManager`, sobreescribiendo `quarkus.rest-client."<config-key>".url` con la URL dinámica del mock.
   - En los mocks de microservicios aguas abajo, **validar que el microservicio bajo prueba propague correctamente el `access token` y las cabeceras de sesión/trazabilidad** recibidas en la petición entrante.
2. **Matriz Obligatoria de Casos Borde Bancarios**:
   - **Fronteras monetarias**: `0.00`, monto negativo, `min`, `max` exacto, `max + 0.01` y exceso de decimales (`10.999`).
   - **Sesión y Control de Acceso**: Petición sin `access token`/sesión, con token expirado o firma inválida (`401`), y operación sobre cuenta perteneciente a otro titular distinto al de la sesión/token (`403` BOLA).
   - **Resiliencia aguas abajo**: Simular `401` aguas abajo (sesión invalidada), `500`, `503` y latencia mayor al `read-timeout` (`delay`) en el mock externo verificando respuesta controlada según el contrato OpenAPI sin filtrar trazas internas.

## Validación

- Confirmar que ningún escenario de prueba de componente realice llamadas de red hacia ambientes externos reales.
