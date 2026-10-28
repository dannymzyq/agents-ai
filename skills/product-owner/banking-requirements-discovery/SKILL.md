---
name: banking-requirements-discovery
description: >-
  Usa este skill cuando el Product Owner deba recoger, clarificar y estructurar
  necesidades de negocio bancario antes de diseñar o construir un microservicio.
---

# Descubrimiento de Necesidades Bancarias

Procedimiento estructurado para transformar una solicitud del negocio bancario en reglas funcionales libres de ambigüedad.

## Pasos

1. **Acotar el contexto operativo**:
   - Tipo de operación (consulta, transferencia, pago, reverso, bloqueo, apertura) y canales consumidores (App móvil, Banca web, B2B, Core, batch).
   - Criticidad y ventana operativa (24x7 vs. horario de corte con zona horaria/UTC explícita).
2. **Extraer matriz de reglas financieras obligatorias**:
   - **Moneda y precisión**: Códigos `ISO-4217` permitidos, cantidad de decimales y regla de redondeo (ej. `HALF_EVEN`) para tasas/comisiones.
   - **Límites y saldos**: Monto mínimo, máximo por operación/acumulado diario y criterio de saldo disponible.
   - **Estados de producto/cliente**: Comportamiento ante cuentas inactivas, bloqueadas, embargadas o cerradas.
   - **Idempotencia y reversos**: Ventana de deduplicación, respuesta ante reintento idéntico vs. conflicto por misma clave con distinto monto/destino, y si aplica anulación/reverso.
3. **Delimitar alcance y vacíos**:
   - Declarar explícitamente **In Scope** y **Out of Scope**.
   - Si falta alguna definición financiera o regulatoria, registrarla como `[PREGUNTA ABIERTA]` y consultar antes de asumir.

## Validación (Definition of Ready)

- Cero reglas monetarias, de redondeo o de estado dejadas a interpretación del desarrollador.
- Catálogo preliminar de motivos de rechazo funcional definido (ej. `INSUFFICIENT_FUNDS`, `ACCOUNT_BLOCKED`, `DAILY_LIMIT_EXCEEDED`).
