## Respuesta (baseline, sin skill)

Claro, aquí tienes un escenario **general** de disponibilidad (no ligado todavía a un sistema concreto), siguiendo el template de 6 partes propuesto por el SEI (Bass, Clements & Kazman, *Software Architecture in Practice*):

---

### Escenario general de disponibilidad

1. **Fuente de estímulo (Source of stimulus)**
   Puede provenir de fuentes internas o externas al sistema:
   - Un componente de hardware (servidor, disco, red, sensor).
   - Un componente de software (proceso, servicio, hilo, base de datos).
   - Personas (usuarios, operadores, administradores) que cometen errores.
   - Entidades externas (servicios de terceros, dependencias externas).
   - El propio entorno físico (corte de energía, desastre natural).

2. **Estímulo (Stimulus)**
   Una falla (*fault*) que se manifiesta como:
   - Una caída (*crash*) del componente.
   - Una omisión (el componente no responde a una solicitud).
   - Un comportamiento incorrecto (*incorrect behavior* / *timing failure*, respuesta fuera de tiempo).
   - Pérdida de conectividad de red.
   - Sobrecarga que impide procesar solicitudes.

3. **Artefacto (Artifact)**
   El recurso o parte del sistema afectada por la falla:
   - Un servicio, proceso o componente específico.
   - El sistema completo.
   - Un conjunto de datos o recurso compartido (por ejemplo, la base de datos).
   - Un enlace de comunicación o canal de red.

4. **Entorno (Environment)**
   El estado del sistema en el momento en que ocurre el estímulo:
   - En operación normal.
   - En modo degradado (ya con otra falla activa, es decir, fallas concurrentes).
   - Durante el arranque, apagado o una actualización/mantenimiento.
   - Bajo carga pico o condiciones de sobrecarga.

5. **Respuesta (Response)**
   Lo que el sistema debe hacer ante la falla, típicamente mediante alguna táctica de disponibilidad:
   - Detectar la falla (heartbeat, ping/echo, monitoreo, timeout, excepción).
   - Registrar (loggear) el evento y notificar a los operadores/administradores.
   - Aislar la falla para evitar que se propague (fault containment).
   - Recuperarse automáticamente: conmutación a un componente redundante (failover), reintento de la operación, reinicio del componente, restauración desde un estado/checkpoint conocido.
   - Continuar operando en modo degradado (degradación controlada / *graceful degradation*) mientras se resuelve la falla.
   - Prevenir que la falla vuelva a ocurrir o escale (por ejemplo, mediante un patrón *circuit breaker*).

6. **Medida de respuesta (Response Measure)**
   Criterios cuantificables para verificar que la respuesta fue adecuada:
   - Tiempo de detección de la falla (por ejemplo, en milisegundos o segundos).
   - Tiempo de recuperación o restauración del servicio (MTTR).
   - Disponibilidad del sistema expresada como porcentaje de uptime (p. ej., 99.9%, 99.99%).
   - Tiempo medio entre fallas (MTTF) o tiempo medio entre fallas del sistema (MTBF).
   - Porcentaje de solicitudes atendidas correctamente durante el incidente (nivel de servicio mantenido).
   - Ausencia de pérdida de datos (o cantidad máxima tolerable de datos perdidos, RPO).
   - Notificación efectiva a las partes interesadas dentro de un tiempo determinado.

---

### Resumen en formato tabular

| Parte | Descripción genérica |
|---|---|
| **Fuente del estímulo** | Componente interno, externo, hardware, software o persona |
| **Estímulo** | Falla: caída, omisión, respuesta incorrecta o fuera de tiempo |
| **Artefacto** | Componente, servicio, dato o canal de comunicación afectado |
| **Entorno** | Operación normal, degradada, arranque/apagado o sobrecarga |
| **Respuesta** | Detectar, notificar, aislar, recuperar (failover/retry/reinicio) o degradar con control |
| **Medida de respuesta** | Tiempo de detección, tiempo de recuperación, % de disponibilidad, MTTF/MTBF, datos no perdidos |

