## Prompt

Revisá si este escenario está completo: "Cuando se solicita agregar un nuevo tipo de reporte, el sistema debe implementarlo rápido y sin bugs."

## Criterios de aceptación esperados

- Debe señalar que el escenario mezcla modificabilidad ("agregar un nuevo tipo de reporte") con rendimiento/confiabilidad ("rápido y sin bugs") en una sola oración, y sugerir separarlo en escenarios distintos.
- Medida de respuesta debe marcarse ❌ (ni "rápido" ni "sin bugs" son cuantificables tal como están).

## Respuesta obtenida

**Escenario analizado:** "Cuando se solicita agregar un nuevo tipo de reporte, el sistema debe implementarlo rápido y sin bugs."

### ⚠️ Aviso previo: mezcla de atributos de calidad en una sola oración

Antes de la tabla, un señalamiento importante: este escenario mezcla dos atributos de calidad distintos en una sola frase:

- **Modificabilidad**: "agregar un nuevo tipo de reporte" — es un cambio al sistema, se evalúa en tiempo de diseño/desarrollo (costo, tiempo, componentes afectados).
- **Rendimiento / confiabilidad**: "rápido y sin bugs" — "rápido" sugiere una medida de tiempo (¿tiempo de implementación del cambio, o tiempo de ejecución del reporte una vez implementado? es ambiguo) y "sin bugs" sugiere una medida de confiabilidad/calidad del software (defectos, correctitud), que es un atributo distinto de la modificabilidad en sí.

Recomiendo **separar esto en dos escenarios**:
1. Un escenario de **modificabilidad** sobre el costo/tiempo de implementar el nuevo tipo de reporte.
2. Un escenario de **confiabilidad** (o rendimiento, según a qué se refiera "rápido") sobre el comportamiento del sistema ya con el cambio hecho (ej. tasa de defectos post-release, o tiempo de respuesta del nuevo reporte al ejecutarse).

Below se completa el análisis de las 6 partes tomando el escenario tal como está redactado (como escenario de modificabilidad, que es la lectura principal), para no perder de vista los demás huecos.

### Tabla de completitud

| # | Parte | Estado | Comentario |
|---|-------|--------|------------|
| 1 | Fuente de estímulo | ❌ Ausente | No se dice quién solicita el nuevo tipo de reporte. En modificabilidad esto importa: no es lo mismo que lo pida un desarrollador del equipo (cambio interno planificado) que un cliente externo vía un ticket de soporte, o un integrador usando una API pública (afecta el proceso de aprobación y el artefacto realmente impactado). |
| 2 | Estímulo | ✅ Presente | "se solicita agregar un nuevo tipo de reporte" describe con claridad la condición que dispara la respuesta: una solicitud de cambio concreta. No está mezclado con la respuesta ni con el ambiente. |
| 3 | Ambiente | ❌ Ausente | No se especifica en qué circunstancia ocurre el pedido: ¿en tiempo de diseño, antes de un release ya planificado, o como cambio urgente sobre el sistema en producción? Esto cambia mucho el esfuerzo y el riesgo esperado. |
| 4 | Artefacto | ⚠️ Ambiguo | "el sistema" es genérico. El contexto sugiere que el cambio impacta específicamente al módulo/motor de reportes (y potencialmente a la capa de persistencia o a la UI de selección de reportes), no a "todo el sistema". |
| 5 | Respuesta | ❌ Ausente / no descripta como comportamiento | "implementarlo" expresa una intención, no una secuencia de comportamiento verificable. No dice qué pasos sigue el equipo/sistema (diseño, desarrollo, pruebas, code review, despliegue) ni qué ocurre con el resto de las funcionalidades existentes. |
| 6 | Medida de respuesta | ❌ Ausente / no medible | "rápido" y "sin bugs" no son cuantificables tal como están. "Rápido" no dice respecto a qué unidad de tiempo (¿días, horas de desarrollo?) ni umbral. "Sin bugs" no es verificable sin un criterio objetivo (0 defectos es prácticamente imposible de garantizar y de medir sin una ventana temporal y un criterio de severidad). |

