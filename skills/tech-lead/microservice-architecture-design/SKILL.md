---
name: microservice-architecture-design
description: >-
  Usa este skill cuando el Líder Técnico evalúe arquitectura, conformidad contra el
  contrato OpenAPI inmutable, límites de dominio o resiliencia en un microservicio bancario.
---

# Arquitectura y Alineación con Contrato OpenAPI Bancario

Procedimiento para estructurar límites limpios y cambios internos seguros en Quarkus y Java 21 **manteniendo el contrato OpenAPI existente como fuente de verdad inmutable**.

## Pasos

1. **Fronteras de Arquitectura Hexagonal / Limpia**:
   - **Dominio**: Entidades, Value Objects monetarios (`Money`) y políticas puras en Java 21 sin dependencias de Jakarta REST, Jackson ni Panache.
   - **Aplicación**: Casos de uso y puertos de entrada/salida; orquestación transaccional desacoplada de llamadas HTTP externas.
   - **Adaptadores**: Recursos REST Quarkus ceñidos al OpenAPI, clientes `@RegisterRestClient` y repositorios/entidades de persistencia.
2. **Contrato OpenAPI como Fuente de Verdad Inmutable**:
   - **Prohibido editar el archivo OpenAPI**: Rutas, verbos HTTP, cabeceras (sesión, `Authorization` / `access token`, correlación), esquemas JSON y estructuras de respuesta/error se toman estrictamente del OpenAPI vigente.
   - Si el código actual diverge del OpenAPI, planificar la corrección en la implementación Java/Quarkus para cumplir el contrato.
   - Documentar el mapeo y decisiones internas con [openapi-alignment-and-adr-template.md](./references/openapi-alignment-and-adr-template.md).
3. **Propagación de Contexto, Resiliencia e Impacto**:
   - Garantizar propagación limpia del `access token` y contexto de sesión hacia los microservicios aguas abajo que lo consumen.
   - Definir `connect-timeout`, `read-timeout` y `CircuitBreaker`/`Bulkhead` por dependencia externa; **prohibir `@Retry` automático en operaciones que muten saldos o generen transacciones**.
   - En cambios de base de datos, exigir migraciones compatibles hacia atrás (*expand-and-contract*).

## Validación

- Cero modificaciones propuestas sobre el archivo OpenAPI y conformidad total de entradas, salidas y códigos HTTP respecto a dicho contrato.