Este esqueleto es intencionalmente genérico: cuando quieras aplicarlo a un sistema concreto, lo ideal es instanciar cada parte con valores específicos y medibles (por ejemplo, "la base de datos principal falla mientras el sistema opera bajo carga normal; el sistema debe detectar la falla en menos de 5 segundos y conmutar automáticamente a la réplica secundaria, manteniendo una disponibilidad anual del 99.95% sin pérdida de transacciones confirmadas").

Si quieres, puedo ayudarte a convertir este escenario general en uno **concreto**, aplicado a un sistema real que tengas en mente.

## Autocrítica (checklist SEI)

**Advertencia metodológica previa:** el pedido era "un escenario" (singular). La respuesta, en cambio, entrega para cada una de las 6 partes una **taxonomía/menú de opciones posibles** (p. ej. "puede ser hardware, software, personas, entorno físico...") en lugar de comprometerse con un único valor por parte. Un escenario general SEI real (como el clásico ejemplo del libro: "un servidor del clúster falla por hardware; el proceso de aplicación cae; el entorno es operación normal; el sistema notifica al operador; el sistema sigue operando sin downtime, en menos de 3 segundos") es una sola oración instanciada con un valor por parte, aunque ese valor sea genérico. Esta confusión de fondo (catálogo de posibilidades vs. escenario instanciado) afecta a las 6 partes y se detalla abajo.

1. **Fuente del estímulo — Presente pero no medible o vago.**
   El texto enumera categorías ("Un componente de hardware... Personas... Entidades externas... El propio entorno físico") pero nunca fija una fuente concreta para el escenario; es un listado de tipos válidos según la taxonomía del SEI, no una fuente instanciada.

2. **Estímulo — Presente pero no medible o vago.**
   Igual que el punto anterior, se listan tipos de falla ("Una caída... Una omisión... Un comportamiento incorrecto... Pérdida de conectividad... Sobrecarga...") sin seleccionar cuál de ellos es el estímulo del escenario que se está describiendo.

3. **Artefacto — Presente pero no medible o vago.**
   Se menciona "Un servicio, proceso o componente específico. El sistema completo. Un conjunto de datos... Un enlace de comunicación" como opciones intercambiables, sin identificar el artefacto puntual afectado por el estímulo elegido.

4. **Ambiente — Presente pero no medible o vago.**
   Se listan estados posibles ("En operación normal... En modo degradado... Durante el arranque, apagado o... Bajo carga pico...") pero no se fija cuál de esos estados es el del escenario, dejando el ambiente indeterminado.

5. **Respuesta — Presente pero no medible o vago.**
   Se describe un catálogo de tácticas de disponibilidad ("Detectar... Registrar... Aislar... Recuperarse automáticamente (failover, reintento, reinicio)... Continuar en modo degradado... Prevenir mediante circuit breaker") sin narrar la secuencia de actividad concreta que el sistema ejecutaría ante el estímulo específico del escenario.

6. **Medida de respuesta — Presente pero no medible o vago.**
   Aunque nombra las métricas correctas (tiempo de detección, MTTR, % de uptime, MTTF/MTBF, RPO), no fija ningún umbral objetivo dentro de la sección formal del template (dice "por ejemplo, en milisegundos o segundos" o "p. ej., 99.9%, 99.99%" como formato ilustrativo, no como el valor exigido); los únicos números concretos ("menos de 5 segundos", "99.95%") aparecen recién en el párrafo de ejemplo fuera del template de 6 partes, es decir, no forman parte de la medida de respuesta entregada.

### Resumen

El fallo dominante y transversal a las 6 partes es que la respuesta confunde "escenario general" con "taxonomía de posibles valores por categoría": en vez de instanciar una sola oración con un valor concreto (aunque genérico) para cada una de las 6 partes, entrega un menú de opciones por parte, lo cual deja el escenario sin cerrar y a la medida de respuesta sin ningún umbral verificable dentro del template formal. Esto es exactamente lo que una skill correctiva debería forzar: exigir que cada una de las 6 partes se resuelva en un valor único e instanciado, y que la medida de respuesta siempre incluya un número o umbral concreto (no solo el nombre de la métrica).
