## Respuesta (con skill)

Este es un **escenario general de disponibilidad** (independiente de un sistema concreto): sirve como plantilla reutilizable que luego se instancia para un sistema específico ajustando ambiente, artefacto y umbrales numéricos.

| Parte | Valor |
|---|---|
| Fuente de estímulo | Un componente interno del sistema (proceso, hardware o software) u otro sistema externo del que depende |
| Estímulo | Falla por omisión, crash, timing incorrecto o valor de respuesta incorrecto |
| Ambiente | Operación normal (la misma estructura aplica, ajustando umbrales, a arranque o modo degradado) |
| Artefacto | El componente, conector o el sistema completo afectado por la falla |
| Respuesta | El sistema detecta la falla, la registra, notifica al operador/administrador y se recupera mediante redundancia, reintento automático o failover a un nodo/componente alternativo |
| Medida de respuesta | Tiempo de detección de la falla < 10 s; tiempo de reparación/recuperación (MTTR) < 5 min; disponibilidad ≥ 99.9% medida en ventana mensual |

**Prosa:** Cuando un componente del sistema (interno o un sistema externo del que depende) sufre una falla por omisión, crash, timing incorrecto o valor de respuesta incorrecto durante la operación normal, el sistema debe detectar la falla, registrarla, notificar al operador y recuperarse mediante redundancia o failover, logrando un tiempo de detección menor a 10 segundos, un MTTR menor a 5 minutos y una disponibilidad mensual igual o superior al 99.9%.

Nota: al instanciar este escenario para un sistema concreto, reemplazá "componente interno" y "el sistema completo" por el subsistema real (ej. base de datos, servicio de autenticación, feed de datos) y ajustá los umbrales numéricos según el SLA/criticidad del caso real.
