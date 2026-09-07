## Prompt

Revisá si este escenario está completo: "Cuando entra en vigencia una nueva regulación de protección de datos, el sistema debe adaptarse para cumplirla." (el atributo de calidad aquí sería algo como "cumplimiento normativo / compliance", que no es uno de los 7 atributos clásicos del libro)

## Criterios de aceptación esperados

- Debe tratar el atributo no clásico con el mismo rigor que uno clásico (no debe rechazarlo ni bajar el estándar de análisis por no ser uno de los 7 atributos del libro).
- Ambiente, Artefacto y Medida de respuesta deben marcarse ausentes o vagos.
- La propuesta de redacción debe incluir un plazo concreto (ej. "dentro de los 90 días de publicada la regulación") y una medida verificable (ej. "100% de los flujos de datos personales auditados y actualizados").

## Respuesta obtenida

Aclaración inicial: "cumplimiento normativo / compliance" no es uno de los 7 atributos de calidad clásicos del libro del SEI (rendimiento, seguridad, disponibilidad, modificabilidad, testabilidad, usabilidad, interoperabilidad), pero el template de 6 partes (fuente de estímulo, estímulo, ambiente, artefacto, respuesta, medida de respuesta) es agnóstico al atributo: se aplica exactamente con el mismo rigor. Que sea un atributo "no clásico" no baja el estándar de análisis ni es motivo para aceptar el escenario con menos precisión.

### Tabla de completitud

| # | Parte | Estado | Comentario |
|---|-------|--------|------------|
| 1 | Fuente de estímulo | ⚠️ Ambiguo | Se menciona "una nueva regulación", pero no se identifica el origen: ¿qué organismo/jurisdicción la emite (ley nacional, normativa sectorial, GDPR, LGPD, etc.)? Al ser una fuente externa (un regulador), importa su alcance y obligatoriedad para saber cómo se detecta y valida que "entró en vigencia". |
| 2 | Estímulo | ✅ Presente | "Entra en vigencia una nueva regulación de protección de datos" describe el evento disparador con claridad, sin mezclarlo con la respuesta del sistema. |
| 3 | Ambiente | ❌ Ausente | No se dice bajo qué circunstancias ocurre: ¿el sistema está en producción activa procesando datos de usuarios reales? ¿hay una ventana de mantenimiento o corte de servicio? ¿aplica igual a todos los entornos (producción, staging)? No se menciona nada, así que se asume ausente. |
| 4 | Artefacto | ⚠️ Vago | "El sistema" es genérico. No se especifica qué componentes/flujos son estimulados realmente: ¿base de datos de usuarios, formularios de registro, integraciones con terceros, logs, políticas de retención? Una regulación de protección de datos normalmente no afecta "todo el sistema" por igual, sino los puntos donde se capturan/almacenan/transmiten datos personales. |
| 5 | Respuesta | ⚠️ Ambiguo | "Debe adaptarse para cumplirla" describe una intención, no una actividad concreta. No se enumeran los pasos esperados (auditar flujos de datos, actualizar políticas de privacidad y consentimientos, modificar esquemas/código no conformes, documentar la conformidad, notificar a usuarios, etc.). |
| 6 | Medida de respuesta | ❌ Ausente | No hay plazo ni criterio verificable de cumplimiento. "Cumplirla" no es medible: no sabemos en cuánto tiempo, ni qué porcentaje o evidencia demuestra que el cumplimiento se logró. |

### Propuestas de redacción para las partes incompletas

- **Fuente de estímulo (⚠️→✅):** "El área de compliance/legal, al identificar la publicación oficial de una nueva ley o normativa de protección de datos en una jurisdicción donde opera la organización."
- **Ambiente (❌→✅):** "Mientras el sistema está en producción, procesando datos personales de usuarios activos, sin que se admita interrumpir el servicio durante la adaptación."
- **Artefacto (⚠️→✅):** "Los componentes que capturan, almacenan, procesan o transmiten datos personales: formularios de registro/consentimiento, base de datos de usuarios, integraciones con terceros, políticas de retención y logs de auditoría."
- **Respuesta (⚠️→✅):** "El equipo de desarrollo y el área de compliance deben: (1) auditar todos los flujos de datos personales identificados, (2) actualizar las políticas de privacidad y los mecanismos de consentimiento, (3) modificar el código, esquemas de datos o integraciones que no cumplan con la nueva normativa, y (4) documentar la conformidad para una eventual auditoría regulatoria."
- **Medida de respuesta (❌→✅):** "Dentro de los 90 días corridos desde la publicación oficial de la regulación, el 100% de los flujos de datos personales identificados deben estar auditados, actualizados y documentados como conformes, verificado mediante un checklist de compliance firmado por el responsable de protección de datos (DPO)."

### Chequeo de mezcla de atributos

El escenario no mezcla explícitamente dos atributos de calidad distintos en la misma oración (por ejemplo, no combina compliance con rendimiento o seguridad en la misma frase). Aunque compliance y seguridad suelen relacionarse en la práctica (proteger datos personales toca controles de seguridad), tal como está redactado el escenario se mantiene enfocado en un solo atributo (cumplimiento normativo), así que no hace falta separarlo en dos escenarios.

### Escenario reescrito (cambios propuestos marcados con **[AGREGADO]**)

"**[AGREGADO: fuente]** El área de compliance/legal, al identificar la publicación oficial de una nueva ley o normativa de protección de datos en una jurisdicción donde opera la organización (estímulo: entra en vigencia dicha regulación), **[AGREGADO: artefacto]** sobre los componentes que capturan, almacenan, procesan o transmiten datos personales (formularios de registro/consentimiento, base de datos de usuarios, integraciones con terceros, políticas de retención y logs de auditoría), **[AGREGADO: ambiente]** mientras el sistema está en producción y procesando datos de usuarios activos sin interrumpir el servicio, **[AGREGADO: respuesta concreta]** debe: (1) auditar todos los flujos de datos personales identificados, (2) actualizar las políticas de privacidad y los mecanismos de consentimiento, (3) modificar el código, esquemas o integraciones no conformes, y (4) documentar la conformidad para auditoría, de forma que **[AGREGADO: medida]** dentro de los 90 días corridos desde la publicación oficial de la regulación, el 100% de los flujos de datos personales identificados queden auditados, actualizados y documentados como conformes, verificado mediante un checklist de compliance firmado por el DPO."

Estas propuestas son sugerencias para que el usuario las revise y ajuste según el contexto real de la organización (jurisdicción aplicable, plazos legales específicos, estructura del equipo de compliance); no deben tomarse como definitivas sin validación.

## Veredicto

- ✅ Trata "cumplimiento normativo" con el mismo rigor que un atributo clásico: se aclara explícitamente al inicio que el template de 6 partes es agnóstico al atributo y se aplica el análisis completo (tabla de 6 filas, propuestas concretas, reescritura), sin rebajar el estándar por no ser uno de los 7 atributos del libro.
- ✅ Ambiente se marca ❌ ausente, Artefacto se marca ⚠️ vago y Medida de respuesta se marca ❌ ausente — las tres partes exigidas quedan señaladas como ausentes o vagas, cada una con su propia justificación independiente.
- ✅ La propuesta de redacción incluye un plazo concreto ("dentro de los 90 días corridos desde la publicación oficial de la regulación") y una medida verificable ("el 100% de los flujos de datos personales identificados queden auditados, actualizados y documentados como conformes, verificado mediante un checklist de compliance firmado por el DPO"), coincidiendo con los ejemplos pedidos.

**Resultado: PASA**
