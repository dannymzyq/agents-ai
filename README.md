# Kit de Agentes SDLC Bancarios para Visual Studio Code

Equipo de 5 agentes especialistas + 1 agente orquestador diseñado para auditar, planificar mejoras (mediante Acta consensuada) y ejecutar cambios sobre microservicios bancarios existentes en **Java 21**, **Quarkus 3.x** y **Karate DSL**, funcionando **100% desde el Chat Agente de VS Code sin instalar Python ni herramientas externas**.

---

## Instalación en la Laptop Corporativa (Solo Copiar y Pegar)

> [!IMPORTANT]
> Asegúrate de incluir las carpetas ocultas (`.github` y `.vscode`). Al comprimir en `.zip` toda esta carpeta y descomprimir su contenido en la **raíz** del repositorio del microservicio existente, todo queda activo sin configuración adicional.

1. **Pega el contenido de esta carpeta en la raíz del microservicio existente**:
   - `.github/` *(Agentes `.agent.md` y comandos rápidos `.prompt.md` — detectados automáticamente por VS Code)*
   - `skills/` *(Los 11 Skills técnicos y funcionales agrupados por rol)*
   - `docs/sdlc/templates/` *(Plantilla oficial del Acta de Revisión)*
   - `AGENTS.md` *(Reglas globales del equipo y principios bancarios)*
   - `.vscode/settings.json` *(Si el microservicio ya tiene un `.vscode/settings.json`, fusiona únicamente las 4 claves `chat.*` de este archivo).*
2. Abre la carpeta del microservicio en **Visual Studio Code** y abre el panel de **Chat** en modo **Agent**.

---

## Cómo Ejecutar el Flujo Completo sobre un Microservicio Existente

### Paso 1 — Revisión Individual + Generación del Acta (Sin tocar código)
En el Chat de VS Code escribe:
```text
/auditar-microservicio
```
*(O selecciona el agente **`sdlc-orchestrator`** en el selector del chat y pídele auditar el servicio).*

- Los 5 agentes (`product-owner`, `tech-lead`, `backend-developer`, `qa-engineer`, `security-specialist`) revisan el microservicio bajo sus criterios individuales y de equipo.
- Se genera **`docs/sdlc/ACTA-REVISION.md`** con observaciones trazables (`Archivo:Línea`), acuerdos del equipo y el Plan de Trabajo numerado (`T-01`, `T-02`, ...).
- El orquestador **se detiene automáticamente** para que revises, ajustes o respondas cualquier `[PREGUNTA ABIERTA]` en el Acta.

### Paso 2 — Ejecución Conjunta del Plan Aprobado
Cuando estés conforme con `docs/sdlc/ACTA-REVISION.md`, haz clic en el botón **"Aprobar Acta y Ejecutar Plan"** en el chat o escribe:
```text
/ejecutar-acta
```
- El orquestador coordina tarea por tarea: definición/contrato (`product-owner` + `tech-lead`) → código Quarkus/Java 21 y `./mvnw test` (`backend-developer`) → pruebas de componentes y mocks en Karate (`qa-engineer`) → auditoría de cierre (`security-specialist` + `tech-lead`), actualizando el estado en `docs/sdlc/ACTA-REVISION.md`.
