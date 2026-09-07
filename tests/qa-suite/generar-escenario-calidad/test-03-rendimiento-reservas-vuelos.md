## Prompt

Necesito un escenario concreto de rendimiento para un sistema de reservas de vuelos, durante una liquidación de pasajes (Cyber Monday de aerolíneas).

## Criterios de aceptación esperados

- Ambiente = carga pico explícita (el evento de alta demanda).
- Medida de respuesta con número concreto (latencia p95, throughput, tiempo de respuesta, o tasa de error bajo carga).
- El Estímulo no está confundido con la Respuesta (el estímulo es la llegada de solicitudes/carga, no "el sistema responde rápido").

## Respuesta obtenida

Escenario concreto de **rendimiento** para el sistema de reservas de vuelos durante una liquidación de pasajes (Cyber Monday de aerolíneas):

| Parte | Valor |
|---|---|
| Fuente de estímulo | Usuarios finales (pasajeros) accediendo simultáneamente desde la web y la app móvil de la aerolínea |
| Estímulo | Llegada masiva y concurrente de solicitudes de búsqueda de vuelos y de creación de reservas (ráfaga de tráfico muy por encima de lo habitual) |
| Ambiente | Carga pico explícita: ventana de liquidación tipo "Cyber Monday de aerolíneas", con un pico estimado de [A DEFINIR: factor de multiplicación exacto sobre el tráfico normal, ej. 15x-20x] solicitudes concurrentes respecto a un día normal (supuesto, a validar con el cliente) |
| Artefacto | El motor de búsqueda de disponibilidad de vuelos y el servicio de creación/confirmación de reservas (booking engine), incluyendo su API de tarifas |
| Respuesta | El sistema atiende las búsquedas y reservas escalando horizontalmente los servicios afectados (auto-scaling) y aplicando control de carga (colas, rate limiting) para evitar caídas, sin perder ni duplicar reservas |
| Medida de respuesta | Latencia de búsqueda de vuelos p95 < 2 s; confirmación de reserva p95 < 3 s; throughput sostenido ≥ 500 reservas/minuto; tasa de error (5xx/timeouts) bajo carga < 1% (valores supuestos como punto de partida, a ajustar con el cliente) |

**Prosa:** "Cuando durante la liquidación de pasajes (Cyber Monday de aerolíneas) llega una ráfaga masiva y concurrente de solicitudes de búsqueda y reserva de vuelos muy por encima del tráfico normal, el motor de búsqueda y el servicio de reservas deben escalar horizontalmente y aplicar control de carga para responder con una latencia p95 menor a 2 segundos en búsquedas y 3 segundos en confirmaciones de reserva, sosteniendo un throughput de al menos 500 reservas/minuto y una tasa de error bajo carga inferior al 1%."

Nota: el factor exacto de pico de tráfico no fue provisto por el usuario, por lo que quedó marcado como `[A DEFINIR]`; los valores numéricos de la medida de respuesta (latencias, throughput, tasa de error) se proponen como supuestos iniciales razonables para este tipo de sistema y deberían validarse con el cliente.

## Veredicto

- Ambiente = carga pico explícita (el evento de alta demanda): ✅ La fila "Ambiente" nombra explícitamente la ventana de liquidación tipo Cyber Monday como carga pico, con un multiplicador de tráfico indicado (aunque el valor exacto se marcó [A DEFINIR], la naturaleza de carga pico queda explícita y cerrada).
- Medida de respuesta con número concreto (latencia p95, throughput, tiempo de respuesta, o tasa de error bajo carga): ✅ La fila "Medida de respuesta" incluye latencia p95 (2 s y 3 s), throughput (≥500 reservas/minuto) y tasa de error (<1%), todos numéricos.
- El Estímulo no está confundido con la Respuesta (el estímulo es la llegada de solicitudes/carga, no "el sistema responde rápido"): ✅ El "Estímulo" describe la llegada de la ráfaga de solicitudes de búsqueda/reserva, mientras que el escalado, control de carga y no pérdida de reservas se ubican correctamente en "Respuesta".

**Resultado: PASA**
