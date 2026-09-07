## Prompt

Armá un árbol de utilidad para un sistema de IoT industrial de una planta fabril, considerando disponibilidad, seguridad y modificabilidad como mínimo.

## Criterios de aceptación esperados

- Los 3 atributos pedidos están presentes.
- Refinamientos ligados a sensores/actuadores físicos y fallas de hardware/red industrial, no genéricos de software puro.
- Hojas completas: 6 partes separables, H/M/L justificado.
## Respuesta obtenida

Para este sistema de IoT industrial de una planta fabril armé el árbol con los 3 atributos pedidos (Disponibilidad, Seguridad, Modificabilidad) y agregué un cuarto atributo, Rendimiento, porque en un entorno industrial con lazos de control físicos el tiempo de respuesta es habitualmente un requisito crítico y el enunciado pide "como mínimo" esos tres, dejando lugar a ampliar. Si preferís dejarlo solo en los 3 originales, se puede quitar la rama de Rendimiento sin afectar el resto.

### Árbol indentado

```
Utility
├── Disponibilidad
│   ├── Recuperación ante falla de sensor/actuador de campo redundante
│   │   └── Escenario: conmutación automática a sensor redundante ante pérdida de señal — (H, M)
│   ├── Continuidad operativa ante caída de la red industrial (fieldbus/Profinet)
│   │   └── Escenario: failover de red industrial con PLC en modo autónomo — (H, H)
│   └── Recuperación ante falla del gateway IoT / edge device
│       └── Escenario: failover local a gateway secundario en standby — (M, M)
├── Seguridad
│   ├── Integridad de comandos hacia actuadores (OT)
│   │   └── Escenario: rechazo de comando no autenticado/anómalo a un actuador — (H, H)
│   ├── Segmentación y detección de acceso IT→OT no autorizado
│   │   └── Escenario: bloqueo de conexión no autorizada desde red corporativa a red OT — (H, M)
│   └── Integridad de firmware de dispositivos de campo
│       └── Escenario: verificación de firma y rollback ante firmware corrupto — (M, H)
├── Modificabilidad
│   ├── Incorporar un nuevo tipo de sensor/protocolo de campo
│   │   └── Escenario: agregar driver de protocolo OPC-UA sin tocar lógica de negocio — (M, L)
│   ├── Actualizar firmware de PLCs/gateways sin detener la línea
│   │   └── Escenario: rolling update de firmware con nodo redundante activo — (M, H)
│   └── Cambiar de proveedor de un sensor/actuador
│       └── Escenario: reemplazo de driver de actuador sin tocar lógica de control — (L, L)
└── Rendimiento
    ├── Latencia de control en lazo cerrado
    │   └── Escenario: corrección de variable de proceso en tiempo real — (H, M)
    └── Throughput de telemetría bajo pico de producción
        └── Escenario: ingesta de ráfaga de lecturas de miles de sensores — (M, M)
```

### Tabla

