---
name: karate-component-testing
description: >-
  Usa este skill cuando el QA Engineer diseñe, implemente o ejecute pruebas
  automatizadas de componentes y contratos API utilizando Karate DSL.
---

# Pruebas de Componentes Automatizadas con Karate DSL

Guía para construir y ejecutar pruebas de componentes aisladas, repetibles y paralelizables sobre microservicios Quarkus.

## Arquitectura y Diseño de Pruebas

1. **Integración Quarkus + Karate (`@QuarkusTest` + `@Karate.Test`)**:
   - Levantar el microservicio Quarkus real en puerto de prueba e inyectar `baseUrl` dinámico hacia `karate-config.js`.
   - Seguir el patrón de arranque y runner detallado en [quarkus-karate-setup.md](./references/quarkus-karate-setup.md).
2. **Estructura e Independencia de `.feature`**:
   - Alinear tags con los criterios del `product-owner`: `@smoke`, `@happy-path`, `@business-rejection`, `@boundary`, `@idempotency`, `@security`.
   - Generar `Idempotency-Key`, `X-Correlation-Id` e identificadores de datos únicos (`java.util.UUID.randomUUID() + ''`) por escenario para permitir ejecución paralela sin colisión de estado.
   - Separar contratos/esquemas JSON reutilizables en `classpath:schemas/` usando `read()`.
3. **Aserciones Bancarias Estrictas**:
   - Validar esquema completo con fuzzy matchers (`#uuid`, `#string`, `#notnull`, `#regex ^\\d+\\.\\d{2}$` para montos con 2 decimales exactos).
   - Verificar códigos HTTP exactos, estructura `RFC 9457 Problem Details` en errores y devolución/eco de `X-Correlation-Id`.

## Validación

- Ejecutar `./mvnw test -Dtest=*KarateRunner` confirmando cero dependencia de orden entre escenarios y reporte limpio de Karate.
