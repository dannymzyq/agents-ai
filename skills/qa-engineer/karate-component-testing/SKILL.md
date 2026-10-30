---
name: karate-component-testing
description: >-
  Usa este skill cuando el QA Engineer diseñe, implemente o ejecute pruebas
  automatizadas de componentes y conformidad contra el contrato OpenAPI con Karate DSL.
---

# Pruebas de Componentes Automatizadas con Karate DSL

Guía para construir y ejecutar pruebas de componentes aisladas, repetibles y paralelizables sobre microservicios Quarkus tomando el **contrato OpenAPI como fuente de verdad**.

## Arquitectura y Diseño de Pruebas

1. **Integración Quarkus + Karate (`@QuarkusTest` + `@Karate.Test`)**:
   - Levantar el microservicio Quarkus real en puerto de prueba e inyectar `baseUrl` dinámico hacia `karate-config.js`.
   - Seguir el patrón de arranque y runner detallado en [quarkus-karate-setup.md](./references/quarkus-karate-setup.md).
2. **Estructura e Independencia de `.feature`**:
   - Alinear tags con los criterios del `product-owner`: `@smoke`, `@happy-path`, `@business-rejection`, `@boundary`, `@session-auth`, `@security`.
   - Configurar en cada escenario cabeceras válidas de sesión, `Authorization: Bearer <access_token>` de prueba e identificadores dinámicos (`java.util.UUID.randomUUID() + ''`) para permitir ejecución paralela sin colisión de estado.
   - Separar esquemas JSON esperados (fieles al contrato OpenAPI vigente) en `classpath:schemas/` usando `read()`.
3. **Aserciones Bancarias y Contractuales Estrictas**:
   - Validar que códigos HTTP, nombres de campos y tipos coincidan exactamente con el contrato OpenAPI usando fuzzy matchers (`#uuid`, `#string`, `#notnull`, `#regex ^\\d+\\.\\d{2}$` para montos con 2 decimales exactos).
   - Verificar cabeceras de respuesta y estructura de errores tal como las define el OpenAPI del servicio.

## Validación

- Ejecutar `./mvnw test -Dtest=*KarateRunner` confirmando cero dependencia de orden entre escenarios y conformidad total con el contrato OpenAPI intacto.
