---
name: banking-transactional-integrity
description: >-
  Usa este skill cuando el Backend Developer implemente operaciones monetarias,
  persistencia transaccional, control de idempotencia o auditoría en servicios bancarios.
---

# Integridad Transaccional y Monetaria Bancaria

Reglas obligatorias para proteger exactitud contable, idempotencia atómica y consistencia concurrente en Quarkus.

## Reglas de Implementación

1. **Aritmética Monetaria (`Money` Value Object)**:
   - Instanciar montos únicamente con `new BigDecimal(String)` o `BigDecimal.valueOf(...)`; prohibido `float`/`double`.
   - Normalizar escala según moneda `ISO-4217` con `RoundingMode` explícito (ej. `HALF_EVEN`) y comparar montos con `a.compareTo(b) == 0` (nunca `equals()`).
2. **Idempotencia Atómica (Anti-TOCTOU)**:
   - Persistir registro de idempotencia con restricción `UNIQUE (idempotency_key, operation)` y hash SHA-256 del payload relevante.
   - Si la clave ya existe: devolver respuesta original si el hash coincide (`200`/`201`), o rechazar con `409 Conflict` / `422 Unprocessable Content` si el payload cambió o una ejecución previa sigue `IN_PROGRESS`.
3. **Concurrencia, Transacciones y Esquema BD**:
   - En débitos/transferencias concurrentes, proteger saldos con `@Version` (optimista) o `LockModeType.PESSIMISTIC_WRITE` con `jakarta.persistence.lock.timeout` adquiriendo bloqueos en **orden determinista de ID de cuenta** para evitar *deadlocks*.
   - Registrar siempre un movimiento/asiento contable inmutable en la misma transacción `@Transactional` de actualización de saldo; nunca invocar llamadas HTTP externas dentro de `@Transactional` (usar pasos separados o patrón Outbox).
   - Mantener `quarkus.hibernate-orm.database.generation=validate` en `%prod` gestionando DDL con Flyway o Liquibase.

## Validación

- Confirmar que peticiones concurrentes o repetidas con el mismo `Idempotency-Key` jamás generen doble cargo ni saldos negativos no permitidos.
