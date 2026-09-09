## Prompt

Armá un árbol de utilidad para un sistema de historia clínica electrónica, con al menos 5 atributos de calidad, dado que va a manejar datos sensibles de pacientes.

## Criterios de aceptación esperados

- Al menos 5 atributos distintos.
- Seguridad/privacidad aparece con peso fuerte (razonable que caiga en cuadrante H,H dado el dominio).
- Refinamientos específicos del dominio salud (no genéricos).
- Estructura y hojas igual de completas que en el resto de la suite (6 partes, H/M/L justificado).

## Respuesta obtenida

Dado que es un sistema de **historia clínica electrónica (HCE)** que maneja datos sensibles de pacientes, derivo 5 atributos de calidad relevantes: **Seguridad y privacidad**, **Disponibilidad**, **Rendimiento**, **Interoperabilidad** y **Auditabilidad/Trazabilidad**. Por el dominio (datos de salud, marco regulatorio tipo HIPAA / Ley 25.326), le doy a Seguridad y privacidad el mayor peso del árbol.

### Árbol indentado

```
Utility
├── Seguridad y privacidad
│   ├── Confidencialidad — control de acceso basado en rol y relación de atención
│   │   └── Escenario: acceso no autorizado de un empleado sin rol clínico — (H, H)
│   ├── Protección de categorías especiales (salud mental, VIH, salud reproductiva)
│   │   └── Escenario: acceso a diagnóstico reforzado sin autorización adicional — (H, M)
│   └── Cifrado de datos en tránsito y en reposo
│       └── Escenario: intercepción de tráfico / backup sustraído — (H, L)
├── Disponibilidad
│   ├── Recuperación ante falla del servidor central
│   │   └── Escenario: caída del servidor primario durante guardia nocturna — (H, M)
│   └── Continuidad operativa ante corte de conectividad en el punto de atención
│       └── Escenario: pérdida de conexión WAN durante atención en curso — (M, H)
├── Rendimiento
│   ├── Tiempo de respuesta al consultar historia clínica en guardia
│   │   └── Escenario: consulta de historia de un paciente que ingresa por emergencia — (H, M)
│   └── Throughput durante campañas de carga masiva
│       └── Escenario: pico de registros durante campaña de vacunación — (M, M)
├── Interoperabilidad
│   ├── Intercambio de resultados de laboratorio vía HL7/FHIR
│   │   └── Escenario: incorporación de resultado externo a la historia clínica — (H, H)
│   └── Interoperabilidad con HCE de otras instituciones (red de salud)
│       └── Escenario: solicitud de resumen clínico por derivación de paciente — (M, H)
└── Auditabilidad y trazabilidad
    ├── Registro de accesos a la historia clínica
    │   └── Escenario: apertura de una historia clínica por cualquier usuario — (H, L)
    └── Trazabilidad de modificaciones al historial (integridad del registro)
        └── Escenario: corrección de un diagnóstico o medicación ya cargada — (H, M)
```

### Tabla (Atributo | Refinamiento | Escenario | Valor,Riesgo | Justificación)

