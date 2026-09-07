## Prompt

Quiero un árbol de utilidad para un sistema de atención al cliente. La meta de negocio es "reducir el tiempo de resolución de reclamos en un 30%". No te digo qué atributos de calidad usar, inferilos vos.

## Criterios de aceptación esperados

- La skill explica cómo derivó los atributos de calidad a partir de la meta de negocio dada (no los lista sin justificar de dónde salen).
- Mínimo 3-4 atributos razonables para esa meta (ej. rendimiento, usabilidad, posiblemente disponibilidad o modificabilidad).
- Estructura y hojas completas como en el resto de la suite.

## Respuesta obtenida

### Cómo derivé los atributos de calidad

Partiendo de la meta de negocio **"reducir el tiempo de resolución de reclamos en un 30%"**, descompuse qué componentes técnicos determinan ese tiempo de extremo a extremo, y de ahí salieron los atributos candidatos:

- **Rendimiento** — el tiempo de resolución incluye directamente los tiempos de procesamiento internos del sistema (asignar el reclamo a un agente, recuperar el historial del cliente, etc.). Si esos pasos son lentos, el tiempo total sube sin importar qué tan bueno sea el agente.
- **Usabilidad** — parte del tiempo de resolución es el tiempo que el agente humano tarda en operar el sistema (navegar pantallas, buscar información, decidir la respuesta). Una interfaz ineficiente agrega minutos por reclamo que se multiplican por volumen.
- **Disponibilidad** — el tiempo de resolución se mide de punta a punta; si el sistema de gestión de reclamos cae, los tickets en curso quedan bloqueados y el tiempo promedio se dispara. Reducir el tiempo un 30% exige que el sistema esté operativo de forma consistente.
- **Modificabilidad** — para sostener (y seguir mejorando) esa reducción del 30%, el negocio va a necesitar ajustar reglas de enrutamiento/priorización de reclamos con frecuencia (por tipo de cliente, por SLA, por categoría). Si el sistema es rígido, cada ajuste demora y frena la iteración hacia la meta.

Con esto quedan 4 atributos, todos trazables a la meta de negocio dada (no son una lista genérica).

### Árbol de utilidad (vista indentada)

```
Utility
├── Rendimiento
│   ├── Tiempo de asignación automática de reclamos a agentes
│   │   └── [Escenario] Asignación de ticket bajo carga promedio — (H, M)
│   └── Tiempo de respuesta al consultar historial del cliente
│       └── [Escenario] Consulta de historial en horario pico — (H, L)
├── Usabilidad
│   ├── Eficiencia de la interfaz de gestión de reclamos
│   │   └── [Escenario] Agente nuevo completa el ciclo de un reclamo típico — (H, M)
│   └── Feedback de próximos pasos sugeridos
│       └── [Escenario] Sugerencia de acción según categoría del reclamo — (M, M)
├── Disponibilidad
│   └── Continuidad operativa ante fallas del sistema
│       └── [Escenario] Failover automático tras caída de servidor — (H, M)
└── Modificabilidad
    └── Ajuste de reglas de enrutamiento/priorización de reclamos
        └── [Escenario] Cambio de regla de priorización sin tocar otros módulos — (M, L)
```

### Árbol de utilidad (tabla)

