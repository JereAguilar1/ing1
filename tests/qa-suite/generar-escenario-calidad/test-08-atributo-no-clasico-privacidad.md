## Prompt

Quiero generar escenarios para el atributo "privacidad de datos por diseño" (privacy by design) en una app de salud, que tampoco es uno de los atributos clásicos del libro.

## Criterios de aceptación esperados

- No rechaza el pedido por no ser un atributo clásico.
- Sigue visiblemente el mini-proceso de 3 pasos: refinar en sub-características → 2-3 escenarios concretos de ejemplo → generalizar.
- Fuente de estímulo y Ambiente están explícitos en cada uno de los escenarios concretos (este es el gap que el baseline original sin skill tenía en atributos no clásicos).
- Medidas cuantificables y propias del dominio (ej. % de datos anonimizados, tiempo de respuesta a un pedido de borrado de datos, cantidad de accesos no autorizados detectados).

## Respuesta obtenida

"Privacidad de datos por diseño" no es uno de los atributos clásicos del libro (disponibilidad, interoperabilidad, modificabilidad, rendimiento, seguridad, testabilidad, usabilidad), así que en vez de forzarlo en esas plantillas o inventar algo sin proceso, sigo el mini-proceso del Cap. 4: (a) refinar el atributo en sub-características, (b) construir 2-3 escenarios concretos de ejemplo para una app de salud, y (c) generalizar a partir de ellos.

### a) Refinar "privacidad de datos por diseño" en sub-características

Para una app de salud, propongo entender este atributo como la combinación de:

1. **Minimización de datos**: recolectar y retener solo los datos estrictamente necesarios para la finalidad declarada.
2. **Anonimización / seudonimización**: los datos sensibles (historia clínica, diagnósticos) se despersonalizan antes de usos secundarios (analítica, investigación, reportes).
3. **Control del titular sobre sus datos**: derechos de acceso, rectificación, portabilidad y borrado ("derecho al olvido") ejercibles por el paciente.
4. **Control de acceso y auditoría**: solo personal autorizado accede a datos identificables, y todo acceso queda registrado y es detectable si es indebido.
5. **Protección en tránsito y en reposo**: cifrado de los datos sensibles en todo su ciclo de vida.

(Si el usuario tiene otra definición en mente —por ejemplo, enfocada solo en cumplimiento normativo tipo HIPAA/GDPR— este es el punto donde ajustaría el refinamiento; lo marco como supuesto de trabajo.)

### b) Escenarios concretos de ejemplo

**Escenario concreto 1 — Derecho al borrado (sub-característica 3)**

| Parte | Valor |
|---|---|
| Fuente de estímulo | Un paciente autenticado en la app |
| Estímulo | Solicita el borrado ("derecho al olvido") de todos sus datos personales y clínicos |
| Ambiente | Operación normal, cuenta activa, sin trámites administrativos o legales pendientes sobre esos datos |
| Artefacto | El módulo de gestión de solicitudes de derechos sobre datos personales (data subject rights) |
| Respuesta | El sistema verifica identidad, elimina o anonimiza irreversiblemente los datos identificables en todos los subsistemas (BD, backups activos, logs) y confirma al paciente |
| Medida de respuesta | Confirmación de recepción de la solicitud en ≤ 72 horas; eliminación/anonimización efectiva completa en ≤ 30 días corridos; 100% de los registros identificables removidos |

**Escenario concreto 2 — Anonimización para uso secundario (sub-características 1 y 2)**

| Parte | Valor |
|---|---|
| Fuente de estímulo | Un proceso batch interno del equipo de analítica/investigación clínica |
| Estímulo | Solicita exportar un dataset de historias clínicas para un estudio agregado |
| Ambiente | Ejecución periódica (job nocturno), fuera del flujo de atención en tiempo real |
| Artefacto | El pipeline de anonimización/seudonimización de datos previo a la exportación |
| Respuesta | El sistema remueve o seudonimiza todos los identificadores directos e indirectos antes de exportar, aplicando un umbral mínimo de k-anonimato |
| Medida de respuesta | 100% de identificadores directos (nombre, DNI, contacto) removidos; k-anonimato ≥ 5 en el dataset exportado; riesgo de re-identificación estimado < 1% |

**Escenario concreto 3 — Acceso no autorizado a historia clínica (sub-característica 4)**

