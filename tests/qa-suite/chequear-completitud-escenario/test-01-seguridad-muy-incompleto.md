## Prompt

Revisá si este escenario está completo: "El sistema debe protegerse de ataques."

## Criterios de aceptación esperados

- Prácticamente las 6 partes deben marcarse ❌ (fuente, estímulo, ambiente, artefacto, respuesta y medida están todas vagas o ausentes).
- Debe señalar explícitamente que el enunciado es demasiado genérico para ser un escenario/ASR utilizable.
- Debe proponer, para cada parte, cómo especificarla (no solo decir "falta todo" sin ejemplos concretos).

## Respuesta obtenida

El enunciado **"El sistema debe protegerse de ataques"** es demasiado genérico para funcionar como escenario de atributo de calidad (ASR). Es una declaración de intención, no un escenario: no permite diseñar una solución concreta ni verificar si se cumplió, porque ninguna de las 6 partes del template SEI está especificada de forma concreta.

### Tabla de completitud

| # | Parte | Estado | Comentario |
|---|-------|--------|------------|
| 1 | Fuente de estímulo | ❌ | No se dice quién ataca. En seguridad esto es crítico: la respuesta esperada es muy distinta si es un atacante externo no autenticado, un usuario interno malicioso, o un proceso automatizado (bot/script de escaneo). |
| 2 | Estímulo | ❌ | "Ataques" es una categoría, no un evento disparador concreto. No se especifica el tipo de ataque (inyección SQL, fuerza bruta sobre login, XSS, DDoS, exfiltración de datos, etc.), y cada tipo dispara una respuesta distinta. |
| 3 | Ambiente | ❌ | No se indica bajo qué circunstancias ocurre: ¿el sistema expuesto a red pública o solo en red interna? ¿en operación normal o durante una ventana de mantenimiento? ¿en producción o en un ambiente de staging? |
| 4 | Artefacto | ❌ | "El sistema" es completamente genérico. No se identifica qué componente sería el blanco (ej. el formulario de login, la API pública, la base de datos, el endpoint de carga de archivos). |
| 5 | Respuesta | ❌ | "Protegerse" no describe ningún comportamiento observable. No dice si el sistema debe bloquear la solicitud, bloquear la IP/cuenta, registrar el evento (logging), alertar a un equipo, degradar el servicio, etc. |
| 6 | Medida de respuesta | ❌ | No hay ningún criterio cuantificable ni verificable. "Protegerse" no se puede auditar: no hay número de intentos tolerados, tiempo de detección, tasa de bloqueo exitoso, ni tasa de falsos positivos/negativos. |

**Resultado: 6 de 6 partes ausentes (❌).** El enunciado no es un escenario de atributo de calidad utilizable tal como está redactado — es un objetivo cualitativo general, y de ahí no se puede derivar ni una arquitectura ni una prueba de aceptación.

### Cómo especificar cada parte (propuestas concretas)

1. **Fuente de estímulo** → especificar el tipo de actor y su nivel de confianza. Ejemplo: *"un atacante externo no autenticado"* (distinto de *"un usuario interno con credenciales válidas pero permisos elevados indebidos"*, que exigiría controles diferentes).

2. **Estímulo** → elegir un tipo de ataque concreto y describir el evento, no la reacción. Ejemplo: *"realiza intentos repetidos de login con distintas contraseñas contra una misma cuenta (fuerza bruta)"* o *"envía una solicitud HTTP con una carga maliciosa de inyección SQL en el campo de búsqueda"*.

3. **Ambiente** → indicar la circunstancia operativa. Ejemplo: *"con el sistema expuesto a la red pública, en operación normal (no en mantenimiento)"*.

4. **Artefacto** → identificar el componente específico atacado, en vez de "el sistema". Ejemplo: *"el endpoint de autenticación (login) de la API"*.

5. **Respuesta** → describir el comportamiento esperado paso a paso. Ejemplo: *"el sistema detecta el patrón de intentos fallidos, bloquea temporalmente la cuenta/IP de origen, registra el incidente en el log de auditoría y notifica al equipo de seguridad"*.

6. **Medida de respuesta** → definir umbrales verificables. Ejemplo: *"la cuenta se bloquea luego de 5 intentos fallidos en 1 minuto; el bloqueo se aplica en menos de 2 segundos; el 100% de los intentos bloqueados quedan registrados en el log de auditoría; 0 accesos no autorizados exitosos durante la ventana de ataque simulado en pruebas de penetración"*.

### Escenario reescrito (propuesta, a revisar por el usuario)

> **[PROPUESTO] Fuente de estímulo:** un atacante externo no autenticado.
> **[PROPUESTO] Estímulo:** realiza intentos repetidos de login con distintas contraseñas contra una misma cuenta (ataque de fuerza bruta).
> **[PROPUESTO] Ambiente:** con el sistema expuesto a la red pública, en operación normal.
> **[PROPUESTO] Artefacto:** el endpoint de autenticación (login) de la API.
> **[PROPUESTO] Respuesta:** el sistema detecta el patrón de intentos fallidos, bloquea temporalmente la cuenta y la IP de origen, registra el incidente en el log de auditoría, y notifica al equipo de seguridad.
> **[PROPUESTO] Medida de respuesta:** el bloqueo se activa luego de 5 intentos fallidos en 1 minuto y se aplica en menos de 2 segundos; el 100% de los intentos quedan registrados; 0 accesos no autorizados exitosos durante la ventana de ataque en pruebas de penetración.

**Importante:** este escenario reescrito es una propuesta ilustrativa (marcada con [PROPUESTO]) basada en un solo tipo de ataque (fuerza bruta sobre login) para mostrar cómo se vería un escenario completo. El enunciado original no permite saber si esto es realmente lo que el usuario quiere proteger — "ataques" podría referirse a inyección SQL, XSS, DDoS, exfiltración de datos, etc., cada uno con su propio escenario. Se recomienda que el usuario defina, para cada tipo de ataque relevante a su sistema, un escenario separado siguiendo esta misma estructura, en vez de intentar cubrir "todos los ataques" en un único enunciado.

## Veredicto

- ✅ **"Prácticamente las 6 partes deben marcarse ❌"** — La tabla marca las 6 partes (fuente, estímulo, ambiente, artefacto, respuesta y medida) como ❌.
- ✅ **"Debe señalar explícitamente que el enunciado es demasiado genérico para ser un escenario/ASR utilizable"** — Se indica explícitamente al inicio y en la conclusión de la tabla ("no es un escenario de atributo de calidad utilizable tal como está redactado").
- ✅ **"Debe proponer, para cada parte, cómo especificarla (no solo decir 'falta todo' sin ejemplos concretos)"** — Se incluye una sección con una propuesta concreta y un ejemplo redactado para cada una de las 6 partes, más un escenario reescrito completo marcado como propuesta.

**Resultado: PASA**
