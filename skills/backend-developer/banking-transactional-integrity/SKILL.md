---
name: banking-transactional-integrity
description: >-
  Usa este skill cuando el Backend Developer implemente operaciones monetarias,
  persistencia transaccional, validación de contexto de sesión/access token o auditoría bancaria.
---

# Integridad Transaccional y Monetaria Bancaria

Reglas obligatorias para proteger exactitud contable, validez de sesión/token y consistencia concurrente en Quarkus.

## Reglas de Implementación

1. **Aritmética Monetaria (`Money` Value Object)**:
   - Instanciar montos únicamente con `new BigDecimal(String)` o `BigDecimal.valueOf(...)`; prohibido `float`/`double`.
   - Normalizar escala según moneda `ISO-4217` con `RoundingMode` explícito (ej. `HALF_EVEN`) y comparar montos con `a.compareTo(b) == 0` (nunca `equals()`).
2. **Validación por Sesión y Access Token Propagado**:
   - Asegurar que toda operación financiera verifique la vigencia y coherencia de la **sesión activa** y los claims del **`access token` propagado**, vinculando la identidad del titular con las cuentas involucradas antes de ejecutar cualquier afectación.
3. **Concurrencia, Transacciones y Esquema BD**:
   - En débitos/transferencias concurrentes, proteger saldos con `@Version` (optimista) o `LockModeType.PESSIMISTIC_WRITE` con `jakarta.persistence.lock.timeout` adquiriendo bloqueos en **orden determinista de ID de cuenta** para evitar *deadlocks*.
   - Registrar siempre un movimiento/asiento contable inmutable en la misma transacción `@Transactional` de actualización de saldo; nunca invocar llamadas HTTP externas dentro de `@Transactional` (usar pasos separados o patrón Outbox).
   - Mantener `quarkus.hibernate-orm.database.generation=validate` en `%prod` gestionando DDL con Flyway o Liquibase.

## Validación

- Confirmar que peticiones concurrentes o con sesión/`access token` inválido jamás comprometan la consistencia de saldos ni generen saldos negativos no permitidos.
