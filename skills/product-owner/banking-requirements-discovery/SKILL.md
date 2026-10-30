---
name: banking-requirements-discovery
description: >-
  Usa este skill cuando el Product Owner deba recoger, clarificar y estructurar
  necesidades de negocio bancario respetando el contrato OpenAPI existente del microservicio.
---

# Descubrimiento de Necesidades Bancarias

Procedimiento estructurado para transformar una solicitud del negocio bancario o revisión funcional en reglas claras alineadas al **contrato OpenAPI vigente (fuente de verdad inmutable)**.

## Pasos

1. **Acotar el contexto operativo y contractual**:
   - Identificar las operaciones involucradas dentro del contrato OpenAPI existente y los canales/microservicios consumidores.
   - Definir criticidad y ventana operativa (24x7 vs. horario de corte con zona horaria/UTC explícita).
2. **Extraer matriz de reglas financieras y de sesión obligatorias**:
   - **Moneda y precisión**: Códigos `ISO-4217` permitidos, cantidad de decimales y regla de redondeo (ej. `HALF_EVEN`) para tasas/comisiones.
   - **Límites y saldos**: Monto mínimo, máximo por operación/acumulado diario y criterio de saldo disponible.
   - **Estados de producto/cliente**: Comportamiento ante cuentas inactivas, bloqueadas, embargadas o cerradas.
   - **Sesión, Access Token y reversos**: Validar que la operación exija sesión activa y `access token` válido propagado desde el flujo previo, comportamiento ante expiración/invalidez de sesión y si aplica anulación/reverso.
3. **Delimitar alcance sin alterar el OpenAPI**:
   - Declarar explícitamente **In Scope** y **Out of Scope** sin proponer cambios al archivo OpenAPI.
   - Si falta alguna regla financiera o hay duda sobre el significado funcional de un campo del OpenAPI, registrarla como `[PREGUNTA ABIERTA]` y consultar antes de asumir.

## Validación (Definition of Ready)

- Cero reglas monetarias, de redondeo, de sesión o de estado dejadas a interpretación del desarrollador.
- Catálogo de motivos de rechazo funcional alineado con los códigos/respuestas declarados en el contrato OpenAPI.