| Parte | Valor |
|---|---|
| Fuente de estímulo | Un usuario interno (ej. personal administrativo) sin permiso clínico asignado |
| Estímulo | Intenta acceder a la historia clínica completa de un paciente fuera de su rol/alcance de atención |
| Ambiente | Operación normal, sistema en producción, 24/7 |
| Artefacto | El módulo de control de acceso basado en roles y el log de auditoría de accesos a datos clínicos |
| Respuesta | El sistema deniega el acceso, registra el intento en el log de auditoría y genera una alerta al equipo de seguridad/privacidad |
| Medida de respuesta | Tiempo de detección y bloqueo < 5 segundos; 100% de los intentos de acceso indebido quedan registrados en el log; 0 accesos no autorizados exitosos tolerados por mes |

### c) Generalización — Escenario general de "privacidad de datos por diseño"

Tomando en conjunto los estímulos, artefactos y medidas de los tres escenarios anteriores:

| Parte | Valor |
|---|---|
| Fuente de estímulo | El titular de los datos (paciente), un proceso interno del sistema (analítica/exportación/backup) o un actor interno/externo que intenta acceder sin autorización |
| Estímulo | Una solicitud de ejercicio de derechos sobre datos personales (acceso, rectificación, borrado, portabilidad), un requerimiento de procesamiento/exportación de datos sensibles para un fin secundario, o un intento de acceso indebido a datos identificables |
| Ambiente | Operación normal del sistema, en cualquier etapa del ciclo de vida del dato (recolección, procesamiento, almacenamiento, exportación o eliminación) |
| Artefacto | El subsistema de gestión de datos personales/clínicos (módulo de derechos del titular, pipeline de anonimización, o control de acceso y auditoría) |
| Respuesta | El sistema aplica el principio de minimización/anonimización correspondiente, ejecuta o deniega la operación según corresponda, y registra la acción en el log de auditoría |
| Medida de respuesta | Tiempo de respuesta a solicitudes de derechos ≤ 30 días (confirmación ≤ 72 h); ≥ 99% de los identificadores directos removidos/anonimizados antes de cualquier uso secundario; tiempo de detección y bloqueo de accesos indebidos < 5 s, con 100% de los intentos registrados en auditoría |

**Prosa (general):** "Cuando un paciente ejerce un derecho sobre sus datos, un proceso interno solicita exportarlos para un fin secundario, o un actor intenta acceder a ellos sin autorización, el sistema de gestión de datos clínicos debe responder aplicando minimización/anonimización, ejecutando o denegando la operación y dejando registro de auditoría, cumpliendo con tiempos de respuesta ≤ 30 días para solicitudes de derechos, ≥ 99% de anonimización efectiva y detección/bloqueo de accesos indebidos en menos de 5 segundos."

**Prosas (concretas):**
- Escenario 1: "Cuando un paciente solicita el borrado de sus datos, el módulo de derechos del titular debe confirmar la solicitud en menos de 72 horas y eliminar o anonimizar el 100% de sus datos identificables en un plazo máximo de 30 días."
- Escenario 2: "Cuando el proceso de analítica solicita exportar historias clínicas para un estudio, el pipeline de anonimización debe remover el 100% de los identificadores directos y garantizar un k-anonimato ≥ 5 antes de la exportación."
- Escenario 3: "Cuando un usuario sin permiso clínico intenta acceder a una historia clínica, el control de acceso debe bloquear el intento y registrarlo en auditoría en menos de 5 segundos, sin tolerar accesos no autorizados exitosos."

*Supuestos marcados*: los valores numéricos (72 h, 30 días, k≥5, <5 s) son propuestas ilustrativas alineadas a plazos típicos de regulaciones de protección de datos en salud (tipo GDPR/HIPAA); si el usuario tiene SLAs o normativa local específica, deberían ajustarse.

## Veredicto

- **No rechaza el pedido por no ser un atributo clásico.** ✅ La respuesta explicita que no es un atributo clásico y aplica el mini-proceso del Cap. 4 en vez de negarse.
- **Sigue visiblemente el mini-proceso de 3 pasos: refinar en sub-características → 2-3 escenarios concretos de ejemplo → generalizar.** ✅ La respuesta está estructurada en secciones "a) Refinar...", "b) Escenarios concretos..." (3 escenarios) y "c) Generalización...", en ese orden.
- **Fuente de estímulo y Ambiente están explícitos en cada uno de los escenarios concretos.** ✅ Los tres escenarios concretos tienen filas "Fuente de estímulo" y "Ambiente" con valores concretos y no vacíos (paciente autenticado/operación normal; proceso batch/ejecución periódica nocturna; usuario interno sin permiso/operación normal 24-7).
- **Medidas cuantificables y propias del dominio.** ✅ Cada escenario concreto y el general tienen medidas numéricas específicas del dominio de salud/privacidad (≤72h de confirmación, ≤30 días de borrado, 100% de identificadores removidos, k-anonimato ≥5, riesgo de re-identificación <1%, detección/bloqueo <5s, 0 accesos no autorizados tolerados).

**Resultado: PASA**
