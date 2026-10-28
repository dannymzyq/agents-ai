---
agent: sdlc-orchestrator
description: "Audita el microservicio existente con los 5 agentes SDLC y genera el Acta de observaciones y Plan de Mejora (sin tocar código productivo)."
---

Ejecuta el **Modo 1 (Auditoría y Generación del Acta — Fases 1 y 2)** sobre el microservicio existente en este espacio de trabajo:

1. Coordina la revisión individual en **solo lectura** de `product-owner`, `tech-lead`, `backend-developer`, `qa-engineer` y `security-specialist`, aplicando rigurosamente sus respectivos `SKILL.md` en `skills/<rol>/`.
2. Consolida las observaciones individuales y de equipo en `docs/sdlc/ACTA-REVISION.md` usando la plantilla `docs/sdlc/templates/ACTA-TEMPLATE.md`, priorizando hallazgos críticos/bloqueantes y estructurando el Plan de Trabajo (`T-01..T-NN`) cuidando compatibilidad hacia atrás.
3. **No modifiques ningún archivo de código productivo ni pruebas aún.** Detente al generar `docs/sdlc/ACTA-REVISION.md` y presenta el resumen ejecutivo para mi revisión y aprobación.
