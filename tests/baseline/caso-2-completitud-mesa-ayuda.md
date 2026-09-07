## Respuesta (baseline, sin skill)

No, el escenario está incompleto. Usando el método de escenarios de atributos de calidad de SEI (Bass, Clements y Kazman), un escenario bien formado debe tener seis partes: fuente del estímulo, estímulo, artefacto, ambiente, respuesta y medida de la respuesta. Veamos qué tiene y qué le falta:

**Lo que sí está presente:**
- **Fuente del estímulo:** "un cliente" (aunque de forma genérica).
- **Estímulo:** "reporta un error crítico".
- **Artefacto:** "el sistema" (de forma muy genérica, sin indicar qué componente o subsistema).
- **Respuesta:** "registrar el ticket y notificar al equipo de soporte".

**Lo que falta:**

1. **Ambiente (environment):** No se especifica en qué condiciones ocurre el estímulo. ¿Es en operación normal, en horario pico, con el sistema bajo carga, en modo degradado, durante un despliegue? El ambiente afecta directamente si la respuesta es alcanzable.

2. **Medida de la respuesta (response measure):** Este es el punto más crítico que falta. Un escenario de atributo de calidad debe ser verificable y medible, y aquí no hay ningún criterio cuantificable. Por ejemplo: ¿en cuánto tiempo debe registrarse el ticket? ¿en cuánto tiempo debe notificarse al equipo? ¿qué porcentaje de disponibilidad se exige para esta funcionalidad? ¿qué pasa si la notificación falla (reintentos, canal alternativo)? Sin una medida, no se puede saber si el sistema "cumple" o "no cumple" el escenario, lo cual contradice el propósito mismo del método.

3. **Precisión del estímulo:** "Error crítico" es ambiguo. ¿Qué hace que un error sea "crítico"? ¿Hay una clasificación de severidad predefinida (P1, P2, etc.) o queda a criterio del cliente? Esto debería estar acotado para que el escenario sea probable y no interpretable.

4. **Precisión del artefacto:** "El sistema" es demasiado amplio. Sería mejor identificar el componente específico (por ejemplo, el módulo de gestión de tickets o el servicio de notificaciones), sobre todo si el escenario se va a usar para evaluar una arquitectura concreta.

5. **Atributo de calidad implícito no declarado:** El escenario parece apuntar a *disponibilidad* o *usabilidad* (o incluso a un atributo más específico como "capacidad de soporte" / *supportability*), pero no queda explícito cuál es el atributo de calidad que se está evaluando. Esto importa porque condiciona qué tácticas arquitectónicas se van a considerar (detección de fallos, redundancia en la notificación, colas de mensajes, etc.).

**Ejemplo de versión completa (atributo de calidad: disponibilidad/capacidad de soporte):**

> "Un cliente reporta, a través del portal web, un error clasificado como crítico (severidad P1) mientras el sistema opera en condiciones normales de carga. El sistema de gestión de tickets debe registrar el ticket en la base de datos y enviar una notificación (email + Slack) al equipo de soporte de guardia en menos de 30 segundos desde que se recibe el reporte, con una tasa de éxito de notificación del 99.9%. Si la notificación inicial falla, el sistema debe reintentar hasta 3 veces en un lapso de 2 minutos y escalar a un canal alternativo (SMS) si no hay confirmación de entrega."

En resumen: el escenario original identifica correctamente la idea general (evento → registro → notificación), pero le faltan el ambiente y, sobre todo, una medida de respuesta cuantificable, además de precisar qué se entiende por "error crítico" y qué componente del sistema es el responsable. Sin esos elementos, el escenario no es testeable ni sirve como criterio de aceptación arquitectónico.

## Autocrítica (checklist SEI)

Análisis independiente del escenario original ("Cuando un cliente reporta un error crítico, el sistema debe registrar el ticket y notificar al equipo de soporte.") frente a las 6 partes del template SEI, y evaluación de cómo el baseline manejó cada una.

### 1. Fuente del estímulo
**Estado en el original:** presente pero genérico ("un cliente", sin distinguir tipo de cliente, canal de reporte, o si es un cliente autenticado/anónimo).
**Clasificación del baseline: Detectado pero mal resuelto.**
El baseline lo marca como presente "(aunque de forma genérica)", pero nunca lo incluye en la lista de "Lo que falta" ni propone ninguna precisión. En el "Ejemplo de versión completa" la fuente sigue siendo literalmente "Un cliente" (solo se le agrega el canal "a través del portal web", que en realidad describe el estímulo/ambiente, no la fuente); la genericidad señalada nunca se corrige.

