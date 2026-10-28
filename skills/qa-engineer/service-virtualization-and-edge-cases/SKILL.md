---
name: service-virtualization-and-edge-cases
description: >-
  Usa este skill cuando el QA Engineer necesite aislar dependencias externas
  mediante mocks en Karate/WireMock y probar casos borde bancarios o fallos de red.
---

# Virtualización de Servicios y Casos Borde Bancarios

Procedimiento para aislar el microservicio bajo prueba frente a sistemas externos y validar su resiliencia y reglas límite.

## Pasos

1. **Ciclo de Vida de Mocks para Clientes Quarkus**:
   - Iniciar `com.intuit.karate.http.MockServer` (o WireMock) **antes** del arranque de Quarkus mediante `QuarkusTestResourceLifecycleManager`, sobreescribiendo `quarkus.rest-client."<config-key>".url` con la URL dinámica del mock.
   - Enrutar respuestas del mock por header/identificador de cuenta o monto para permitir que múltiples escenarios corran sin interferirse.
2. **Matriz Obligatoria de Casos Borde Bancarios**:
   - **Fronteras monetarias**: `0.00`, monto negativo, `min`, `max` exacto, `max + 0.01` y exceso de decimales (`10.999`).
   - **Idempotencia completa**: (a) Reintento idéntico con mismo `Idempotency-Key` → misma respuesta sin duplicar efecto; (b) Mismo `Idempotency-Key` con distinto monto/cuenta → rechazo `409`/`422`.
   - **Control de acceso**: Petición sin token/expirado (`401`) y operación sobre cuenta perteneciente a otro cliente (`403` BOLA).
   - **Resiliencia aguas abajo**: Simular `500`, `503` y latencia mayor al `read-timeout` (`delay`) en el mock del Core bancario verificando degradación controlada (`502`/`504`) sin filtrar trazas internas.

## Validación

- Confirmar que ningún escenario de prueba de componente realice llamadas de red hacia ambientes externos reales.
