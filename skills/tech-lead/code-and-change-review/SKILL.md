---
name: code-and-change-review
description: >-
  Usa este skill cuando el Líder Técnico deba revisar cambios de código, pull requests,
  refactorizaciones o evaluar impacto técnico sobre microservicios en Quarkus y Java 21.
---

# Revisión de Código e Impacto ante Cambios

Checklist técnico para auditar entregas y modificaciones sobre microservicios bancarios en Quarkus 3.x y Java 21.

## Pasos de Revisión

1. **Alcance y Compatibilidad**:
   - Inspeccionar `git diff` contra los criterios del `product-owner` y el contrato aprobado.
   - Verificar que no existan *breaking changes* silenciosos en JSON, rutas HTTP ni migraciones destructivas de BD (`DROP`/`RENAME` directo).
2. **Antipatrones Críticos en Quarkus 3 y Java 21**:
   - **Concurrencia**: Bloqueo accidental del Event Loop de Vert.x; `@RunOnVirtualThread` mal aplicado sobre `Uni`/`Multi` o con bloques `synchronized` alrededor de I/O (*thread pinning*).
   - **Transacciones**: `@Transactional` envolviendo llamadas `@RestClient` lentas (retención de conexiones DB y riesgo de inconsistencia).
   - **Dinero y Dominio**: Uso de `double`/`float`, `BigDecimal.equals()` en lugar de `compareTo() == 0`, o `record` con datos sensibles sin sobrescribir `toString()`.
   - **Trazabilidad**: Falta de limpieza de `MDC` al finalizar la petición o captura silenciosa de excepciones.
3. **Formato Obligatorio de Dictamen**:
   - **Estado**: `APROBADO` | `APROBADO CON OBSERVACIONES` | `REQUIERE CAMBIOS`
   - **Impacto**: Compatibilidad API/BD y riesgo operativo (`Bajo` / `Medio` / `Alto`).
   - **Hallazgos**: `[BLOQUEANTE | IMPORTANTE | SUGERENCIA] Archivo:Línea — Problema detectado → Corrección concreta`.

## Validación

- Cualquier hallazgo `[BLOQUEANTE]` impide el estado `APROBADO` e indica qué prueba unitaria o escenario Karate debe agregarse.
