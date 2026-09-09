## Prompt

Escenario concreto de interoperabilidad: un e-commerce debe integrar una nueva pasarela de pago externa.

## Criterios de aceptación esperados

- Fuente de estímulo / Estímulo dejan explícito que se trata de integrar un sistema externo (la pasarela) o su protocolo.
- Medida de respuesta cuantifica % de transacciones procesadas correctamente, tiempo de integración, o incidentes de incompatibilidad.
- No se confunde con un escenario de seguridad ni de rendimiento puro.

## Respuesta obtenida

Escenario concreto de **interoperabilidad**: integración de una nueva pasarela de pago externa en un sistema de e-commerce.

| Parte | Valor |
|---|---|
| Fuente de estímulo | La pasarela de pago externa (proveedor de pagos, ej. Stripe/MercadoPago/similar) |
| Estímulo | Solicitud de intercambiar mensajes de pago (autorización, captura y notificación vía webhook) siguiendo el protocolo y formato de API de la pasarela |
| Ambiente | Integración en tiempo de diseño/desarrollo, durante la implementación del módulo de checkout, previo al pase a producción |
| Artefacto | El módulo de checkout del e-commerce (adaptador/conector de pagos que expone e integra la interfaz hacia la pasarela) |
| Respuesta | El sistema de e-commerce intercambia correctamente los mensajes con la API de la pasarela (formato JSON/REST, autenticación, recepción y validación de webhooks de confirmación) según el protocolo acordado, sin requerir modificaciones en el core del sistema |
| Medida de respuesta | ≥ 99% de las transacciones de prueba procesadas correctamente en el ambiente de integración (sandbox); tiempo total de integración ≤ 3 semanas-persona; ≤ 5 incidentes de incompatibilidad de formato/protocolo detectados durante la certificación con la pasarela |

Prosa: "Cuando la nueva pasarela de pago externa solicita intercambiar mensajes de autorización, captura y confirmación (webhooks) durante la integración del módulo de checkout, el e-commerce debe procesar correctamente al menos el 99% de las transacciones de prueba en el ambiente sandbox, completar la integración en un máximo de 3 semanas-persona y registrar no más de 5 incidentes de incompatibilidad de formato/protocolo durante la certificación con el proveedor."

## Veredicto

- ✅ Fuente de estímulo / Estímulo dejan explícito que se trata de integrar un sistema externo (la pasarela) o su protocolo — la fuente es "la pasarela de pago externa" y el estímulo nombra explícitamente el intercambio de mensajes según "el protocolo y formato de API de la pasarela".
- ✅ Medida de respuesta cuantifica % de transacciones procesadas correctamente, tiempo de integración, o incidentes de incompatibilidad — incluye las tres: ≥99% de transacciones correctas, ≤3 semanas-persona de integración, ≤5 incidentes de incompatibilidad.
- ✅ No se confunde con un escenario de seguridad ni de rendimiento puro — el estímulo y la respuesta giran sobre acuerdo de protocolo/formato e intercambio correcto de datos entre sistemas (interoperabilidad), no sobre ataques/autenticación como fin en sí mismo (seguridad) ni sobre carga/latencia bajo volumen (rendimiento); la autenticación y el JSON/REST aparecen solo como parte del protocolo a respetar, no como el foco de la respuesta.

**Resultado: PASA**
