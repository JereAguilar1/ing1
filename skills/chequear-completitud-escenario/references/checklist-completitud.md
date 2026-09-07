# Cómo se suele completar cada parte, por tipo de atributo

Ejemplos de redacciones concretas para proponer cuando una parte está ausente o ambigua. Adaptá el número/umbral al contexto real del usuario — estos son puntos de partida, no valores a copiar sin criterio.

## Ambiente (la parte más omitida en prosa libre)
- Rendimiento: "durante carga pico (hora de mayor tráfico, ej. 9-10am)"
- Disponibilidad: "en operación normal, fuera de ventana de mantenimiento"
- Seguridad: "con el sistema expuesto a red pública"
- Modificabilidad: "en tiempo de diseño, antes del próximo release"

## Fuente de estímulo (la segunda más omitida)
- Seguridad: "un atacante externo no autenticado" vs. "un usuario interno con permisos elevados" (la respuesta esperada cambia según cuál sea)
- Disponibilidad: "un componente interno del sistema" vs. "un servicio de terceros del que dependemos"
- Modificabilidad: "un desarrollador del equipo" vs. "un integrador externo usando la API pública"

## Medida de respuesta (debe ser numérica o verificable)
- En vez de "rápido" → "en menos de 200ms (p95)"
- En vez de "seguro" → "0 accesos no autorizados exitosos en el período de auditoría; 100% de intentos fallidos registrados"
- En vez de "notificar" (sin plazo) → "dentro de los 5 minutos, con una tasa de entrega ≥ 99%"
- En vez de "fácil de usar" → "un usuario nuevo completa la tarea en menos de 3 intentos, sin ayuda externa"

## Artefacto (evitar "el sistema" cuando el contexto sugiere algo más específico)
- Si el escenario habla de un flujo puntual (ej. "reportar un error"), preguntá o proponé cuál componente lo atiende (ej. "el módulo de tickets", "el gateway de notificaciones") en vez de dejar "el sistema" genérico.

## Respuesta (describir comportamiento, no solo intención)
- En vez de "el sistema debe notificar al equipo" → "el sistema crea el ticket, lo asigna según la regla de guardia activa, y envía una notificación push + email al responsable asignado"
