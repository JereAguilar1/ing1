## Prompt

Armá un árbol de utilidad para una app de transporte tipo Uber, con un mínimo de 4 atributos, incluyendo al menos disponibilidad y seguridad del usuario.

## Criterios de aceptación esperados

- Disponibilidad y seguridad del usuario están ambas presentes explícitamente.
- Refinamientos de dominio (matching de conductor, geolocalización, botón de pánico/seguridad del pasajero, etc.).
- Al menos 4 atributos totales, hojas completas (6 partes + H/M/L justificado).

## Respuesta obtenida

# Árbol de utilidad — App de transporte tipo Uber

La raíz es **Utility**: la "bondad" general de la app de transporte, tal como la evaluaría un usuario, un conductor y el negocio. A partir de ahí se despliegan 4 atributos de calidad, cada uno refinado en 1-3 sub-nodos específicos del dominio (matching de conductor, geolocalización en tiempo real, botón de pánico, etc.), y cada refinamiento termina en al menos una hoja: un escenario concreto con sus 6 partes y su puntaje (Valor de negocio, Riesgo técnico) justificado.

## 1. Árbol indentado

```
Utility
├── Disponibilidad
│   ├── Disponibilidad del servicio de matching conductor-pasajero
│   │   └── Escenario: pico de solicitudes 5x en hora punta — (H, M)
│   └── Disponibilidad del tracking GPS en tiempo real durante el viaje
│       └── Escenario: caída de la región/datacenter que aloja geolocalización — (H, H)
├── Seguridad del usuario
│   ├── Botón de pánico / alerta de emergencia del pasajero
│   │   └── Escenario: el pasajero presiona el botón de pánico en un viaje activo — (H, M)
│   ├── Verificación de identidad y antecedentes del conductor
│   │   └── Escenario: onboarding de un conductor nuevo — (H, L)
│   └── Protección de datos de ubicación y personales (privacidad)
│       └── Escenario: intento de acceso no autorizado al historial de ubicaciones — (H, M)
├── Rendimiento
│   ├── Tiempo de respuesta del matching conductor-pasajero
│   │   └── Escenario: pasajero solicita un viaje en condiciones normales — (H, M)
│   └── Throughput en horas de alta demanda (surge)
│       └── Escenario: demanda 8x en un evento excepcional (ej. Año Nuevo) — (M, H)
└── Usabilidad
    ├── Facilidad para solicitar un viaje (primera interacción)
    │   └── Escenario: usuario nuevo solicita su primer viaje — (M, L)
    └── Accesibilidad para usuarios con discapacidad visual
        └── Escenario: usuario con lector de pantalla solicita un viaje — sin puntaje (ver nota)
```

## 2. Tabla

