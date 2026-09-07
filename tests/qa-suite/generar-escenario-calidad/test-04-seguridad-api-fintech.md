## Prompt

Escenario concreto de seguridad: la API pública de una fintech recibe un intento de acceso no autorizado a datos de cuentas.

## Criterios de aceptación esperados

- Fuente de estímulo caracteriza explícitamente al atacante (externo, no autenticado), no queda como "alguien" genérico.
- Respuesta incluye detección + bloqueo/denegación + registro del intento.
- Medida de respuesta cuantificable (% de intentos bloqueados, tiempo de detección, etc.) — no solo "de forma segura".

## Respuesta obtenida

Escenario concreto de **seguridad** para la API pública de una fintech, ante un intento de acceso no autorizado a datos de cuentas:

| Parte | Valor |
|---|---|
| Fuente de estímulo | Un atacante externo no autenticado: un actor sin credenciales válidas ni sesión activa en el sistema, que interactúa con la API pública desde Internet (no es un usuario legítimo ni un proceso interno) |
| Estímulo | Intento de acceso no autorizado a datos de cuentas: por ejemplo, una solicitud al endpoint de consulta de saldo/movimientos usando un token inválido, expirado o robado, o mediante manipulación del identificador de cuenta en la URL/payload para acceder a una cuenta ajena (IDOR) |
| Ambiente | Operación normal, con la API pública expuesta en producción y accesible desde red pública (Internet), bajo carga habitual de tráfico |
| Artefacto | El endpoint/servicio de la API que expone los datos de cuentas, incluyendo su capa de autenticación y autorización |
| Respuesta | El sistema detecta que la solicitud no está autorizada, deniega el acceso (responde 401/403 sin exponer ningún dato de la cuenta), registra el intento en el log de auditoría/SIEM (IP de origen, timestamp, endpoint, identidad reclamada, tipo de fallo) y dispara una alerta al equipo de seguridad si el mismo origen supera un umbral de intentos fallidos repetidos |
| Medida de respuesta | ≥ 99.5% de los intentos de acceso no autorizado son bloqueados; tiempo de detección y denegación < 200 ms por solicitud; 100% de los intentos bloqueados quedan registrados en el log de auditoría; alerta al equipo de seguridad en < 5 minutos cuando se superan 10 intentos fallidos desde el mismo origen en 1 minuto |

**Prosa:** "Cuando un atacante externo no autenticado intenta acceder a datos de cuentas a través de la API pública de la fintech (por ejemplo, con un token inválido o manipulando el identificador de cuenta), el sistema debe detectar y denegar el acceso en menos de 200 ms, registrar el 100% de estos intentos en el log de auditoría, y alertar al equipo de seguridad en menos de 5 minutos si se detectan más de 10 intentos fallidos desde el mismo origen en un minuto, bloqueando al menos el 99.5% de los intentos no autorizados."

**Supuestos marcados** (el prompt no especificó valores numéricos; se proponen como punto de partida a validar con el equipo de seguridad/negocio): umbral de 99.5% de bloqueo, latencia de detección de 200 ms, umbral de alerta de 10 intentos/minuto y SLA de alerta de 5 minutos.

## Veredicto

- Fuente de estímulo caracteriza explícitamente al atacante (externo, no autenticado), no queda como "alguien" genérico. → ✅ La fila "Fuente de estímulo" dice explícitamente "atacante externo no autenticado: un actor sin credenciales válidas ni sesión activa... desde Internet", excluyendo usuarios legítimos y procesos internos.
- Respuesta incluye detección + bloqueo/denegación + registro del intento. → ✅ La fila "Respuesta" cubre las tres cosas: "detecta que la solicitud no está autorizada", "deniega el acceso (401/403)" y "registra el intento en el log de auditoría/SIEM", más alerta adicional.
- Medida de respuesta cuantificable (% de intentos bloqueados, tiempo de detección, etc.) — no solo "de forma segura". → ✅ La fila "Medida de respuesta" da valores numéricos concretos: ≥99.5% bloqueados, <200 ms de detección, 100% registrados, alerta en <5 min ante 10 intentos/min, y se marcan explícitamente como supuestos a validar.

**Resultado: PASA**