| Atributo | Refinamiento | Escenario (6 partes) | (Valor, Riesgo) | Justificación |
|---|---|---|---|---|
| Rendimiento | Tiempo de asignación automática de reclamos a agentes | Fuente de estímulo: cliente que envía un nuevo reclamo · Estímulo: creación de un ticket de reclamo · Ambiente: operación normal, carga promedio · Artefacto: motor de asignación/enrutamiento de tickets · Respuesta: el sistema asigna automáticamente el ticket al agente disponible más adecuado según categoría y carga · Medida de respuesta: tiempo de asignación < 5 segundos en el 95% de los casos | (H, M) | H de negocio: la asignación rápida reduce directamente el tiempo total de resolución, clave para la meta del 30%; M de riesgo: requiere reglas de enrutamiento con datos de disponibilidad de agentes en tiempo real, complejidad moderada |
| Rendimiento | Tiempo de respuesta al consultar historial del cliente | Fuente de estímulo: agente de atención al cliente · Estímulo: solicitud de ver historial de reclamos e interacciones previas del cliente · Ambiente: horario pico de atención · Artefacto: servicio de consulta de historial del cliente · Respuesta: el sistema devuelve el historial completo del cliente · Medida de respuesta: tiempo de respuesta p95 < 1 segundo bajo carga pico | (H, L) | H de negocio: sin historial rápido el agente pierde tiempo buscando contexto, alargando la resolución; L de riesgo: es una consulta de lectura estándar, patrón ya conocido |
| Usabilidad | Eficiencia de la interfaz de gestión de reclamos | Fuente de estímulo: agente de atención al cliente (nuevo, 1 semana de entrenamiento) · Estímulo: necesidad de completar el ciclo de resolución de un reclamo típico (clasificar, resolver, cerrar) · Ambiente: operación normal · Artefacto: interfaz de gestión de reclamos · Respuesta: el agente completa el flujo sin asistencia externa · Medida de respuesta: tiempo para completar el flujo ≤ 4 minutos, con ≤ 1 error de navegación | (H, M) | H de negocio: una interfaz eficiente reduce el tiempo por reclamo, aporte directo a la meta del 30%; M de riesgo: requiere rediseño de UX y pruebas con usuarios reales, no trivial |
| Usabilidad | Feedback de próximos pasos sugeridos | Fuente de estímulo: agente de atención al cliente · Estímulo: apertura de un reclamo ya categorizado automáticamente · Ambiente: operación normal · Artefacto: módulo de sugerencias/next-best-action · Respuesta: el sistema muestra los pasos recomendados y plantillas de respuesta según la categoría del reclamo · Medida de respuesta: el agente selecciona una acción sugerida en ≥ 80% de los casos, reduciendo el tiempo de decisión | (M, M) | M de negocio: ayuda a estandarizar y acelerar, pero no es indispensable si el agente ya es experimentado; M de riesgo: requiere lógica de recomendación que puede no ser precisa al inicio |
| Disponibilidad | Continuidad operativa ante fallas del sistema | Fuente de estímulo: falla de hardware/software en el servidor del sistema de gestión de reclamos · Estímulo: caída inesperada del servicio · Ambiente: horario de atención (horas hábiles) · Artefacto: servidor/servicio de gestión de reclamos · Respuesta: failover automático a instancia de respaldo, los agentes siguen trabajando · Medida de respuesta: tiempo de recuperación < 2 minutos, 0 pérdida de tickets en curso | (H, M) | H de negocio: cada minuto de caída se traduce directamente en más tiempo de resolución acumulado, contradiciendo la meta; M de riesgo: mecanismo de failover conocido pero depende de infraestructura existente |
| Modificabilidad | Ajuste de reglas de enrutamiento/priorización de reclamos | Fuente de estímulo: gerente de atención al cliente · Estímulo: pedido de cambiar la regla de priorización de reclamos (ej. priorizar por tipo de cliente o por SLA) · Ambiente: tiempo de diseño, fuera de producción · Artefacto: módulo de reglas de enrutamiento · Respuesta: se modifica/agrega la regla sin tocar el módulo de asignación de agentes ni el de reporting · Medida de respuesta: cambio confinado a 1 componente, implementado y desplegado en ≤ 8 horas-persona | (M, L) | M de negocio: mejora la capacidad de la empresa de iterar hacia el objetivo del 30%, pero no es la palanca principal; L de riesgo: el módulo de reglas ya está diseñado como configurable |

### Candidatas a analizar primero

Ninguna hoja cae en el cuadrante estricto (H,H). Las candidatas más cercanas — y las que recomiendo analizar primero en una evaluación de arquitectura — son las tres de valor de negocio H con riesgo M: **"Tiempo de asignación automática de reclamos a agentes"** (Rendimiento), **"Eficiencia de la interfaz de gestión de reclamos"** (Usabilidad) y **"Continuidad operativa ante fallas del sistema"** (Disponibilidad). Son las que más impactan la meta del 30% y donde todavía hay incertidumbre técnica real sobre cómo lograrlas.

## Veredicto

- ✅ La skill explica cómo derivó los atributos de calidad a partir de la meta de negocio dada (no los lista sin justificar de dónde salen) — la sección "Cómo derivé los atributos de calidad" traza cada uno de los 4 atributos (Rendimiento, Usabilidad, Disponibilidad, Modificabilidad) directamente a la meta de reducir el tiempo de resolución en un 30%, con el razonamiento explícito de por qué cada uno afecta ese tiempo.
- ✅ Mínimo 3-4 atributos razonables para esa meta (ej. rendimiento, usabilidad, posiblemente disponibilidad o modificabilidad) — se entregaron exactamente los 4 atributos sugeridos como ejemplo en el criterio (rendimiento, usabilidad, disponibilidad, modificabilidad), todos pertinentes al dominio de atención al cliente.
- ✅ Estructura y hojas completas como en el resto de la suite — el árbol tiene la raíz Utility, atributos, refinamientos específicos del sistema (no genéricos) y 6 hojas, cada una con las 6 partes del escenario separadas (Fuente de estímulo, Estímulo, Ambiente, Artefacto, Respuesta, Medida de respuesta), puntuadas (Valor, Riesgo) con justificación de una línea, entregadas en formato árbol indentado y en tabla, y con el cierre señalando candidatas para la evaluación de arquitectura.

**Resultado: PASA**
