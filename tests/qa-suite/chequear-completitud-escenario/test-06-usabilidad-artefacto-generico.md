## Prompt

Revisá si este escenario está completo: "Un usuario nuevo quiere completar su primera compra sin ayuda, y el sistema debe guiarlo."

## Criterios de aceptación esperados

- Artefacto debe marcarse ⚠️ ("el sistema" es genérico; debería precisarse el flujo de checkout/onboarding u otro componente concreto).
- Medida de respuesta debe marcarse ❌ (falta una tasa de finalización de tarea, tiempo, o tasa de error).
- Debe proponer una redacción concreta que reemplace "el sistema" por algo más específico.

## Respuesta obtenida

Analicé el escenario contra el template de 6 partes del SEI. Es un escenario de **usabilidad** (un solo atributo de calidad, no hay mezcla que separar).

### Tabla de completitud

| Parte | Estado | Justificación |
|---|---|---|
| Fuente de estímulo | ⚠️ ambiguo | "Un usuario nuevo" identifica al actor, pero es ambiguo si se refiere a alguien recién registrado o a alguien que usa el flujo de compra por primera vez sin haber tenido contacto previo con el sistema. Para usabilidad esa distinción sí importa (cambia qué tan familiarizado está con patrones de UI similares). |
| Estímulo | ⚠️ ambiguo | "Quiere completar su primera compra sin ayuda" describe una intención/deseo, no un evento concreto que dispara la respuesta del sistema. Falta el evento disparador puntual (ej. que el usuario inicie el checkout). |
| Ambiente | ❌ ausente | No se especifica bajo qué circunstancias ocurre: no se dice si es en condiciones normales de operación, en qué dispositivo/canal (web, app), ni si el usuario ya pasó por un onboarding o llega sin ningún contexto previo. Se debe asumir que falta. |
| Artefacto | ⚠️ ambiguo | "El sistema" es genérico. El contexto sugiere que el estímulo aplica puntualmente al flujo de checkout/compra (carrito, datos de envío, pago, confirmación), no a "el sistema" completo. |
| Respuesta | ⚠️ ambiguo | "Debe guiarlo" describe una intención general, no un comportamiento verificable del sistema (qué hace concretamente: ¿muestra pasos?, ¿valida campos?, ¿da mensajes de error?). |
| Medida de respuesta | ❌ ausente | No hay ninguna métrica. Palabras como "guiarlo... sin ayuda" no cuentan como medida: falta una tasa de finalización de tarea, un tiempo máximo, o una tasa de error/abandono. |

### Propuestas de redacción concreta

- **Fuente de estímulo:** "Un usuario que ingresa por primera vez al flujo de compra, sin capacitación previa ni ayuda de terceros (soporte humano, chat, documentación externa)."
- **Estímulo:** "El usuario inicia el proceso de checkout (agrega un producto al carrito y presiona 'comprar')."
- **Ambiente:** "En condiciones normales de operación, durante su primer intento de compra en el sitio, sin haber consultado ayuda ni documentación previa."
- **Artefacto:** "El flujo de checkout (carrito → datos de envío → método de pago → confirmación)."
- **Respuesta:** "El flujo de checkout presenta los pasos de forma secuencial, valida los datos ingresados en cada paso, y muestra mensajes de error específicos y accionables ante campos incompletos o inválidos, sin requerir intervención humana."
- **Medida de respuesta:** "Al menos el 90% de los usuarios nuevos completa la compra en menos de 5 minutos sin abandonar el flujo, y menos del 5% necesita contactar soporte o abandona por error."

### Escenario reescrito (propuesta, a revisar por el usuario)

"**[agregado]** Un usuario que ingresa por primera vez al flujo de compra, sin capacitación previa ni ayuda de terceros, **[estímulo ajustado]** inicia el proceso de checkout agregando un producto al carrito y presionando 'comprar', **[agregado — ambiente]** en condiciones normales de operación, durante su primer intento de compra en el sitio y sin haber consultado ayuda previa. **[artefacto precisado]** El flujo de checkout (carrito → datos de envío → pago → confirmación) **[respuesta precisada]** presenta los pasos de forma secuencial, valida los datos en cada paso y muestra mensajes de error específicos ante campos incompletos, sin requerir intervención humana. **[agregado — medida de respuesta]** Como resultado, al menos el 90% de los usuarios nuevos completa la compra en menos de 5 minutos sin abandonar el flujo, y menos del 5% necesita contactar soporte."

Todo lo marcado como "agregado" o "precisado" es una propuesta mía para que el usuario la valide con datos reales del negocio (los umbrales de 90%/5 min/5% son ilustrativos, no definitivos).

## Veredicto

- Artefacto debe marcarse ⚠️ ("el sistema" es genérico; debería precisarse el flujo de checkout/onboarding u otro componente concreto). → ✅ Cumplido: la tabla marca Artefacto como ⚠️ ambiguo y propone "el flujo de checkout" como reemplazo concreto de "el sistema".
- Medida de respuesta debe marcarse ❌ (falta una tasa de finalización de tarea, tiempo, o tasa de error). → ✅ Cumplido: la tabla marca Medida de respuesta como ❌ ausente y la propuesta agrega tasa de finalización (90%), tiempo (5 min) y tasa de error/soporte (5%).
- Debe proponer una redacción concreta que reemplace "el sistema" por algo más específico. → ✅ Cumplido: se propone "El flujo de checkout (carrito → datos de envío → método de pago → confirmación)" y se usa esa redacción en el escenario reescrito.

**Resultado: PASA**