| Atributo | Refinamiento | Escenario (resumen 6 partes) | (Valor, Riesgo) | Justificación |
|---|---|---|---|---|
| Disponibilidad | Disponibilidad del servicio de matching conductor-pasajero | Fuente de estímulo: pico de solicitudes concurrentes (hora punta / evento masivo) · Estímulo: aumento súbito de solicitudes de viaje que satura el matching · Ambiente: operación normal con carga 5x el promedio · Artefacto: servicio de matching conductor-pasajero · Respuesta: el sistema balancea carga entre instancias/regiones y degrada mostrando tiempos de espera estimados en vez de caerse · Medida de respuesta: el servicio permanece disponible (>99.9%) y responde en <5s incluso bajo 5x carga pico, sin caídas totales | (H, M) | H de negocio: sin matching el producto no existe, es el corazón del servicio; M de riesgo: el autoscaling es un patrón conocido, pero el matching geoespacial bajo carga real es más difícil de probar con confianza |
| Disponibilidad | Disponibilidad del tracking GPS en tiempo real durante el viaje | Fuente de estímulo: falla de infraestructura (caída de una región cloud o de un proveedor de mapas) · Estímulo: la región que aloja el servicio de geolocalización deja de responder · Ambiente: operación normal en producción, con viajes activos en curso · Artefacto: servicio de geolocalización / tracking en tiempo real · Respuesta: el sistema conmuta automáticamente (failover) a una región de respaldo sin perder la sesión del viaje en curso · Medida de respuesta: failover completo en menos de 30 segundos, sin pérdida de viajes activos y con notificación al usuario si hay degradación | (H, H) | H de negocio: un viaje en curso sin tracking es un riesgo de seguridad y de confianza del usuario; H de riesgo: los failovers multi-región con estado de sesión son notoriamente difíciles de lograr sin pérdida de datos |
| Seguridad del usuario | Botón de pánico / alerta de emergencia del pasajero | Fuente de estímulo: el pasajero durante un viaje activo · Estímulo: el pasajero presiona el botón de pánico dentro de la app · Ambiente: viaje en curso, en cualquier momento entre el inicio y el fin del trayecto · Artefacto: módulo de seguridad/emergencia y su integración con la central de monitoreo/servicios de emergencia · Respuesta: el sistema comparte ubicación en tiempo real y datos del viaje con la central de seguridad y/o contactos de confianza, y ofrece llamar a emergencias · Medida de respuesta: la alerta llega a la central/contacto de confianza en menos de 5 segundos, con la ubicación actualizándose cada ≤3 segundos | (H, M) | H de negocio: la seguridad física del pasajero es un diferenciador reputacional y regulatorio crítico (un caso de abuso daña gravemente la marca); M de riesgo: la integración con notificaciones push es conocida, pero garantizar esa latencia con mala señal es incierto |
| Seguridad del usuario | Verificación de identidad y antecedentes del conductor | Fuente de estímulo: un nuevo conductor que se registra en la plataforma · Estímulo: el conductor completa el registro y sube documentación (licencia, antecedentes penales) · Ambiente: proceso de onboarding, antes de habilitar al conductor · Artefacto: módulo de verificación de identidad / integración con background-check · Respuesta: el sistema valida automáticamente documentos y antecedentes contra fuentes oficiales antes de habilitar la cuenta · Medida de respuesta: el 100% de los conductores queda bloqueado para aceptar viajes hasta completar la verificación, con resolución en menos de 48 horas | (H, L) | H de negocio: es un requisito legal/reputacional no negociable en la mayoría de las jurisdicciones; L de riesgo: existen proveedores externos de background-check con APIs maduras, patrón ya validado en la industria |
| Seguridad del usuario | Protección de datos de ubicación y personales (privacidad) | Fuente de estímulo: atacante externo o empleado interno sin autorización · Estímulo: intento de acceso no autorizado al historial de ubicaciones de un usuario · Ambiente: operación normal, base de datos de producción · Artefacto: almacén de datos de viajes/ubicación y capa de control de acceso · Respuesta: el sistema rechaza el acceso, registra el intento en auditoría y alerta al equipo de seguridad · Medida de respuesta: 100% de los accesos no autorizados bloqueados, con alerta emitida en menos de 1 minuto y sin exposición de datos | (H, M) | H de negocio: una filtración de datos de ubicación puede exponer físicamente a usuarios y generar sanciones regulatorias (ej. GDPR); M de riesgo: control de acceso y auditoría son patrones conocidos, pero cubrir todos los vectores internos/externos exige revisión continua |
| Rendimiento | Tiempo de respuesta del matching conductor-pasajero | Fuente de estímulo: un pasajero · Estímulo: el pasajero solicita un viaje desde la app · Ambiente: operación normal, densidad de conductores típica de una zona urbana · Artefacto: servicio de matching y algoritmo de asignación · Respuesta: el sistema calcula y devuelve el conductor asignado más cercano disponible · Medida de respuesta: el 95% de las solicitudes recibe un conductor asignado en menos de 4 segundos | (H, M) | H de negocio: la percepción de rapidez para encontrar conductor es el principal factor de retención frente a la competencia; M de riesgo: el algoritmo de matching geoespacial con optimización de ETA es complejo de afinar a esa latencia |
| Rendimiento | Throughput en horas de alta demanda (surge) | Fuente de estímulo: evento de alta demanda (hora pico, evento masivo, mal clima) · Estímulo: el volumen de solicitudes se multiplica por 8 respecto al promedio · Ambiente: pico de demanda excepcional (ej. Año Nuevo) · Artefacto: backend de procesamiento de solicitudes y cálculo de tarifas dinámicas (surge pricing) · Respuesta: el sistema escala horizontalmente y sigue procesando solicitudes sin degradar el tiempo de respuesta · Medida de respuesta: el sistema procesa al menos 8x el volumen normal de solicitudes por segundo, manteniendo el p95 de latencia bajo 6 segundos | (M, H) | M de negocio: afecta la experiencia pero el usuario tolera algo de demora en eventos excepcionales conocidos; H de riesgo: escalar 8x con cálculo de tarifas dinámicas en tiempo real es un escenario que rara vez se prueba a esa escala |
| Usabilidad | Facilidad para solicitar un viaje (primera interacción) | Fuente de estímulo: un usuario nuevo que instala la app por primera vez · Estímulo: el usuario intenta solicitar su primer viaje sin haber usado la app antes · Ambiente: primer uso, sin configuración previa de método de pago · Artefacto: flujo de onboarding y solicitud de viaje de la interfaz · Respuesta: la app guía al usuario paso a paso (destino, método de pago, confirmación) con mínima fricción · Medida de respuesta: el 90% de los usuarios nuevos completa su primera solicitud de viaje en menos de 3 minutos sin asistencia externa | (M, L) | M de negocio: mejora la conversión de nuevos usuarios pero no es bloqueante como la seguridad o la disponibilidad; L de riesgo: son patrones de UX de onboarding ampliamente conocidos y probados en apps móviles |
| Usabilidad | Accesibilidad para usuarios con discapacidad visual | Fuente de estímulo: un usuario con discapacidad visual que usa un lector de pantalla · Estímulo: el usuario navega la app con VoiceOver/TalkBack para solicitar un viaje · Ambiente: operación normal, dispositivo con lector de pantalla activado · Artefacto: interfaz de usuario del flujo de solicitud de viaje · Respuesta: todos los elementos interactivos exponen etiquetas accesibles y el flujo se completa solo con el lector de pantalla · Medida de respuesta: el usuario completa la solicitud de un viaje usando exclusivamente el lector de pantalla, sin pasos bloqueados, verificado en una auditoría WCAG 2.1 AA | sin puntaje confiable | No tengo información suficiente sobre el estado actual de accesibilidad de la app (si ya hay soporte parcial o ninguno) para puntuar el riesgo técnico con confianza. En negocio estimaría (M) por inclusión y cumplimiento normativo en varias jurisdicciones, pero prefiero señalarlo como pendiente de validar con producto/legal en vez de inventar el score completo |

