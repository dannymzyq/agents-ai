---
name: acceptance-criteria-bdd
description: >-
  Usa este skill cuando el Product Owner deba redactar Historias de Usuario y
  Criterios de Aceptación en formato Gherkin alineados al contrato OpenAPI bancario.
---

# Historias de Usuario y Criterios de Aceptación (BDD)

Guía para escribir criterios verificables en Gherkin alineados con el **contrato OpenAPI existente** y listos para las pruebas de componentes en Karate DSL.

## Estructura Obligatoria

1. **Historia de Usuario y Reglas Base**:
   - Formato `Como <consumidor> / Quiero <operación> / Para <impacto de negocio>` acompañado de la tabla resumen de moneda, escala decimal, límites y validación de sesión/`access token`.
2. **Escenarios Gherkin Etiquetados (Karate-Ready)**:
   - `@happy-path`: Operación válida con sesión y `access token` vigentes verificando resultado y saldos esperados.
   - `@business-rejection`: Rechazos funcionales explícitos con el código/estado esperado según el contrato OpenAPI (fondos insuficientes, cuenta bloqueada, moneda distinta, límite superado).
   - `@boundary`: Valores límite exactos (`min`, `max`, `max + 0.01`, `0.00`, exceso de decimales).
   - `@session-auth`: Rechazo ante `access token` o sesión ausente, expirada o no perteneciente al titular de la cuenta operada.
3. **Plantilla de Referencia**:
   - Sigue la estructura exacta definida en [hu-gherkin-banking-template.md](./references/hu-gherkin-banking-template.md).

## Validación

- Todos los montos en ejemplos (`Scenario Outline` / `Examples`) usan formato decimal exacto con escala fija (`"1500.00"`) y código `ISO-4217`.
- Ningún criterio contradice ni exige modificar rutas, cabeceras o esquemas del contrato OpenAPI vigente.
