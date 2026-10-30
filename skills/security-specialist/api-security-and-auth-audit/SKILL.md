---
name: api-security-and-auth-audit
description: >-
  Usa este skill cuando el Especialista en Seguridad audite endpoints REST, validación y
  propagación de sesiones y access tokens, y riesgos OWASP API Top 10 en microservicios Quarkus.
---

# Auditoría de Seguridad de APIs, Sesiones y Access Tokens

Procedimiento para detectar vulnerabilidades de sesión/autenticación, suplantación y abuso financiero en microservicios Quarkus respetando su contrato OpenAPI inmutable.

## Checklist de Auditoría

1. **Validación y Propagación Segura de Sesiones y Access Tokens**:
   - Confirmar política *deny-by-default* (`quarkus.security.deny-unannotated-members=true` o anotación explícita en cada recurso) y validación estricta del `access token` entrante (`mp.jwt.verify.issuer`, `mp.jwt.verify.audiences`, expiración `exp`, algoritmo asimétrico `RS256`/`ES256`, nunca `none`) y del estado activo de la sesión asociada.
   - Auditar la **propagación hacia microservicios aguas abajo**: asegurar que el `access token` y contexto de sesión solo se reenvíen a clientes `@RegisterRestClient` internos de confianza bajo TLS, nunca hacia terceros externos no autorizados.
   - **Prevención BOLA / Mass Assignment**: Verificar que la identidad y sesión del titular (`subject` / `customerId`) se extraiga del `access token` / contexto de sesión validado y se cruce contra la cuenta operada; prohibir confiar en un `customerId` enviado libremente en el payload o query.
2. **Condiciones de Carrera Financieras (Double-Spend / TOCTOU)**:
   - Auditar operaciones de débito, transferencia o reverso asegurando que la verificación de saldo + afectación ocurra de forma atómica bajo bloqueo transaccional en BD (`@Version` o `PESSIMISTIC_WRITE` con orden determinista).
3. **Inyección y Superficie de Ataque**:
   - Confirmar consultas parametrizadas en Panache/JPA, límites de payload (`quarkus.http.limits.max-body-size`), sanitización de cabeceras reflejadas en logs (sin `\r`/`\n` para evitar Log Injection) y bloqueo de `/q/dev*` y Swagger UI abierto en `%prod`.

## Formato Obligatorio de Dictamen

- **Dictamen**: `APROBADO` | `OBSERVADO` | `BLOQUEADO` *(Crítica o Alta bloquea pase; prohibido alterar el contrato OpenAPI)*.
- **Hallazgos**: `[CRÍTICA | ALTA | MEDIA | BAJA] (OWASP/CWE) Archivo:Línea — Vector de ataque/brecha → Remediación interna exacta en Quarkus`.
