## Prompt

Revisá si este escenario está completo: "El sistema valida el formulario y muestra un mensaje de error al usuario."

## Criterios de aceptación esperados

- Debe señalar que lo descripto es en realidad la Respuesta, y que falta el Estímulo real (el evento que dispara esa validación, ej. el usuario envía el formulario con datos inválidos).
- Estímulo debe marcarse ❌ o ⚠️ por esta confusión, explicada explícitamente (no solo marcada sin justificación).

## Respuesta obtenida

Analicé el escenario: *"El sistema valida el formulario y muestra un mensaje de error al usuario."*

### Tabla de completitud (6 partes)

| Parte | Estado | Comentario |
|---|---|---|
| Fuente de estímulo | ❌ Ausente | No se dice quién origina el evento. Es relevante distinguir si es un usuario final completando el formulario, un proceso automático que pre-completa datos, o una integración externa que envía datos al formulario, porque puede cambiar el tipo de validación esperada. |
| Estímulo | ❌ Ausente / confundido con la Respuesta | Lo que el texto describe ("el sistema valida el formulario") **no es el estímulo, es parte de la Respuesta** — es una acción que hace el sistema, no el evento externo que dispara esa acción. Falta el evento real que gatilla la validación, por ejemplo: "el usuario envía (submit) el formulario con uno o más campos inválidos o incompletos". |
| Ambiente | ❌ Ausente | No se especifica bajo qué circunstancias ocurre esto: ¿operación normal?, ¿el usuario tiene sesión activa?, ¿es la primera vez que completa el formulario o un reintento? |
| Artefacto | ⚠️ Ambiguo | "El sistema" es genérico. El contexto sugiere que el estímulo golpea específicamente al formulario o al módulo/componente de validación de ese formulario, no al sistema completo. |
| Respuesta | ⚠️ Ambiguo (incompleta) | "Muestra un mensaje de error" describe un comportamiento, pero es incompleta: no dice qué mensaje (genérico o específico por campo), si resalta los campos con error, ni qué pasa con los datos ya ingresados (se conservan o se pierden). |
| Medida de respuesta | ❌ Ausente | No hay ningún criterio verificable: no se indica en cuánto tiempo debe aparecer el mensaje, ni cómo se verifica que el error mostrado sea el correcto (ej. cobertura de reglas de validación). |

### Detalle del problema principal: Estímulo confundido con Respuesta

El escenario tal como está redactado empieza directamente por lo que el sistema *hace* ("valida... y muestra..."), sin mencionar el evento externo que dispara esa validación. Esto es un caso típico de estímulo redactado como acción del sistema en lugar de como el evento disparador. El estímulo real debería ser algo como: **"el usuario envía el formulario con datos inválidos o campos obligatorios vacíos"**, y recién ahí la respuesta del sistema es validar y mostrar el error.

### Propuestas de redacción para completar

- **Fuente de estímulo (propuesta):** "un usuario final autenticado, interactuando con el formulario desde la interfaz web/móvil".
- **Estímulo (propuesta):** "el usuario envía (submit) el formulario con al menos un campo inválido o un campo obligatorio vacío".
- **Ambiente (propuesta):** "en operación normal del sistema, con el usuario dentro de una sesión activa (no expirada)".
- **Artefacto (propuesta):** "el módulo de validación del formulario [nombre del formulario específico, ej. formulario de registro]".
- **Respuesta (propuesta):** "el sistema identifica el/los campo(s) inválido(s), los resalta visualmente y muestra un mensaje de error específico por campo (no un mensaje genérico), sin perder los datos ya ingresados en los campos válidos".
- **Medida de respuesta (propuesta):** "el mensaje de error se muestra en menos de 500ms desde el submit; el 100% de las reglas de validación definidas para ese formulario son verificadas antes de mostrar el resultado".

### Escenario reescrito (propuesta completa — a revisar por el usuario)

> Un **usuario final autenticado** *(agregado)* **envía (submit) el formulario con al menos un campo inválido o un campo obligatorio vacío** *(agregado — reemplaza el estímulo original mal ubicado)*, **en operación normal del sistema, con la sesión activa** *(agregado)*, sobre **el módulo de validación del formulario [especificar cuál]** *(agregado/aclarado, antes decía "el sistema")*. El sistema identifica el/los campo(s) inválido(s), los resalta y muestra un mensaje de error específico por campo, conservando los datos ya ingresados en los campos válidos *(ampliado respecto del original)*, **en menos de 500ms desde el submit, verificando el 100% de las reglas de validación definidas** *(agregado)*.

No se detectó mezcla de dos atributos de calidad distintos en este escenario (es un único flujo de validación/usabilidad).

## Veredicto

- ✅ Se señala explícitamente que lo descripto ("el sistema valida el formulario y muestra un mensaje de error") es en realidad la Respuesta, y se propone el Estímulo real ("el usuario envía el formulario con al menos un campo inválido o un campo obligatorio vacío"), cumpliendo el primer criterio.
- ✅ La fila de Estímulo en la tabla está marcada ❌ ("Ausente / confundido con la Respuesta") con una justificación explícita en la propia fila y ampliada en la sección "Detalle del problema principal", no una marca sin explicación.

**Resultado: PASA**
