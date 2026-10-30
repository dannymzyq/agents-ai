# Equipo de Agentes SDLC — Microservicios Bancarios

Configuración multi-agente portable para Visual Studio Code (Agent Mode) orientada a la revisión, planificación y evolución de microservicios bancarios con **Java 21**, **Quarkus 3.x** y **Karate DSL**.

## Agentes y Grupo de Skills Asignados

| Agente (`.github/agents/`) | Rol | Skills Asignados (`skills/<rol>/`) |
| :--- | :--- | :--- |
| `sdlc-orchestrator` | Coordinador del Comité SDLC | Orquesta revisión integral, Acta de mejoras y ejecución del plan |
| `product-owner` | Product Owner Bancario | `banking-requirements-discovery`, `acceptance-criteria-bdd` |
| `tech-lead` | Líder Técnico y Arquitecto | `microservice-architecture-design`, `code-and-change-review` |
| `backend-developer` | Backend Developer (Quarkus + Java 21) | `quarkus-java21-development`, `banking-transactional-integrity`, `backend-unit-testing` |
| `qa-engineer` | QA Automatización (Karate DSL) | `karate-component-testing`, `service-virtualization-and-edge-cases` |
| `security-specialist` | Especialista en Seguridad | `api-security-and-auth-audit`, `data-protection-and-vulnerability-scan` |

## Protocolo de Orquestación sobre un Microservicio Existente

El archivo `docs/sdlc/ACTA-REVISION.md` (basado en `docs/sdlc/templates/ACTA-TEMPLATE.md`) es la **memoria compartida** entre los agentes:

1. **Fase 1 — Revisión Individual (Solo Lectura)**: Cada uno de los 5 especialistas inspecciona el microservicio existente bajo sus propios criterios y `SKILL.md` contrastándolo contra el **contrato OpenAPI vigente**, sin alterar código productivo ni el OpenAPI.
2. **Fase 2 — Consenso de Equipo y Acta (`docs/sdlc/ACTA-REVISION.md`)**: `sdlc-orchestrator` consolida las 5 revisiones, elimina duplicados, resuelve conflictos entre roles, prioriza riesgos y genera el Plan de Acción (`T-01..T-NN`). **Se detiene obligatoriamente esperando aprobación del usuario.**
3. **Fase 3 — Ejecución Conjunta del Plan**: Con el Acta aprobada, por cada tarea:
   - `product-owner` / `tech-lead` fijan criterios funcionales y alineación arquitectónica respetando estrictamente el contrato OpenAPI existente.
   - `backend-developer` implementa en Quarkus/Java 21 y valida `./mvnw test`.
   - `qa-engineer` automatiza/actualiza pruebas de componentes y mocks en Karate DSL validando el cumplimiento del contrato OpenAPI y la propagación de sesión/`access token`.
   - `security-specialist` y `tech-lead` auditan los cambios antes de marcar la tarea como `COMPLETADA` en el Acta.

## Compuertas de Calidad (Gates de Bloqueo)

- **Fallo en QA (`qa-engineer`)**: Retorna a `backend-developer` con evidencia reproducible (escenario, payload, esperado según OpenAPI vs. obtenido, `X-Correlation-Id`).
- **Bloqueo de Seguridad (`security-specialist`)**: Hallazgo `[CRÍTICA]` o `[ALTA]` marca la tarea como `BLOQUEADA` hasta su remediación y prueba de regresión.
- **Bloqueo Técnico (`tech-lead`)**: Hallazgo `[BLOQUEANTE]` (incluyendo cualquier desviación o intento de alterar el contrato OpenAPI) marca la tarea como `REQUIERE CAMBIOS`.

## Principios Bancarios No Negociables

- **Contrato OpenAPI como fuente de verdad inmutable**: Queda **estrictamente prohibido modificar el contrato OpenAPI** del microservicio. Toda arquitectura, implementación REST/DTOs/errores y prueba Karate debe ajustarse con exactitud al contrato OpenAPI existente.
- **Validación por Sesión y Access Token propagado**: El control de validez, autorización y contexto operativo utiliza las **sesiones y los `access tokens` generados previamente**, los cuales son consumidos, validados y propagados entre los microservicios.
- **Precisión monetaria**: `BigDecimal` exclusivo con escala, moneda `ISO-4217` y `RoundingMode` explícitos; serialización JSON plana (`write-bigdecimal-as-plain=true`) y comparación con `compareTo == 0`. Prohibido `float`/`double`.
- **Trazabilidad sin fuga de datos**: Propagar cabeceras de trazabilidad y sesión según contrato; prohibido exponer PII, PAN, CVV, credenciales, identificadores sensibles de sesión, `access tokens` o stacktraces en logs, `Record.toString()` o respuestas HTTP.
- **Cero invenciones**: Ningún agente asume reglas financieras no definidas ni altera el OpenAPI; marca `[PREGUNTA ABIERTA]` y consulta al usuario.
