## Prompt

Generá un escenario concreto de disponibilidad para la app móvil de un banco, enfocado en el momento de pago de sueldos (fin de mes), cuando la carga de transacciones se dispara.

## Criterios de aceptación esperados

- Tabla de 6 filas cerrada, con un valor concreto por celda (no un listado de opciones sin resolver).
- El Ambiente menciona explícitamente el pico de fin de mes / pago de sueldos.
- La Medida de respuesta incluye al menos un número (tiempo de detección/recuperación, % de disponibilidad, MTTR, etc.).
- Hay una versión en prosa coherente con la tabla.

## Respuesta obtenida

**Escenario concreto** (específico de la app móvil del banco, no una plantilla general).

| Parte | Valor |
|---|---|
| Fuente de estímulo | El conjunto de clientes del banco que reciben su sueldo y acceden simultáneamente a la app móvil |
| Estímulo | Pico súbito de volumen de transacciones (consultas de saldo, transferencias, pagos de servicios) que supera la capacidad normal del backend y provoca saturación, degradación de tiempos de respuesta o caída de algún nodo |
| Ambiente | Ventana de pago de sueldos de fin de mes (últimos 2 días hábiles del mes, franja horaria 8:00–20:00), con carga transaccional hasta 10 veces el promedio diario habitual |
| Artefacto | El servicio backend de procesamiento de transacciones (API de transferencias y consulta de saldo) que soporta la app móvil |
| Respuesta | El sistema escala automáticamente los recursos (auto-scaling) para absorber la demanda, encola sin pérdida las solicitudes que exceden la capacidad instantánea y, si un nodo falla, detecta la falla y conmuta a una instancia redundante sin pérdida de transacciones |
| Medida de respuesta | Disponibilidad ≥ 99.95% durante toda la ventana de pico; tiempo de detección de falla < 30 s; tiempo de recuperación (MTTR) < 2 min; 0% de transacciones perdidas o duplicadas |

**Prosa:** "Cuando el pico de fin de mes por pago de sueldos genera hasta 10 veces la carga transaccional habitual sobre la app móvil del banco, el backend de procesamiento de transacciones debe escalar automáticamente y, ante la falla de un nodo, detectarla en menos de 30 segundos y recuperarse (failover) en menos de 2 minutos sin perder transacciones, manteniendo una disponibilidad igual o superior al 99.95% durante toda la ventana de pico."

## Veredicto

- ✅ Tabla de 6 filas cerrada, con un valor concreto por celda: las 6 partes (fuente de estímulo, estímulo, ambiente, artefacto, respuesta, medida de respuesta) tienen un valor específico y cerrado, sin listas de opciones sin resolver.
- ✅ El Ambiente menciona explícitamente el pico de fin de mes / pago de sueldos: la fila de Ambiente dice literalmente "Ventana de pago de sueldos de fin de mes (últimos 2 días hábiles del mes...)".
- ✅ La Medida de respuesta incluye al menos un número: incluye 99.95%, < 30 s, < 2 min y 0%.
- ✅ Hay una versión en prosa coherente con la tabla: la prosa retoma los mismos valores (10x carga, auto-scaling, <30s detección, <2min MTTR, 99.95% disponibilidad) sin contradecir la tabla.

**Resultado: PASA**
