---
name: acceptance-criteria-bdd
description: >-
  Usa este skill cuando el Product Owner deba redactar Historias de Usuario y
  Criterios de Aceptación en formato Gherkin para microservicios bancarios.
---

# Historias de Usuario y Criterios de Aceptación (BDD)

Guía para escribir criterios verificables en Gherkin que sirvan como fuente directa para el contrato técnico y las pruebas de componentes en Karate DSL.

## Estructura Obligatoria

1. **Historia de Usuario y Reglas Base**:
   - Formato `Como <consumidor> / Quiero <operación> / Para <impacto de negocio>` acompañado de la tabla resumen de moneda, escala decimal y límites.
2. **Escenarios Gherkin Etiquetados (Karate-Ready)**:
   - `@happy-path`: Operación válida verificando nuevo estado/saldo y referencia generada.
   - `@business-rejection`: Rechazos funcionales explícitos con su código de negocio esperado (fondos insuficientes, cuenta bloqueada, moneda distinta, límite superado).
   - `@boundary`: Valores límite exactos (`min`, `max`, `max + 0.01`, `0.00`, exceso de decimales).
   - `@idempotency`: Reintento con misma clave y mismo payload (sin doble cargo) vs. misma clave con payload distinto (rechazo por conflicto).
3. **Plantilla de Referencia**:
   - Sigue la estructura exacta definida en [hu-gherkin-banking-template.md](./references/hu-gherkin-banking-template.md).

## Validación

- Todos los montos en ejemplos (`Scenario Outline` / `Examples`) usan formato decimal exacto con comillas o escala fija (`"1500.00"`) y código `ISO-4217`.
- Ningún paso Gherkin menciona clases Java, tablas SQL ni detalles internos del framework.
