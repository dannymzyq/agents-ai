# Plantilla de Historia de Usuario y Gherkin Bancario

## 1. Historia de Usuario
- **ID / Capacidad**: `HU-XXX — <Nombre corto de la operación>`
- **Como** `<canal o sistema consumidor>`
- **Quiero** `<ejecutar operación financiera>`
- **Para** `<valor de negocio verificable>`

## 2. Reglas Financieras y Operativas
| Regla | Definición Exacta |
| :--- | :--- |
| Moneda (`ISO-4217`) y Escala | Ej. `PEN` / `USD`, 2 decimales fijos, redondeo `HALF_EVEN` |
| Límites por Operación | Mínimo `"1.00"`, Máximo `"10000.00"` |
| Idempotencia | Obligatoria (`Idempotency-Key`, vigencia 24h; payload distinto = conflicto) |
| Estados Válidos | Cuenta origen y destino en estado `ACTIVE` |
| Fuera de Alcance | `<Explícito qué no cubre esta historia>` |

## 3. Criterios de Aceptación (Gherkin Karate-Ready)

```gherkin
Feature: Transferencia entre cuentas propias

  @happy-path @smoke
  Scenario: Transferencia exitosa con saldo suficiente
    Given una cuenta origen "ACTIVA" con saldo disponible "1500.00" en "USD"
    And una cuenta destino "ACTIVA" en "USD"
    When el cliente solicita transferir "250.50" "USD" con una clave de idempotencia única
    Then la operación es aceptada con estado exitoso y código de referencia único
    And el nuevo saldo disponible de la cuenta origen es "1249.50" "USD"

  @business-rejection
  Scenario Outline: Rechazo funcional por regla de negocio (<motivo>)
    Given una cuenta origen en estado "<estadoOrigen>" con saldo "<saldo>" en "USD"
    When el cliente solicita transferir "<monto>" "USD"
    Then la operación es rechazada con código de negocio "<codigoError>"

    Examples:
      | motivo               | estadoOrigen | saldo     | monto     | codigoError          |
      | fondos insuficientes | ACTIVE       | 100.00    | 100.01    | INSUFFICIENT_FUNDS   |
      | cuenta bloqueada     | BLOCKED      | 1500.00   | 50.00     | ACCOUNT_BLOCKED      |
      | límite superado      | ACTIVE       | 20000.00  | 10000.01  | LIMIT_EXCEEDED       |

  @idempotency
  Scenario: Reintento idéntico no duplica el débito y payload alterado es rechazado
    Given una transferencia procesada exitosamente con clave "IDEMP-001" por "100.00" "USD"
    When se reintenta la solicitud con clave "IDEMP-001" y el mismo monto "100.00" "USD"
    Then se retorna exactamente el mismo resultado original sin aplicar segundo cargo
    When se envía la clave "IDEMP-001" alterando el monto a "200.00" "USD"
    Then la solicitud es rechazada por conflicto de idempotencia ("IDEMPOTENCY_CONFLICT")
```
