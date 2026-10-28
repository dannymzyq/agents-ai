---
name: microservice-architecture-design
description: >-
  Usa este skill cuando el Líder Técnico deba definir arquitectura, contratos API
  (OpenAPI), límites de dominio o patrones de resiliencia para un microservicio bancario.
---

# Diseño Arquitectónico y Evolución de Microservicios Bancarios

Procedimiento para definir contratos, límites limpios y estrategia de cambio seguro en microservicios con Quarkus y Java 21.

## Pasos

1. **Fronteras de Arquitectura Hexagonal / Limpia**:
   - **Dominio**: Entidades, Value Objects monetarios (`Money`) y políticas puras en Java 21 sin dependencias de Jakarta REST, Jackson ni Panache.
   - **Aplicación**: Casos de uso y puertos de entrada/salida; orquestación transaccional desacoplada de llamadas HTTP externas.
   - **Adaptadores**: Recursos REST Quarkus, clientes `@RegisterRestClient` y repositorios/entidades de persistencia.
2. **Contrato API-First y Estándar de Errores**:
   - Rutas versionadas (`/api/v1/...`), representación monetaria sin punto flotante (`amount` decimal exacto + `currency` `ISO-4217`) y errores uniformes bajo **RFC 9457 / 7807 (`Problem Details`)** con código de negocio estable.
   - Cabeceras obligatorias: `X-Correlation-Id` en toda operación e `Idempotency-Key` en toda operación que mute estado (`POST`/`PATCH`).
   - Usa la plantilla en [api-contract-and-adr-template.md](./references/api-contract-and-adr-template.md).
3. **Resiliencia y Análisis de Impacto ante Cambios**:
   - Definir `connect-timeout`, `read-timeout` y `CircuitBreaker`/`Bulkhead` por dependencia externa; **prohibir `@Retry` automático en operaciones mutables salvo idempotencia garantizada extremo a extremo**.
   - En cambios sobre servicios existentes, exigir compatibilidad hacia atrás y migración *expand-and-contract* en API y base de datos.

## Validación

- Contrato suficientemente preciso para que `backend-developer` y `qa-engineer` trabajen en paralelo sin adivinar campos, cabeceras ni códigos HTTP.
