## Prompt

Revisá si este escenario está completo: "Cuando el sensor de temperatura del data center detecta que la sala de servidores supera los 30°C durante operación normal, el sistema de refrigeración debe activar el enfriamiento adicional y notificar al equipo de infraestructura, logrando bajar la temperatura a menos de 25°C en menos de 10 minutos en el 95% de los casos."

## Criterios de aceptación esperados

- Las 6 partes deben marcarse ✅: Fuente = sensor de temperatura; Estímulo = temperatura > 30°C; Ambiente = operación normal; Artefacto = sistema de refrigeración; Respuesta = activar enfriamiento + notificar; Medida = <25°C en <10min en el 95% de los casos.
- La skill NO debe inventar huecos que no existen en este escenario (es un test de falso positivo: un escenario genuinamente completo).
- Debe concluir explícitamente que el escenario está completo.

## Respuesta obtenida

Analicé el escenario contra el template de 6 partes del SEI. Resultado, parte por parte:

| # | Parte | Estado | Contenido identificado |
|---|-------|--------|-------------------------|
| 1 | Fuente de estímulo | ✅ | El sensor de temperatura del data center. Es un componente interno y automático (no un usuario ni un sistema externo), por lo que su confiabilidad no introduce ambigüedad relevante para la respuesta esperada. |
| 2 | Estímulo | ✅ | "la sala de servidores supera los 30°C". Es una condición medible y está redactada como el evento disparador, sin mezclarse con la respuesta (activar enfriamiento) ni con el ambiente (operación normal). |
| 3 | Ambiente | ✅ | "durante operación normal". Se indica explícitamente el modo de operación bajo el cual ocurre el estímulo, distinguiéndolo de un posible escenario en modo degradado o de mantenimiento. |
| 4 | Artefacto | ✅ | "el sistema de refrigeración". Está acotado a un componente específico (no "el sistema" en general), consistente con que es él quien recibe el estímulo y ejecuta la respuesta. |
| 5 | Respuesta | ✅ | "activar el enfriamiento adicional y notificar al equipo de infraestructura". Describe comportamiento concreto (dos acciones observables), no solo una intención genérica. |
| 6 | Medida de respuesta | ✅ | "bajar la temperatura a menos de 25°C en menos de 10 minutos en el 95% de los casos". Es cuantificable y verificable: define un umbral objetivo (25°C), un límite de tiempo (10 min) y una tasa de cumplimiento (95%), en lugar de un adjetivo vago como "rápido" o "eficaz".

**Conclusión: el escenario está completo.** Las 6 partes del template SEI están presentes y son verificables; no encontré huecos ni ambigüedades que señalar. Tampoco mezcla dos atributos de calidad distintos en la misma oración: todo el escenario gira en torno a un único atributo (disponibilidad/tolerancia a fallas del sistema de refrigeración, con un componente de rendimiento en el tiempo de respuesta), por lo que no hace falta separarlo en dos escenarios. No fue necesario proponer ninguna redacción adicional.

## Veredicto

- ✅ Las 6 partes se marcaron ✅ con el mapeo correcto: Fuente = sensor de temperatura; Estímulo = temperatura > 30°C; Ambiente = operación normal; Artefacto = sistema de refrigeración; Respuesta = activar enfriamiento + notificar; Medida = <25°C en <10min en el 95% de los casos.
- ✅ No se inventaron huecos inexistentes: la respuesta no marcó ninguna parte como ⚠️/❌ ni propuso completar nada que ya estuviera presente y medible.
- ✅ Se concluyó explícitamente que el escenario está completo, con la frase "el escenario está completo" en negrita como cierre.

**Resultado: PASA**