### 2. Estímulo
**Estado en el original:** presente pero impreciso ("reporta un error crítico"; "crítico" no está definido).
**Clasificación del baseline: Correctamente detectado y resuelto.**
El baseline lo lista primero como "presente" y luego, en el punto 3 ("Precisión del estímulo"), señala explícitamente que "error crítico" es ambiguo y pregunta si existe una clasificación de severidad predefinida. En el ejemplo final lo resuelve de forma concreta: "un error clasificado como crítico (severidad P1)", que es verificable y no depende de interpretación subjetiva.

### 3. Artefacto
**Estado en el original:** presente pero excesivamente amplio ("el sistema", sin identificar componente).
**Clasificación del baseline: Detectado pero mal resuelto.**
El baseline identifica correctamente el problema en el punto 4 ("'El sistema' es demasiado amplio... sería mejor identificar el componente específico, por ejemplo el módulo de gestión de tickets o el servicio de notificaciones"), pero en su propio ejemplo final no aplica esa separación: usa un único artefacto ("El sistema de gestión de tickets") como responsable tanto de registrar el ticket como de notificar por email, Slack y escalar a SMS, mezclando dos responsabilidades que el propio baseline había sugerido distinguir.

### 4. Ambiente
**Estado en el original:** ausente por completo (no se indica el estado del sistema al llegar el estímulo).
**Clasificación del baseline: Correctamente detectado y resuelto.**
El baseline lo señala explícitamente como el primer punto faltante ("No se especifica en qué condiciones ocurre el estímulo... ¿operación normal, horario pico, sistema bajo carga, modo degradado?") y en el ejemplo final lo resuelve con una condición concreta y típica del formato SEI: "mientras el sistema opera en condiciones normales de carga".

### 5. Respuesta
**Estado en el original:** presente pero incompleta (no especifica canal de notificación ni qué ocurre si la notificación falla).
**Clasificación del baseline: Confundido.**
El baseline da la respuesta original por buena sin reservas ("Respuesta: 'registrar el ticket y notificar al equipo de soporte'" aparece en "Lo que sí está presente", sin ningún matiz de vaguedad, a diferencia de Fuente y Artefacto que sí llevan la aclaración "de forma genérica"). Las carencias reales de la respuesta —falta de canal de notificación, falta de mecanismo de reintento o escalamiento— sólo emergen mezcladas dentro de la pregunta retórica de la Medida de la respuesta: "¿qué pasa si la notificación falla (reintentos, canal alternativo)?" (punto 2). Reintentos y canal alternativo son acciones que el sistema ejecuta (parte de la Respuesta), no una forma de medir si la respuesta fue exitosa; al meterlas dentro del ítem de "Medida de la respuesta", el baseline confunde dos partes distintas del template en lugar de reconocer un vacío propio de la Respuesta.

### 6. Medida de respuesta
**Estado en el original:** ausente por completo (no hay ningún criterio cuantificable de éxito).
**Clasificación del baseline: Correctamente detectado y resuelto.**
El baseline lo identifica como "el punto más crítico que falta" y lo resuelve en el ejemplo final con criterios genuinamente medibles y verificables: "en menos de 30 segundos" y "con una tasa de éxito de notificación del 99.9%". (Nota: como se indica en el punto 5, esta misma sección arrastra también la definición de reintentos/canal alternativo, que en rigor pertenece a la Respuesta y no a su medida — el criterio cuantificable propiamente dicho, sin embargo, sí queda bien resuelto.)

### Resumen
El fallo más importante y recurrente del baseline es que, una vez que marca una parte como "presente", deja de exigirle el mismo estándar de precisión y verificabilidad que aplica a la Medida de la respuesta: la genericidad de la Fuente y el Artefacto queda señalada pero sin corregirse en el ejemplo final, y los vacíos reales de la Respuesta (canal, reintentos, escalamiento) terminan disueltos dentro de la discusión de la Medida en vez de reconocerse como un problema propio de esa parte. En otras palabras, el baseline es fuerte para detectar la ausencia de la Medida de la respuesta, pero inconsistente para exigir concreción en el resto de las partes y para mantener los límites entre ellas — este es exactamente el tipo de vacío que una skill de Claude Code debería forzar a resolver de forma sistemática, parte por parte.
