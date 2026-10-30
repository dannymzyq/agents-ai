# Acta de Revisión y Plan de Mejora — Microservicio Bancario

- **Servicio auditado**: `<nombre-del-microservicio>`
- **Contrato OpenAPI de referencia (Inmutable)**: `<ruta-del-archivo-openapi>`
- **Fecha**: `<YYYY-MM-DD>`
- **Estado del Acta**: `BORRADOR — PENDIENTE APROBACIÓN` | `APROBADA — EN EJECUCIÓN` | `CERRADA`

---

## 1. Diagnóstico Individual por Agente

### 1.1 Product Owner (`product-owner`)
- **Observaciones funcionales, reglas de negocio y coherencia con OpenAPI**:
  - `[ID-PO-01]` `<Archivo:Línea o Endpoint>` — `<Observación concisa>`

### 1.2 Líder Técnico (`tech-lead`)
- **Observaciones de arquitectura, cumplimiento del contrato OpenAPI y resiliencia**:
  - `[ID-TL-01]` `<Archivo:Línea>` — `<Observación concisa>`

### 1.3 Backend Developer (`backend-developer`)
- **Observaciones de implementación Quarkus 3 / Java 21, sesiones/tokens y transaccionalidad**:
  - `[ID-BE-01]` `<Archivo:Línea>` — `<Observación concisa>`

### 1.4 QA Automatización (`qa-engineer`)
- **Observaciones de pruebas de componentes Karate DSL, mocks, sesión/`access token` y bordes**:
  - `[ID-QA-01]` `<Archivo:Línea o Feature>` — `<Observación concisa>`

### 1.5 Especialista en Seguridad (`security-specialist`)
- **Vulnerabilidades, validación/propagación de sesiones y `access tokens`, y exposición de datos**:
  - `[ID-SEC-01]` `[CRÍTICA|ALTA|MEDIA|BAJA]` `<Archivo:Línea>` — `<Vector y evidencia>`

---

## 2. Acuerdos Consensuados del Equipo y Preguntas Abiertas

- **Puntos críticos unificados (Consenso respetando OpenAPI inmutable)**:
  1. `<Síntesis de los hallazgos transversales prioritarios y criterio técnico acordado ajustado al contrato OpenAPI existente>`
- **Preguntas abiertas para el negocio/usuario (`[PREGUNTA ABIERTA]`)**:
  - `<Ninguna | Pregunta puntual o discrepancia detectada frente al OpenAPI que requiere decisión antes de actuar>`

---

## 3. Plan de Trabajo Ejecutable

| ID | Prioridad | Hallazgos Origen | Descripción Concreta del Cambio (Sin alterar OpenAPI) | Responsable | Validadores (Gates) | Criterio de Cierre Verificable | Estado |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| `T-01` | `CRÍTICA` | `ID-SEC-01, ID-BE-01` | `<Acción acotada>` | `backend-developer` | `qa-engineer`, `security-specialist`, `tech-lead` | `<./mvnw test + escenario Karate + 0 hallazgos altos + OpenAPI intacto>` | `PENDIENTE` |

---

## 4. Bitácora de Ejecución y Cierre

- **`T-01`**: `<Pendiente | Resumen de archivos modificados, tests ejecutados y dictamen de cierre>`