| Atributo | Refinamiento | Escenario (6 partes) | (Valor, Riesgo) | Justificación |
|---|---|---|---|---|
| Seguridad y privacidad | Confidencialidad — control de acceso basado en rol | Fuente de estímulo: empleado interno sin rol clínico · Estímulo: intento de acceder a la historia clínica completa de un paciente sin relación de atención · Ambiente: operación normal · Artefacto: módulo de control de acceso de la HCE · Respuesta: el sistema deniega el acceso, registra el intento y notifica al oficial de seguridad de la información · Medida de respuesta: 100% de accesos no autorizados bloqueados, alerta generada en < 1 min | (H, H) | H de negocio: exponer datos sensibles de pacientes viola la normativa de protección de datos de salud (ej. HIPAA / Ley 25.326) y puede implicar pérdida de habilitación y demandas; H de riesgo: requiere un modelo de permisos granular por rol y relación médico-paciente que aún no está completamente definido |
| Seguridad y privacidad | Protección de categorías especiales (salud mental, VIH, salud reproductiva) | Fuente de estímulo: médico tratante sin autorización especial · Estímulo: solicitud de ver un diagnóstico marcado como "dato sensible reforzado" · Ambiente: operación normal, fuera del equipo tratante autorizado · Artefacto: sección de diagnósticos sensibles de la HCE · Respuesta: el sistema oculta el campo y exige autorización explícita adicional (doble autenticación o consentimiento del paciente) antes de mostrarlo · Medida de respuesta: 0% de exposición de campos reforzados sin autorización verificada, verificación adicional en < 5s | (H, M) | H de negocio: estas categorías tienen protección legal reforzada por el estigma y riesgo de discriminación asociado; M de riesgo: el patrón "break-the-glass" es conocido en sistemas de salud, aunque la taxonomía de categorías especiales del sistema todavía no está cerrada |
| Seguridad y privacidad | Cifrado de datos en tránsito y en reposo | Fuente de estímulo: atacante que intercepta tráfico de red o accede a un backup robado · Estímulo: intento de leer datos de pacientes interceptados o sustraídos · Ambiente: cualquier momento, incluso fuera de horario de atención · Artefacto: canal cliente-servidor y almacenamiento de backups · Respuesta: los datos interceptados o sustraídos son ilegibles sin la clave correspondiente · Medida de respuesta: 100% del tráfico con TLS 1.2+ y 100% de backups cifrados con AES-256 | (H, L) | H de negocio: es un requisito regulatorio no negociable en el dominio de salud; L de riesgo: cifrado en tránsito y en reposo es práctica estándar con soluciones maduras (TLS, KMS) |
| Disponibilidad | Recuperación ante falla del servidor central | Fuente de estímulo: hardware/software del servidor de HCE · Estímulo: caída abrupta del servidor primario · Ambiente: guardia nocturna, alta criticidad · Artefacto: servidor de aplicación de historia clínica · Respuesta: failover automático a nodo de respaldo sin pérdida de sesión activa · Medida de respuesta: tiempo de conmutación < 30s, 0% de pérdida de datos ya confirmados | (H, M) | H de negocio: en guardia, no poder acceder a alergias/medicación compromete la seguridad del paciente; M de riesgo: patrón de failover conocido, pero no probado aún bajo carga real de guardia |
| Disponibilidad | Continuidad operativa ante corte de conectividad en el punto de atención | Fuente de estímulo: corte de conexión WAN del hospital · Estímulo: pérdida de conectividad con el servidor central durante una atención en curso · Ambiente: consultorio o guardia, atención en curso · Artefacto: cliente local de la HCE (modo offline) · Respuesta: el sistema permite consultar los últimos datos sincronizados y registrar notas localmente, sincronizando al reconectar · Medida de respuesta: datos de las últimas 24hs disponibles offline, sincronización completa en < 2 min al reconectar | (M, H) | M de negocio: mitiga un riesgo operativo real pero menos frecuente que una falla de servidor; H de riesgo: requiere arquitectura offline-first con resolución de conflictos, no implementada todavía |
| Rendimiento | Tiempo de respuesta al consultar historia clínica en guardia | Fuente de estímulo: médico de guardia usando la aplicación clínica · Estímulo: solicitud de ver la historia clínica completa de un paciente que ingresa por emergencia · Ambiente: carga pico de guardia (noche/fin de semana) · Artefacto: servicio de consulta de historia clínica · Respuesta: el sistema devuelve la historia completa incluyendo alergias y medicación actual · Medida de respuesta: tiempo de respuesta p95 < 1s bajo carga pico | (H, M) | H de negocio: en una emergencia, la demora en ver alergias/medicación puede costar vidas; M de riesgo: requiere optimización de consultas e índices, conocido pero no trivial con el volumen de historiales |
| Rendimiento | Throughput durante campañas de carga masiva | Fuente de estímulo: sistema de turnos/campaña de vacunación masiva · Estímulo: pico de 500 registros de atención por hora · Ambiente: horario de campaña extendida · Artefacto: servicio de registro de atenciones · Respuesta: el sistema procesa todos los registros sin degradar el tiempo de respuesta de otras consultas · Medida de respuesta: throughput sostenido de 500 registros/hora sin incremento de latencia p95 > 20% | (M, M) | M de negocio: importante en épocas de campaña pero no es una operación diaria; M de riesgo: dimensionamiento manejable con autoscaling estándar, aunque no probado a esa escala |
| Interoperabilidad | Intercambio de resultados de laboratorio vía HL7/FHIR | Fuente de estímulo: laboratorio externo · Estímulo: envío de un resultado de análisis clínico en formato HL7 FHIR · Ambiente: operación normal, integración con proveedor externo · Artefacto: adaptador de interoperabilidad de la HCE · Respuesta: el sistema valida, transforma e incorpora el resultado a la historia clínica del paciente correspondiente · Medida de respuesta: 100% de mensajes válidos incorporados en < 5 min, mensajes inválidos rechazados con notificación | (H, H) | H de negocio: la integración con laboratorios es requisito para operar en red de salud y evitar carga manual propensa a errores; H de riesgo: cada proveedor externo puede tener variantes del estándar FHIR, integración aún no estandarizada |
| Interoperabilidad | Interoperabilidad con HCE de otras instituciones (red de salud) | Fuente de estímulo: sistema de HCE de otra institución de salud · Estímulo: solicitud de acceso a resumen de historia clínica de un paciente derivado · Ambiente: derivación de paciente entre instituciones · Artefacto: API de intercambio de historia clínica (tipo HCEN) · Respuesta: el sistema comparte solo los datos autorizados por consentimiento del paciente, en formato estándar · Medida de respuesta: resumen clínico disponible para la institución receptora en < 2 min tras la solicitud autorizada | (M, H) | M de negocio: mejora la continuidad de atención pero aún no es obligatorio en todas las jurisdicciones; H de riesgo: depende de estándares nacionales de interoperabilidad y consentimiento, terreno poco explorado por el equipo |
| Auditabilidad y trazabilidad | Registro de accesos a la historia clínica | Fuente de estímulo: cualquier usuario del sistema (médico, administrativo, auditor) · Estímulo: apertura de una historia clínica de un paciente · Ambiente: operación normal · Artefacto: módulo de auditoría de accesos · Respuesta: el sistema registra usuario, paciente, fecha/hora y motivo de acceso de forma inmutable · Medida de respuesta: 100% de accesos registrados, log disponible para auditoría en < 1 min, sin posibilidad de edición o borrado | (H, L) | H de negocio: es requisito legal/regulatorio para auditorías e investigación de filtraciones; L de riesgo: el registro de logs de auditoría es un patrón bien conocido, ya usado en otros módulos |
| Auditabilidad y trazabilidad | Trazabilidad de modificaciones al historial (integridad del registro) | Fuente de estímulo: médico tratante · Estímulo: corrección de un diagnóstico o medicación ya cargada · Ambiente: operación normal · Artefacto: módulo de historia clínica · Respuesta: el sistema no sobrescribe el dato original, sino que crea una nueva versión conservando la anterior con autor y timestamp · Medida de respuesta: 100% de modificaciones versionadas, historial completo de cambios recuperable en < 2s | (H, M) | H de negocio: la integridad del registro médico es crítica legalmente (litigios por mala praxis) y clínicamente; M de riesgo: requiere rediseñar el modelo de datos a versionado, no trivial pero con patrones conocidos (event sourcing / append-only) |

