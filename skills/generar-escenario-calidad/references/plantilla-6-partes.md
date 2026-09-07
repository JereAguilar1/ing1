# Combinaciones típicas por atributo de calidad (referencia rápida)

Valores generales frecuentes en la literatura SEI/ATAM. Adaptalos siempre al sistema concreto; no los copies literal si el usuario ya dio contexto propio.

## Disponibilidad
- Fuente de estímulo: proceso interno, hardware, software, otro sistema
- Estímulo: falla (omisión, crash, timing, valor de respuesta incorrecto)
- Ambiente: operación normal / arranque / degradado
- Artefacto: componente, conector o todo el sistema
- Respuesta: detectar, notificar, registrar, recuperar (redundancia, reintento, failover)
- Medida de respuesta: tiempo de detección, tiempo de reparación (MTTR), % de disponibilidad, tasa de falla (MTBF)

## Modificabilidad
- Fuente de estímulo: desarrollador, administrador
- Estímulo: pedido de agregar/cambiar/eliminar una funcionalidad, plataforma o cualidad
- Ambiente: tiempo de diseño / compilación / build / despliegue / ejecución
- Artefacto: componente(s), datos, interfaces, conectores afectados
- Respuesta: realizar el cambio sin efecto colateral en otras funciones
- Medida de respuesta: costo (horas-persona), cantidad de componentes afectados, tiempo hasta el despliegue

## Rendimiento (Performance)
- Fuente de estímulo: usuarios, sistemas externos, procesos internos
- Estímulo: llegada de un evento/request (periódico, esporádico, o ráfaga)
- Ambiente: carga normal / carga pico / modo degradado
- Artefacto: sistema o componente específico
- Respuesta: procesar y devolver un resultado
- Medida de respuesta: latencia, throughput, tiempo de respuesta percentil (p95/p99), tasa de error bajo carga

## Seguridad
- Fuente de estímulo: atacante interno o externo (identificado o no), usuario legítimo
- Estímulo: intento de acceso no autorizado, manipulación de datos, denegación de servicio
- Ambiente: online / offline, conectado a red pública o interna
- Artefacto: dato, servicio o recurso del sistema
- Respuesta: autenticar, autorizar, encriptar, registrar el intento, alertar
- Medida de respuesta: % de ataques detectados/bloqueados, tiempo de detección, tiempo de recuperación tras brecha

## Usabilidad
- Fuente de estímulo: usuario final (novato o experto)
- Estímulo: quiere aprender la funcionalidad, ser eficiente, minimizar errores, o adaptar la interfaz
- Ambiente: primer uso / uso habitual
- Artefacto: la interfaz de usuario o una funcionalidad específica
- Respuesta: el sistema guía/asiste/confirma la acción
- Medida de respuesta: tiempo para completar la tarea, tasa de error del usuario, tasa de finalización de la tarea, puntaje de satisfacción (SUS)

## Testabilidad
- Fuente de estímulo: desarrollador o tester, en tiempo de diseño o después de un cambio
- Estímulo: se completó una unidad/incremento de desarrollo y necesita ser probado
- Ambiente: desarrollo, integración continua, staging
- Artefacto: componente(s) bajo prueba
- Respuesta: el sistema permite controlar el estado y observar la salida del componente
- Medida de respuesta: % de cobertura de código, tiempo/esfuerzo para encontrar la próxima falla, tiempo para ejecutar la suite

## Interoperabilidad
- Fuente de estímulo: sistema externo
- Estímulo: pedido de intercambiar datos/servicios con el sistema
- Ambiente: integración en tiempo de diseño o en tiempo de ejecución
- Artefacto: interfaz o servicio expuesto
- Respuesta: el sistema intercambia datos/servicios correctamente según el protocolo/formato acordado
- Medida de respuesta: % de mensajes/transacciones procesados correctamente, tiempo de integración, cantidad de incidentes de incompatibilidad