## 3. Candidatas a analizar primero (cuadrante H, H)

La única hoja que cae limpiamente en (H, H) es **Disponibilidad del tracking GPS en tiempo real durante el viaje** (failover de región/datacenter): es imprescindible para el negocio (un viaje activo sin tracking es un problema de seguridad y confianza) y el riesgo técnico es alto (failover multi-región con estado de sesión, difícil de garantizar sin pérdida de datos). Esta es la primera candidata para un ATAM / evaluación de arquitectura.

Como segunda prioridad, aunque no cae en (H,H) puro, vale la pena vigilar **Throughput en horas de alta demanda (surge)** (M, H): el riesgo técnico es igual de alto y, si bien el valor de negocio individual es medio, un fallo ahí en un evento masivo tiene efecto reputacional amplificado — conviene revisarla junto con la anterior si el tiempo de la evaluación lo permite.

La hoja de accesibilidad quedó sin puntaje de riesgo por falta de información sobre el estado actual de implementación; antes de descartarla o priorizarla habría que relevar ese dato con el equipo.

## Veredicto

- ✅ Disponibilidad y seguridad del usuario están ambas presentes explícitamente — Disponibilidad y "Seguridad del usuario" son dos de los 4 atributos raíz del árbol, cada uno con sus refinamientos y hojas propias.
- ✅ Refinamientos de dominio (matching de conductor, geolocalización, botón de pánico/seguridad del pasajero, etc.) — se incluyeron "Disponibilidad del servicio de matching conductor-pasajero", "Disponibilidad del tracking GPS en tiempo real", "Botón de pánico / alerta de emergencia del pasajero" y "Verificación de identidad del conductor", todos específicos del dominio de transporte tipo Uber, no genéricos.
- ✅ Al menos 4 atributos totales, hojas completas (6 partes + H/M/L justificado) — hay exactamente 4 atributos (Disponibilidad, Seguridad del usuario, Rendimiento, Usabilidad), y de las 9 hojas, 8 tienen las 6 partes explícitas más un puntaje (H/M/L, H/M/L) con justificación de una línea; la novena (accesibilidad) declara explícitamente que no hay información suficiente para puntuar el riesgo en vez de inventarlo, tal como exige el paso 4 del procedimiento del skill.

**Resultado: PASA**
