## Prompt

Revisá si este escenario está completo: "El servidor de base de datos falla y el sistema debe recuperarse automáticamente."

## Criterios de aceptación esperados

- Ambiente debe marcarse ❌ (no se dice bajo qué circunstancias ocurre: operación normal, pico, mantenimiento, etc.).
- Fuente de estímulo debe marcarse ⚠️ o ❌ (no se especifica el tipo de falla ni su origen).
- Medida de respuesta debe marcarse ❌ (no hay tiempo de recuperación ni tasa de éxito).
- Debe proponer valores concretos para completar cada hueco.

## Respuesta obtenida

Reviso el escenario contra el template de 6 partes del SEI. El texto es: "El servidor de base de datos falla y el sistema debe recuperarse automáticamente."

| Parte | Estado | Justificación |
|---|---|---|
| Fuente de estímulo | ⚠️ Ambiguo | Se dice que "el servidor de base de datos falla", pero no se especifica el origen/tipo de la falla: ¿es un crash del proceso del motor de BD, una falla de hardware (disco, memoria), una caída del sistema operativo, una interrupción de red que aísla al servidor, o un corte de energía? Esto importa en disponibilidad porque la estrategia de recuperación esperada (failover a réplica, reinicio de proceso, restauración desde backup) cambia según cuál sea la fuente real. |
| Estímulo | ⚠️ Ambiguo | "Falla" está descripto como evento disparador (no está confundido con la respuesta, eso está bien), pero es genérico: no distingue si es un crash total, una falla de omisión (no responde pero el proceso sigue vivo), una degradación de timing, o corrupción de datos. Cada variante dispara una respuesta distinta. |
| Ambiente | ❌ Ausente | No se indica bajo qué circunstancias ocurre la falla: ¿en operación normal, en horario pico, durante una ventana de mantenimiento, con el sistema en modo degradado por otro incidente previo? No mencionarlo se asume como ausente. |
| Artefacto | ✅ Presente | "El servidor de base de datos" es un artefacto específico y no un "el sistema" genérico. Podría afinarse aún más (ej. indicar si es el nodo primario en un esquema primario-réplica), pero como está, identifica claramente qué componente es estimulado. |
| Respuesta | ⚠️ Ambiguo | "Debe recuperarse automáticamente" expresa una intención, no un comportamiento concreto. No dice si implica failover automático a una réplica, reinicio del proceso, restauración desde backup, redirección de conexiones de clientes, o alguna combinación, ni qué hace el sistema mientras tanto (rechazar requests, encolarlas, mostrar error). |
| Medida de respuesta | ❌ Ausente | No hay tiempo de recuperación, tasa de éxito, ni objetivo de pérdida de datos (RPO). "Recuperarse automáticamente" no es verificable sin un número. |

No se detecta mezcla de dos atributos de calidad distintos en la misma oración: el escenario es puramente de disponibilidad.

### Propuesta de redacción para cada hueco

- **Fuente de estímulo**: "El proceso del motor de base de datos se cae de forma inesperada (crash) en el servidor primario, por una falla de software o de hardware interno del servidor (no por un ataque externo ni por una partición de red)."
- **Estímulo**: "El servidor deja de responder a los health-checks (proceso caído o sistema operativo no disponible)."
- **Ambiente**: "En operación normal de producción, fuera de ventana de mantenimiento programado, con el sistema atendiendo tráfico de usuarios."
- **Respuesta**: "El sistema detecta la falla mediante health-checks periódicos, promueve automáticamente una réplica secundaria a rol primario (failover), redirige las conexiones de las aplicaciones cliente hacia la nueva instancia primaria, y notifica al equipo de operaciones del incidente."
- **Medida de respuesta**: "El failover se completa en menos de 30 segundos desde la detección de la falla, con una tasa de éxito de recuperación automática ≥ 99% de los incidentes, y con pérdida de transacciones confirmadas nula o RPO ≤ 5 segundos."

### Escenario reescrito (propuesta, a revisar por el usuario)

"**[agregado]** El proceso del motor de base de datos se cae de forma inesperada (crash) en el servidor primario, por una falla de software o de hardware interno del servidor. **[original]** El servidor de base de datos falla **[agregado]** — el servidor deja de responder a los health-checks — **[original]** y el sistema debe recuperarse automáticamente **[agregado, reemplazando "recuperarse automáticamente" por comportamiento concreto]**: detecta la falla mediante health-checks periódicos, promueve automáticamente una réplica secundaria a rol primario (failover), redirige las conexiones de las aplicaciones cliente hacia la nueva instancia primaria, y notifica al equipo de operaciones. **[agregado]** Esto ocurre en operación normal de producción, fuera de ventana de mantenimiento, y el failover debe completarse en menos de 30 segundos desde la detección de la falla, con una tasa de éxito ≥ 99% y RPO ≤ 5 segundos (o sin pérdida de transacciones confirmadas)."

Las partes marcadas como "[agregado]" son propuestas mías para completar el escenario, no están confirmadas por el usuario — deberían validarse contra los objetivos reales de negocio (ej. si 30 segundos y 99% son realistas para la infraestructura disponible).

## Veredicto

- Ambiente debe marcarse ❌: ✅ Cumplido — se marcó ❌ ausente, con justificación de que no se especifica operación normal/pico/mantenimiento.
- Fuente de estímulo debe marcarse ⚠️ o ❌: ✅ Cumplido — se marcó ⚠️ ambiguo, señalando que no se especifica el tipo de falla ni su origen.
- Medida de respuesta debe marcarse ❌: ✅ Cumplido — se marcó ❌ ausente, indicando que falta tiempo de recuperación y tasa de éxito (y RPO).
- Debe proponer valores concretos para completar cada hueco: ✅ Cumplido — se propusieron valores concretos (30 segundos, ≥99%, RPO ≤5s, ventana de mantenimiento, tipo de falla, comportamiento de failover) para Fuente, Estímulo, Ambiente, Respuesta y Medida de respuesta.

**Resultado: PASA**
