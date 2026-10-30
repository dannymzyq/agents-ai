# Plantilla de Historia de Usuario y Gherkin Bancario

## 1. Historia de Usuario
- **ID / Capacidad**: `HU-XXX — <Nombre corto de la operación>`
- **Endpoint OpenAPI Asociado (Inmutable)**: `<MÉTODO /ruta/definida/en/openapi>`
- **Como** `<canal o microservicio consumidor>`
- **Quiero** `<ejecutar operación financiera>`
- **Para** `<valor de negocio verificable>`

## 2. Reglas Financieras y Operativas
| Regla | Definición Exacta |
| :--- | :--- |
| Moneda (`ISO-4217`) y Escala | Ej. `PEN` / `USD`, 2 decimales fijos, redondeo `HALF_EVEN` |
| Límites por Operación | Mínimo `"1.00"`, Máximo `"10000.00"` |
| Validación de Sesión y Token | Sesión activa y `access token` válido recibido y propagado entre microservicios |
| Estados Válidos | Cuenta origen y destino en estado `ACTIVE` |
| Fuera de Alcance | `<Explícito qué no cubre esta historia (sin alterar OpenAPI)>` |

## 3. Criterios de Aceptación (Gherkin Karate-Ready)

```gherkin
Feature: Transferencia entre cuentas propias

  @happy-path @smoke
  Scenario: Transferencia exitosa con sesión activa y saldo suficiente
    Given una sesión activa con access token válido para el cliente titular
    And una cuenta origen "ACTIVE" con saldo disponible "1500.00" en "USD"
    And una cuenta destino "ACTIVE" en "USD"
    When el cliente solicita transferir "250.50" "USD"
    Then la operación responde exitosamente según el contrato OpenAPI
    And el nuevo saldo disponible de la cuenta origen es "1249.50" "USD"

  @business-rejection
  Scenario Outline: Rechazo funcional por regla de negocio (<motivo>)
    Given una sesión activa con access token válido para el cliente titular
    And una cuenta origen en estado "<estadoOrigen>" con saldo "<saldo>" en "USD"
    When el cliente solicita transferir "<monto>" "USD"
    Then la operación es rechazada con el código de negocio "<codigoError>" definido en el contrato

    Examples:
      | motivo               | estadoOrigen | saldo     | monto     | codigoError          |
      | fondos insuficientes | ACTIVE       | 100.00    | 100.01    | INSUFFICIENT_FUNDS   |
      | cuenta bloqueada     | BLOCKED      | 1500.00   | 50.00     | ACCOUNT_BLOCKED      |
      | límite superado      | ACTIVE       | 20000.00  | 10000.01  | LIMIT_EXCEEDED       |

  @session-auth
  Scenario: Rechazo cuando la sesión o el access token propagado no es válido
    Given una solicitud con sesión inactiva o access token expirado
    When se invoca la operación de transferencia por "100.00" "USD"
    Then el microservicio rechaza la solicitud como no autorizada sin ejecutar afectación contable
```
