---
name: backend-unit-testing
description: >-
  Usa este skill cuando el Backend Developer deba escribir y ejecutar pruebas
  unitarias y de integración ligera en Quarkus con JUnit 5 y Mockito.
---

# Pruebas Unitarias y de Capa en Quarkus 3 + Java 21

Procedimiento para verificar reglas financieras, seguridad y adaptadores antes de entregar a QA y revisión técnica.

## Pasos

1. **Pruebas Puras de Dominio y Casos de Uso (Sin Contenedor)**:
   - Ejecutar tests ultrarrápidos con `JUnit 5` + `Mockito` (`@ExtendWith(MockitoExtension.class)`).
   - Validar cálculos `BigDecimal` verificando tanto valor (`compareTo == 0`) como escala exacta esperada, bordes monetarios y transiciones de estado.
2. **Pruebas de Integración Ligera (`@QuarkusTest`)**:
   - Mockear clientes HTTP salientes declarando **ambas anotaciones juntas**: `@InjectMock @RestClient` (omitir `@RestClient` rompe la inyección CDI).
   - Probar autorización y prevención BOLA usando `@TestSecurity(user = "...", roles = {...})` y claims simulados (`quarkus-test-security`).
   - En pruebas con RestAssured, configurar `JsonConfig.jsonConfig().numberReturnType(BIG_DECIMAL)` o validar decimales en texto plano para evitar conversión a `float`.
3. **Trazabilidad con Criterios de Negocio**:
   - Cubrir cada criterio del `product-owner`: flujo feliz, rechazos de negocio, conflicto de idempotencia y validación de campos obligatorios (`400`).

## Validación

- Ejecutar `./mvnw test` y garantizar 100% de ejecución exitosa antes de notificar entrega a `qa-engineer`, `tech-lead` y `security-specialist`.
