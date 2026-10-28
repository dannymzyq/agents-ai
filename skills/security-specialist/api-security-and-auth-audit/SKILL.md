---
name: api-security-and-auth-audit
description: >-
  Usa este skill cuando el Especialista en Seguridad audite endpoints REST,
  autenticación/autorización y riesgos OWASP API Top 10 en microservicios Quarkus.
---

# Auditoría de Seguridad de APIs y Riesgos Transaccionales

Procedimiento para detectar vulnerabilidades de acceso, suplantación y abuso financiero en microservicios Quarkus.

## Checklist de Auditoría

1. **Autenticación y Autorización Estricta (SmallRye JWT / OIDC)**:
   - Confirmar política *deny-by-default* (`quarkus.security.deny-unannotated-members=true` o anotación explícita en cada recurso) y validación de `mp.jwt.verify.issuer`, `mp.jwt.verify.audiences`, expiración y algoritmo asimétrico (`RS256`/`ES256`, nunca `none`).
   - **Prevención BOLA / Mass Assignment**: Verificar que la titularidad (`customerId` / `subject`) se obtenga de `SecurityIdentity` / `JsonWebToken` y se cruce contra la cuenta operada; prohibir confiar en un `customerId` enviado libremente en el body o query.
2. **Condiciones de Carrera Financieras (Double-Spend / TOCTOU)**:
   - Auditar operaciones de débito, transferencia o reverso asegurando que la verificación de saldo + afectación ocurra bajo bloqueo transaccional en BD (`@Version` o `PESSIMISTIC_WRITE`) y que `Idempotency-Key` tenga restricción `UNIQUE` atómica.
3. **Inyección y Superficie de Ataque**:
   - Confirmar consultas parametrizadas en Panache/JPA, límites de payload (`quarkus.http.limits.max-body-size`), sanitización de cabeceras reflejadas en logs (`X-Correlation-Id` sin `\r`/`\n` para evitar Log Injection) y bloqueo de `/q/dev*` y Swagger UI abierto en `%prod`.

## Formato Obligatorio de Dictamen

- **Dictamen**: `APROBADO` | `OBSERVADO` | `BLOQUEADO` *(Crítica o Alta bloquea pase)*.
- **Hallazgos**: `[CRÍTICA | ALTA | MEDIA | BAJA] (OWASP/CWE) Archivo:Línea — Vector de ataque/brecha → Remediación exacta en Quarkus`.
