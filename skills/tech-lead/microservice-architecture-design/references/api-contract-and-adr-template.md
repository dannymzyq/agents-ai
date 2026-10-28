# Plantilla de Contrato API Bancario y Mini-ADR

## 1. Especificación de Endpoint
- **Método y Ruta**: `POST /api/v1/transfers`
- **Seguridad**: `Bearer JWT` (claims requeridos: `sub`, `iss`, `aud`, scope/rol autorizado)
- **Cabeceras Obligatorias**:
  - `X-Correlation-Id`: UUID (requerido o generado en borde y devuelto en respuesta)
  - `Idempotency-Key`: UUID / String hasta 64 chars (obligatorio en mutaciones)

## 2. Representación Monetaria y Errores (`RFC 9457 Problem Details`)

**Request / Response Monetario (sin float):**
```json
{
  "sourceAccountId": "acc-001",
  "targetAccountId": "acc-002",
  "amount": 250.50,
  "currency": "USD"
}
```
*(Nota: `BigDecimal` serializado en plano sin notación científica, validado con máximo 2 decimales).*

**Error Estándar (`application/problem+json`):**
```json
{
  "type": "https://api.banco.internal/errors/insufficient-funds",
  "title": "Fondos insuficientes",
  "status": 422,
  "detail": "La cuenta origen no cuenta con saldo disponible para cubrir la operación.",
  "code": "INSUFFICIENT_FUNDS",
  "correlationId": "f47ac10b-58cc-4372-a567-0e02b2c3d479"
}
```

## 3. Catálogo HTTP y Resiliencia Aguas Abajo
- **Respuestas**: `201 Created` (éxito), `200 OK` (replay idempotente), `400` (validación), `401`/`403` (auth/BOLA), `409` (conflicto de idempotencia o en vuelo), `422` (regla de negocio), `502`/`504` (fallo/timeout de dependencia).
- **Clientes Externos (`@RegisterRestClient`)**: `connect-timeout=1000ms`, `read-timeout=3000ms`, sin `@Retry` automático en mutaciones no idempotentes.

## 4. Mini-ADR (Registro de Decisión Arquitectónica)
- **Contexto**: `<Problema o restricción técnica/bancaria>`
- **Decisión**: `<Patrón o diseño elegido>`
- **Consecuencias / Impacto**: `<Trade-offs asumidos y estrategia de compatibilidad hacia atrás>`