| Atributo | Refinamiento | Escenario (6 partes) | (Valor, Riesgo) | Justificación |
|---|---|---|---|---|
| Disponibilidad | Recuperación ante falla de sensor/actuador de campo redundante | Fuente de estímulo: sensor de temperatura/presión en la línea de producción · Estímulo: falla por omisión (deja de enviar lecturas) o valor fuera de rango físicamente imposible · Ambiente: operación normal, línea en producción activa (turno productivo) · Artefacto: módulo de adquisición de datos del gateway IoT de planta · Respuesta: el sistema detecta la pérdida de señal, conmuta automáticamente al sensor redundante (o a un valor estimado por modelo) y genera alarma en el HMI/SCADA · Medida de respuesta: detección de falla < 5s, conmutación a redundante < 10s, 0 paradas de línea no planificadas por esta causa | (H, M) | H de negocio: una parada de línea no planificada cuesta producción y puede generar riesgo de seguridad física; M de riesgo: la redundancia de sensores es un patrón conocido en automatización industrial, pero la conmutación automática integrada al PLC requiere cuidado |
| Disponibilidad | Continuidad operativa ante caída de la red industrial (fieldbus/Profinet) | Fuente de estímulo: switch industrial o segmento de red fieldbus (Profinet/Modbus TCP) · Estímulo: corte de comunicación entre PLC y gateway IoT por falla de cableado, EMI o switch caído · Ambiente: producción en curso, ruido eléctrico típico de motores industriales · Artefacto: enlace de comunicación PLC-gateway y el bus de campo · Respuesta: el sistema conmuta a la ruta de red redundante (anillo/topología con failover) y los PLC locales continúan operando en modo autónomo sin supervisión central · Medida de respuesta: tiempo de conmutación de red < 500ms (ej. estándar de redundancia industrial tipo MRP), 0 pérdida de control local durante el evento | (H, H) | H de negocio: sin comunicación con el gateway se pierde visibilidad y control remoto de la planta, con riesgo de seguridad física; H de riesgo: redes industriales con EMI y hardware legado hacen difícil garantizar los tiempos de conmutación exigidos |
| Disponibilidad | Recuperación ante falla del gateway IoT / edge device | Fuente de estímulo: gateway IoT de borde que agrega datos de múltiples PLCs · Estímulo: crash o cuelgue del gateway por sobrecarga de procesamiento de telemetría · Ambiente: horario de operación pico (turno de máxima producción) · Artefacto: gateway edge y su servicio de agregación/envío a la nube · Respuesta: un gateway secundario en standby toma el rol activo (failover local) y reenvía los datos en buffer a la plataforma central · Medida de respuesta: tiempo de failover < 30s, pérdida de datos de telemetría 0% (buffer local de al menos 1 hora) | (M, M) | M de negocio: afecta visibilidad y analítica pero los PLC siguen controlando la planta localmente; M de riesgo: patrón de alta disponibilidad conocido pero el buffering confiable en hardware edge limitado no está probado aún |
| Seguridad | Integridad de comandos hacia actuadores (OT) | Fuente de estímulo: atacante con acceso a la red OT (interno o vía pivote desde IT) · Estímulo: intento de inyectar o modificar un comando de control (ej. abrir una válvula, detener un motor) hacia un actuador · Ambiente: planta en operación normal, red OT segmentada de la red IT · Artefacto: canal de comunicación PLC-actuador y módulo de control de comandos del SCADA · Respuesta: el sistema valida integridad y autenticidad del comando (firma/checksum), rechaza comandos no autorizados o anómalos y genera alarma de seguridad al equipo de OT · Medida de respuesta: 100% de comandos no autenticados bloqueados, alerta emitida en < 30s | (H, H) | H de negocio: un comando malicioso a un actuador puede causar daño físico, pérdida de producción o riesgo a personas; H de riesgo: los protocolos industriales legados (Modbus) no tienen autenticación nativa, requiere una capa adicional aún no validada en este sistema |
| Seguridad | Segmentación y detección de acceso IT→OT no autorizado | Fuente de estímulo: usuario o proceso desde la red corporativa (IT) · Estímulo: intento de conexión desde la red IT hacia un dispositivo de la red OT (PLC/gateway) fuera de los canales permitidos · Ambiente: operación normal, firewall/DMZ entre IT y OT según arquitectura Purdue · Artefacto: firewall industrial / DMZ entre zonas IT y OT · Respuesta: el sistema bloquea la conexión no autorizada, registra el evento y notifica al SOC/equipo de seguridad OT · Medida de respuesta: 100% de intentos bloqueados fuera de la whitelist, tiempo de alerta < 1 min | (H, M) | H de negocio: un incidente que cruce de IT a OT puede paralizar la planta (ej. ransomware industrial); M de riesgo: la segmentación tipo Purdue es un patrón bien establecido y ya parcialmente implementado |
| Seguridad | Integridad de firmware de dispositivos de campo | Fuente de estímulo: proceso de actualización remota de firmware de un sensor/actuador inteligente o PLC · Estímulo: se recibe un paquete de actualización de firmware para un dispositivo de campo · Ambiente: ventana de mantenimiento programada, planta detenida o en modo reducido · Artefacto: módulo de gestión de firmware del dispositivo de campo · Respuesta: el sistema verifica la firma criptográfica del firmware antes de aplicarlo y rechaza/revierte si la verificación falla · Medida de respuesta: 100% de actualizaciones no firmadas o corruptas rechazadas, rollback automático en < 5 min si falla la validación post-actualización | (M, H) | M de negocio: importante para prevenir compromisos a largo plazo pero no es un evento diario; H de riesgo: muchos dispositivos de campo industriales legados no soportan verificación de firma nativa, requiere solución adicional no probada |
| Modificabilidad | Incorporar un nuevo tipo de sensor/protocolo de campo | Fuente de estímulo: ingeniero de planta / integrador de sistemas · Estímulo: pedido de soportar un nuevo tipo de sensor que usa un protocolo de campo distinto (ej. agregar sensores OPC-UA a una planta que hoy solo usa Modbus) · Ambiente: tiempo de diseño, próxima ventana de mantenimiento/expansión de planta · Artefacto: capa de adaptadores de protocolo del gateway IoT · Respuesta: se agrega un nuevo driver/adaptador de protocolo sin modificar la lógica de negocio del gateway ni los PLCs existentes · Medida de respuesta: cambio confinado a ≤ 2 componentes (capa de adaptadores + configuración), ≤ 40 horas-persona, sin downtime de los sensores existentes | (M, L) | M de negocio: permite escalar la planta a nuevos equipos sin gran esfuerzo, mejora competitividad; L de riesgo: el gateway ya está diseñado con arquitectura de adaptadores plugables para protocolos industriales |
| Modificabilidad | Actualizar firmware de PLCs/gateways sin detener la línea de producción | Fuente de estímulo: equipo de mantenimiento/IT industrial · Estímulo: pedido de desplegar una nueva versión de firmware/software en los PLCs o gateways de una línea · Ambiente: producción en curso, sin ventana de parada total disponible · Artefacto: PLCs y gateways de la línea afectada · Respuesta: se actualiza el firmware de forma progresiva (rolling update) en dispositivos redundantes, manteniendo al menos un nodo activo en todo momento · Medida de respuesta: 0 minutos de parada de línea atribuible a la actualización, rollback disponible en < 10 min si falla | (M, H) | M de negocio: reduce costos operativos al evitar paradas planificadas, pero no es crítico para la operación diaria; H de riesgo: el hardware industrial legado no siempre soporta actualización en caliente, patrón aún no validado en toda la flota de PLCs |
| Modificabilidad | Cambiar de proveedor de un sensor/actuador | Fuente de estímulo: área de compras/ingeniería de planta · Estímulo: pedido de reemplazar el modelo/proveedor de un actuador (ej. una válvula motorizada) por uno de otro fabricante · Ambiente: tiempo de diseño, planificación de reemplazo de equipos · Artefacto: capa de abstracción de dispositivos (device driver) del sistema de control · Respuesta: se reemplaza el driver específico del actuador sin modificar la lógica de control de la línea ni el SCADA · Medida de respuesta: cambio confinado a 1 componente (driver del dispositivo), ≤ 24 horas-persona, sin cambios en la lógica de control validada | (L, L) | L de negocio: deseable para evitar dependencia de un único proveedor (vendor lock-in), pero no urgente hoy; L de riesgo: la capa de abstracción de dispositivos ya existe y se usó en un cambio de proveedor anterior |
| Rendimiento | Latencia de control en lazo cerrado | Fuente de estímulo: sensor de posición/velocidad en un lazo de control crítico · Estímulo: cambio en la variable de proceso que requiere ajuste inmediato del actuador (ej. corrección de velocidad de una cinta transportadora) · Ambiente: operación normal a carga nominal · Artefacto: lazo de control PLC-sensor-actuador · Respuesta: el sistema calcula y aplica la corrección de control · Medida de respuesta: latencia extremo a extremo (sensor → actuador) < 20 ms, jitter < 5 ms | (H, M) | H de negocio: un lazo de control lento puede generar defectos de producción o riesgo mecánico; M de riesgo: los tiempos de ciclo del PLC son conocidos y medibles, pero el procesamiento adicional en el gateway podría sumar latencia |
| Rendimiento | Throughput de telemetría bajo pico de producción | Fuente de estímulo: conjunto de sensores de toda la planta (miles de puntos de datos) · Estímulo: ráfaga de lecturas de telemetría durante un pico de producción (todos los sensores reportando simultáneamente) · Ambiente: turno de máxima producción, todas las líneas activas · Artefacto: pipeline de ingesta de telemetría del gateway/plataforma IoT · Respuesta: el sistema ingiere y procesa todos los mensajes sin descartarlos, aplicando buffering si es necesario · Medida de respuesta: throughput sostenido ≥ 10.000 mensajes/segundo, 0% de mensajes descartados, latencia de ingesta p95 < 200ms | (M, M) | M de negocio: afecta la calidad de la analítica y el mantenimiento predictivo pero no la operación en tiempo real de la planta; M de riesgo: es un problema de escalabilidad conocido pero no probado a esta escala en este sistema específico |

