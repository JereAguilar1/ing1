## Prompt

Revisá si este escenario está completo: "Cuando 1000 usuarios consultan el catálogo simultáneamente durante la promoción de Black Friday, el servicio de catálogo debe responder de manera eficiente."

## Criterios de aceptación esperados

- Fuente de estímulo, Estímulo, Ambiente y Artefacto deben marcarse razonablemente ✅ (están relativamente completos).
- Medida de respuesta debe marcarse ❌ ("de manera eficiente" no tiene número ni unidad).
- Debe proponer una redacción concreta con un número real (ej. tiempo de respuesta p95 en milisegundos, o throughput).

## Respuesta obtenida

Analicé el escenario contra el template de 6 partes del SEI. Este es el resultado:

| # | Parte | Estado | Detalle |
|---|-------|--------|---------|
| 1 | Fuente de estímulo | ✅ | "1000 usuarios" — está identificado quién origina el evento (usuarios finales del catálogo). No es un caso donde la confiabilidad de la fuente cambie la respuesta esperada (no es un escenario de seguridad), así que con el número de usuarios alcanza. |
| 2 | Estímulo | ✅ | "consultan el catálogo simultáneamente" — describe con claridad la condición disparadora (consultas concurrentes), sin mezclarse con el ambiente ni con la respuesta. |
| 3 | Ambiente | ✅ | "durante la promoción de Black Friday" — especifica el modo/circunstancia (carga pico), que es justo lo que suele omitirse en este tipo de escenarios. |
| 4 | Artefacto | ✅ | "el servicio de catálogo" — es un componente específico del sistema, no el genérico "el sistema". |
| 5 | Respuesta | ✅ | "debe responder" (a las consultas) describe una actividad de comportamiento del sistema (procesar y devolver resultado de la consulta), consistente con cómo se redacta la respuesta en escenarios de rendimiento clásicos del SEI. Es breve, pero no es solo una declaración de intención. |
| 6 | Medida de respuesta | ❌ | "de manera eficiente" no tiene número ni unidad. "Eficiente" es exactamente el tipo de adjetivo vago (como "rápido", "seguro") que el checklist marca como NO válido sin una cifra verificable. No se puede determinar con esta redacción si el escenario se cumple o no. |

### Propuesta de redacción para la Medida de respuesta

En vez de "responder de manera eficiente", propongo algo verificable, por ejemplo:

> "...el servicio de catálogo debe responder con un tiempo de respuesta p95 menor a 500 ms y una tasa de error menor al 1%, sin degradar el throughput por debajo de 200 solicitudes/segundo."

(El número exacto de milisegundos, throughput y tasa de error debería confirmarlo el equipo/negocio según el SLA real esperado para Black Friday; lo importante es que quede expresado como umbral cuantificable y no como adjetivo.)

### Sobre mezcla de atributos de calidad

El escenario trata un único atributo (rendimiento/performance) de forma consistente en toda la oración — no hay mezcla con seguridad, disponibilidad u otro atributo, así que no hace falta separarlo en dos escenarios.

### Escenario reescrito (completo)

> Cuando **1000 usuarios** *(fuente de estímulo)* **consultan el catálogo simultáneamente** *(estímulo)* **durante la promoción de Black Friday** *(ambiente)*, **el servicio de catálogo** *(artefacto)* **debe responder** *(respuesta)* **con un tiempo de respuesta p95 menor a 500 ms y una tasa de error menor al 1%, sin degradar el throughput por debajo de 200 solicitudes/segundo** *(medida de respuesta — ⚠️ PROPUESTA MÍA, a confirmar con el equipo/negocio; reemplaza a "de manera eficiente")*.

Todas las partes salvo la medida de respuesta ya estaban presentes y razonablemente completas en el escenario original. Lo único que falta agregar (y que marqué explícitamente arriba) es un umbral numérico concreto para la medida de respuesta.

## Veredicto

- Fuente de estímulo, Estímulo, Ambiente y Artefacto deben marcarse razonablemente ✅ (están relativamente completos). → ✅ Las cuatro partes se marcaron ✅ en la tabla, con justificación específica para cada una (1000 usuarios / consultas simultáneas / Black Friday / servicio de catálogo).
- Medida de respuesta debe marcarse ❌ ("de manera eficiente" no tiene número ni unidad). → ✅ Se marcó ❌ explícitamente, señalando que "eficiente" es un adjetivo vago sin cifra verificable, igual que el ejemplo del checklist ("rápido", "seguro").
- Debe proponer una redacción concreta con un número real (ej. tiempo de respuesta p95 en milisegundos, o throughput). → ✅ Se propuso "p95 menor a 500 ms", tasa de error <1% y throughput ≥200 solicitudes/segundo, además de reescribir el escenario completo marcando la propuesta como tal.

**Resultado: PASA**
