## Respuesta (con skill)

Reviso el escenario contra el template de 6 partes del SEI (fuente de estímulo, estímulo, ambiente, artefacto, respuesta, medida de respuesta).

**Escenario original:** "Cuando un cliente reporta un error crítico, el sistema debe registrar el ticket y notificar al equipo de soporte."

### 1. Tabla de completitud

| Parte | Estado | Comentario |
|---|---|---|
| Fuente de estímulo | ✅ | "un cliente" — el origen está identificado. No es un escenario de seguridad donde la confiabilidad de la fuente cambie la respuesta esperada, así que con esto alcanza. |
| Estímulo | ✅ | "reporta un error crítico" — la condición disparadora está clara y no se confunde con la respuesta. (Queda implícito qué hace que un error sea "crítico"; si el equipo ya tiene una clasificación de severidad definida en otro lado, no hace falta repetirla acá.) |
| Ambiente | ❌ | No se dice bajo qué circunstancias ocurre: ¿en cualquier momento (24/7) o solo en horario de atención? ¿en operación normal o también cuando entran muchos reportes críticos a la vez (ej. una caída masiva)? Al no mencionarse nada, se asume que falta. |
| Artefacto | ⚠️ | "el sistema" es genérico. El escenario describe un flujo puntual (alta de ticket + notificación), así que conviene nombrar el/los componente(s) real(es) involucrados en vez de dejar "el sistema". |
| Respuesta | ⚠️ | Se nombran las dos acciones esperadas ("registrar el ticket" y "notificar al equipo de soporte"), pero en términos de intención, no de comportamiento verificable: no dice qué datos lleva el ticket, cómo se asigna, por qué canal se notifica, ni qué pasa si nadie confirma la notificación (reintento o escalamiento). |
| Medida de respuesta | ❌ | No hay ningún número ni criterio verificable: no se especifica en cuánto tiempo debe quedar registrado el ticket, en cuánto tiempo debe llegar la notificación, ni con qué tasa de éxito. |

### 2. Propuestas concretas para lo que falta o es ambiguo

- **Ambiente:** "en cualquier momento, las 24 horas los 7 días de la semana, incluso fuera del horario laboral del equipo de soporte, tanto en operación normal como durante picos de reportes simultáneos (ej. una caída masiva que genera muchos tickets críticos a la vez)".
- **Artefacto:** en vez de "el sistema", especificar "el módulo de gestión de tickets (mesa de ayuda)" para el registro, y "el gateway/servicio de notificaciones" para el aviso al equipo.
- **Respuesta:** "el sistema registra el ticket con severidad 'crítica', lo asigna automáticamente al ingeniero de guardia según el esquema de turnos activo, y envía una notificación al equipo de soporte por [canal a definir, ej. Slack + email/SMS]; si el responsable asignado no confirma (ack) la notificación, el sistema escala automáticamente a un segundo responsable".
- **Medida de respuesta:** "el ticket queda registrado en menos de 10 segundos desde que se recibe el reporte; la notificación llega al equipo de soporte dentro de los 5 minutos, con una tasa de entrega ≥ 99%; si no hay ack dentro de los 15 minutos, se dispara el escalamiento automático".

### 3. Escenario reescrito (con lo agregado marcado)

Lo que está en **negrita** es lo que yo propuse agregar — no lo tomes como definitivo, revisalo y ajustalo con tu equipo antes de incorporarlo:

> Cuando un cliente reporta un error crítico, **en cualquier momento (24/7), incluso fuera del horario laboral del equipo de soporte y tanto en operación normal como durante picos de reportes simultáneos**, **el módulo de gestión de tickets** debe registrar el ticket **con severidad "crítica" y asignarlo automáticamente al ingeniero de guardia según el esquema de turnos activo**, y **el gateway de notificaciones** debe notificar al equipo de soporte **por [canal a definir, ej. Slack + email/SMS], escalando a un segundo responsable si no hay confirmación**, de modo que **el ticket quede registrado en menos de 10 segundos y la notificación llegue dentro de los 5 minutos con una tasa de entrega ≥ 99% (con escalamiento si no hay ack en 15 minutos)**.

### 4. Mezcla de atributos de calidad

No detecto una mezcla problemática de dos atributos distintos en la misma oración: todo el escenario describe un único flujo de respuesta a incidentes (registro + notificación), donde el tiempo y la tasa de entrega son la medida natural de ese flujo. No hace falta separarlo en dos escenarios.

**Conclusión:** el escenario tal como está **no está completo**. Fuente de estímulo y estímulo están bien. Ambiente y medida de respuesta están ausentes (❌), y artefacto y respuesta están presentes pero demasiado genéricos/incompletos (⚠️) — sobre todo falta el plazo y la tasa de éxito de la notificación, que es lo más crítico para poder verificar este escenario en el futuro.