### Candidatas a analizar primero en la evaluación de arquitectura (cuadrante H,H)

Dos hojas caen en (H,H) y deberían priorizarse en un ATAM / Lightweight Architecture Evaluation:

1. **Disponibilidad — Continuidad operativa ante caída de la red industrial (fieldbus/Profinet)**: alto valor de negocio (pérdida de control/visibilidad de planta) combinado con alto riesgo técnico (EMI, hardware legado, tiempos de conmutación exigentes).
2. **Seguridad — Integridad de comandos hacia actuadores (OT)**: alto valor de negocio (riesgo físico y de producción) combinado con alto riesgo técnico (protocolos industriales legados sin autenticación nativa).

Estas dos hojas son las que más incertidumbre arquitectónica concentran y las que más impacto tendrían si la arquitectura elegida no las resuelve bien, por lo que conviene destinarles el mayor tiempo de análisis en el taller ATAM.

## Veredicto

- ✅ Los 3 atributos pedidos están presentes. — Disponibilidad, Seguridad y Modificabilidad aparecen como ramas de primer nivel del árbol, cada una con 3 refinamientos y sus hojas (se agregó Rendimiento como cuarto atributo opcional, sin reemplazar ninguno de los pedidos).
- ✅ Refinamientos ligados a sensores/actuadores físicos y fallas de hardware/red industrial, no genéricos de software puro. — Los refinamientos son específicos del dominio OT: falla de sensor/actuador redundante, caída de red fieldbus/Profinet por EMI o switch, falla de gateway edge, integridad de comandos a actuadores, segmentación IT/OT tipo Purdue, firmware de dispositivos de campo, drivers de protocolo industrial (OPC-UA/Modbus) y reemplazo de proveedor de actuador — ninguno es un refinamiento genérico de software de negocio.
- ✅ Hojas completas: 6 partes separables, H/M/L justificado. — Las 11 hojas de la tabla tienen las 6 partes del template (Fuente de estímulo, Estímulo, Ambiente, Artefacto, Respuesta, Medida de respuesta) separadas explícitamente con "·", medidas de respuesta cuantificables, y cada puntaje (Valor, Riesgo) viene acompañado de una justificación de una línea que explica por qué es H/M/L de negocio y por qué es H/M/L de riesgo.

**Resultado: PASA**
