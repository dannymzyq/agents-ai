# Matriz de Conformidad OpenAPI (Inmutable) y Mini-ADR

## 1. Verificación contra el Contrato OpenAPI Vigente
> **Regla**: El archivo OpenAPI del microservicio NO se modifica. La implementación se alinea a él.

- **Archivo OpenAPI fuente**: `<ruta-en-el-repositorio>`
- **Operación (`method` + `path`)**: `<ej. POST /v1/... tal como figura en el OpenAPI>`
- **Cabeceras y Seguridad declaradas en OpenAPI**:
  - `Authorization` (`Bearer <access_token>`) y cabeceras de sesión/trazabilidad definidas en el contrato, propagadas y consumidas entre microservicios.
- **Esquemas Request / Response / Error**:
  - Validar correspondencia exacta de nombres de propiedades, obligatoriedad (`required`), formatos decimales y códigos de estado HTTP (`2xx`, `4xx`, `5xx`) declarados en el OpenAPI.

## 2. Propagación y Resiliencia Aguas Abajo (`@RegisterRestClient`)
- **Propagación**: Reenvío controlado de `access token`, atributos de sesión requeridos y cabecera de correlación mediante `ClientHeadersFactory`.
- **Timeouts y Protección**: `connect-timeout` y `read-timeout` explícitos; `CircuitBreaker`/`Bulkhead` según criticidad; prohibido `@Retry` automático en operaciones mutables.

## 3. Mini-ADR (Decisión Arquitectónica Interna)
- **Contexto**: `<Problema técnico o desviación detectada respecto al OpenAPI>`
- **Decisión**: `<Ajuste interno en Quarkus/Java 21 que respeta el OpenAPI sin alterarlo>`
- **Consecuencias / Impacto**: `<Impacto en componentes internos, BD y pruebas Karate>`
