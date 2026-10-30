---
agent: sdlc-orchestrator
description: "Ejecuta en equipo el Plan de Trabajo aprobado en docs/sdlc/ACTA-REVISION.md manteniendo intacto el contrato OpenAPI y superando los gates de QA, Seguridad y Líder Técnico."
---

Ejecuta el **Modo 2 (Ejecución Conjunta del Plan Aprobado — Fase 3)** tomando como fuente de verdad el contrato OpenAPI existente y el plan aprobado en `docs/sdlc/ACTA-REVISION.md`:

1. Verifica que `docs/sdlc/ACTA-REVISION.md` exista y no tenga bloqueos `[PREGUNTA ABIERTA]` sin responder (si existen, consúltame primero).
2. Ejecuta secuencialmente las tareas en estado `PENDIENTE` o `EN_RETRABAJO` coordinando a `product-owner`, `tech-lead`, `backend-developer` (Quarkus + Java 21 + `./mvnw test`), `qa-engineer` (pruebas de componentes Karate DSL + mocks) y la validación final de `security-specialist` y `tech-lead`, **manteniendo el contrato OpenAPI estrictamente intacto**.
3. Actualiza el estado y la bitácora de cada tarea en `docs/sdlc/ACTA-REVISION.md` conforme supere todas las compuertas de calidad.