### Candidatas a analizar primero (cuadrante H,H)

Dos hojas caen en (H,H) y deberían analizarse primero en una evaluación de arquitectura:

1. **Seguridad — Confidencialidad (control de acceso basado en rol):** es la hoja de mayor prioridad del árbol; dado que el sistema maneja datos sensibles de pacientes, un fallo de control de acceso es tanto crítico para el negocio (regulatorio/legal) como técnicamente incierto (modelo de permisos aún no definido).
2. **Interoperabilidad — Intercambio de resultados de laboratorio vía HL7/FHIR:** alto valor de negocio (requisito para operar en red) combinado con alto riesgo técnico (variantes no estandarizadas del protocolo entre proveedores).

Como puede verse, Seguridad y privacidad concentra el mayor peso del árbol (una hoja en H,H y las otras dos en H,M y H,L), consistente con que este es un sistema que maneja datos sensibles de pacientes.

## Veredicto

- ✅ Al menos 5 atributos distintos — el árbol tiene 5: Seguridad y privacidad, Disponibilidad, Rendimiento, Interoperabilidad, Auditabilidad y trazabilidad.
- ✅ Seguridad/privacidad aparece con peso fuerte (cuadrante H,H razonable) — tiene 3 refinamientos (vs. 2 en los demás atributos) y una hoja explícitamente en (H,H) (control de acceso basado en rol), señalada como candidata #1 a analizar primero.
- ✅ Refinamientos específicos del dominio salud (no genéricos) — ej. "protección de categorías especiales (salud mental, VIH)", "intercambio de resultados de laboratorio vía HL7/FHIR", "interoperabilidad con HCE de otras instituciones", "trazabilidad de modificaciones al historial clínico"; ninguno es un refinamiento genérico tipo "seguridad" o "rendimiento" sin contexto.
- ✅ Estructura y hojas igual de completas que en el resto de la suite (6 partes, H/M/L justificado) — cada una de las 11 hojas tiene las 6 partes explícitas (Fuente de estímulo, Estímulo, Ambiente, Artefacto, Respuesta, Medida de respuesta) separadas con "·", y cada puntaje (Valor, Riesgo) viene con su justificación de una línea en formato "H/M de negocio: ...; H/M/L de riesgo: ...".

**Resultado: PASA**