### Propuestas de redacción concreta para las partes incompletas

- **Fuente de estímulo**: "Un desarrollador del equipo, a pedido del Product Owner" (o la fuente real que corresponda: cliente externo, integrador, etc. — ajustar según el proceso real del equipo).
- **Ambiente**: "En tiempo de desarrollo, dentro del ciclo de un sprint planificado (no como hotfix sobre producción)."
- **Artefacto**: "El módulo de generación de reportes" (en lugar de "el sistema").
- **Respuesta**: "El equipo de desarrollo diseña, implementa y prueba el nuevo tipo de reporte integrándolo al módulo existente, sin modificar la interfaz pública de los reportes ya existentes, y lo somete a code review y a la suite de pruebas automatizadas antes de integrarlo a la rama principal."
- **Medida de respuesta**: "El cambio se implementa y se pasa a producción en menos de 5 días hábiles de trabajo, sin introducir regresiones: 0 fallos nuevos detectados por la suite de pruebas automatizadas y 0 incidentes reportados en producción durante los 7 días posteriores al release."

(Si lo que se quería expresar con "rápido" era el tiempo de *ejecución* del reporte una vez generado, y no el tiempo de implementación del cambio, esa sería una medida de **rendimiento** y correspondería a un escenario aparte, por ejemplo: "El nuevo reporte se genera en menos de 3 segundos (p95) bajo carga normal".)

### Escenario reescrito (propuesta — a revisar por el usuario)

> [PROPUESTO] Cuando **un desarrollador del equipo, a pedido del Product Owner** [agregado: fuente de estímulo], solicita agregar un nuevo tipo de reporte, **en tiempo de desarrollo, dentro del ciclo de un sprint planificado** [agregado: ambiente], **el módulo de generación de reportes** [agregado/precisado: artefacto] debe ser modificado de forma que **el equipo diseña, implementa y prueba el nuevo tipo de reporte integrándolo al módulo existente, sin modificar la interfaz pública de los reportes ya existentes, y lo somete a code review y a la suite de pruebas automatizadas antes de integrarlo a la rama principal** [agregado/precisado: respuesta], de modo que **el cambio se implementa y se pasa a producción en menos de 5 días hábiles, sin introducir regresiones: 0 fallos nuevos detectados por la suite de pruebas automatizadas y 0 incidentes reportados en producción durante los 7 días posteriores al release** [agregado: medida de respuesta].

Todo lo marcado como "agregado"/"precisado" es una propuesta mía para que el usuario la valide o ajuste con los números y responsables reales de su contexto — no debe tomarse como definitivo.

**Recordatorio**: como se señaló arriba, si "rápido" se refería al tiempo de ejecución del reporte (no al tiempo de implementar el cambio), ese aspecto debe separarse en un escenario de rendimiento independiente, con su propia fuente de estímulo (ej. "un usuario autenticado solicita el reporte"), ambiente ("bajo carga normal/pico") y medida de respuesta en milisegundos/segundos.

## Veredicto

- Criterio 1 — "Debe señalar que el escenario mezcla modificabilidad ('agregar un nuevo tipo de reporte') con rendimiento/confiabilidad ('rápido y sin bugs') en una sola oración, y sugerir separarlo en escenarios distintos.": ✅ Se señaló explícitamente en la sección "Aviso previo" y se propuso dividir en un escenario de modificabilidad y uno de rendimiento/confiabilidad.
- Criterio 2 — "Medida de respuesta debe marcarse ❌ (ni 'rápido' ni 'sin bugs' son cuantificables tal como están).": ✅ La fila 6 de la tabla marca Medida de respuesta como ❌ Ausente/no medible, justificando que ni "rápido" ni "sin bugs" tienen unidad o umbral verificable.

**Resultado: PASA**
