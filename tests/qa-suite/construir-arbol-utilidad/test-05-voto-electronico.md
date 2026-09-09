## Prompt

Construí un árbol de utilidad para un sistema de votación electrónica, con foco fuerte en seguridad (integridad y confidencialidad del voto) y al menos otros 2 atributos.

## Criterios de aceptación esperados

- Seguridad aparece refinada en sub-categorías propias del dominio (ej. integridad del voto, confidencialidad/anonimato, verificabilidad) — no un solo nodo genérico de "seguridad".
- Al menos una hoja de integridad/confidencialidad del voto cae razonablemente en (H,H) y se señala como prioritaria.
- Al menos 3 atributos de calidad en total.

## Respuesta obtenida

Construí el árbol de utilidad para el sistema de votación electrónica. Como pediste, puse el foco fuerte en Seguridad refinándola en sub-categorías propias del dominio electoral (no la dejo como un nodo genérico), y agregué 3 atributos adicionales: Disponibilidad, Verificabilidad quedó integrada dentro de Seguridad porque en votación electrónica es, ante todo, una propiedad de seguridad del voto (que nadie pueda alterarlo sin ser detectado); además agregué Usabilidad y Rendimiento como los otros dos atributos de calidad.

### Árbol indentado

```
Utility
├── Seguridad
│   ├── Integridad del voto
│   │   └── Un atacante intercepta y modifica un voto en tránsito hacia el servidor de conteo — (H, H)
│   ├── Confidencialidad / anonimato del voto
│   │   └── Un administrador con acceso a la base de datos intenta vincular un voto almacenado con la identidad del votante — (H, H)
│   └── Verificabilidad end-to-end
│       └── Un votante usa su código de recibo para verificar que su voto fue registrado correctamente en el tablero público de auditoría — (H, M)
├── Disponibilidad
│   └── Continuidad de la votación ante falla de infraestructura
│       └── Falla por hardware en un servidor de votación durante horario pico de la jornada electoral — (H, M)
├── Usabilidad
│   └── Emisión del voto sin asistencia para votantes con baja alfabetización digital
│       └── Un votante adulto mayor sin experiencia previa con sistemas digitales completa el proceso de votación sin ayuda de un asistente — (M, M)
└── Rendimiento
    └── Throughput en horas pico de votación
        └── Múltiples votantes emiten su voto simultáneamente durante la hora de mayor concurrencia del día electoral — (M, L)
```

### Tabla

| Atributo | Refinamiento | Escenario (resumen 6 partes) | (Valor, Riesgo) | Justificación |
|---|---|---|---|---|
| Seguridad | Integridad del voto | Fuente de estímulo: atacante externo con capacidad de interceptar tráfico de red · Estímulo: intento de modificar el contenido de un voto en tránsito entre la terminal de votación y el servidor central de conteo · Ambiente: jornada electoral en curso, votación abierta · Artefacto: canal de comunicación terminal-servidor y el registro del voto · Respuesta: el sistema detecta cualquier alteración mediante firma/hash criptográfico y rechaza o marca el voto como inválido, sin permitir que se cuente un voto alterado · Medida de respuesta: 100% de los votos alterados detectados y rechazados, 0 votos modificados contabilizados como válidos | (H, H) | H de negocio: si se puede alterar un voto sin ser detectado, el resultado electoral pierde legitimidad y la elección puede anularse; H de riesgo: exige criptografía end-to-end robusta contra atacantes sofisticados, sin margen de error ni segunda oportunidad el día de la elección |
| Seguridad | Confidencialidad / anonimato del voto | Fuente de estímulo: administrador interno con acceso legítimo a la base de datos de votos · Estímulo: intento de vincular un registro de voto almacenado con la identidad del votante que lo emitió · Ambiente: operación normal, durante o después de la jornada electoral · Artefacto: base de datos de votos y de padrón de votantes · Respuesta: el sistema garantiza que ningún dato almacenado permite reconstruir la relación voto-votante, ni siquiera para un usuario con privilegios administrativos · Medida de respuesta: 0% de los votos re-identificables en auditoría interna o externa, verificado mediante prueba criptográfica de anonimato | (H, H) | H de negocio: el voto secreto es un derecho fundamental del proceso electoral, su violación invalida la elección y expone a votantes a coerción o represalias; H de riesgo: separar identidad y contenido del voto de forma criptográficamente verificable (mixnets, firmas ciegas) es técnicamente complejo y difícil de auditar con certeza absoluta |
| Seguridad | Verificabilidad end-to-end | Fuente de estímulo: votante que ya emitió su voto · Estímulo: el votante ingresa su código de recibo para comprobar que su voto fue registrado tal como lo emitió · Ambiente: período posterior al cierre de la votación, antes de la publicación de resultados finales · Artefacto: tablero público de auditoría (bulletin board) · Respuesta: el sistema muestra evidencia verificable de que el voto fue incluido correctamente en el conteo, sin revelar por quién votó · Medida de respuesta: verificación disponible en menos de 5 segundos para el 100% de los votantes que la soliciten, sin exponer el contenido del voto a terceros | (H, M) | H de negocio: sin verificabilidad pública no hay confianza social en el resultado, aunque el sistema sea técnicamente correcto; M de riesgo: existen esquemas conocidos en la literatura (ej. Benaloh challenge) que reducen la incertidumbre técnica, aunque implementarlos de forma usable no es trivial |
| Disponibilidad | Continuidad de la votación ante falla de infraestructura | Fuente de estímulo: hardware de un servidor de votación · Estímulo: falla por crash del servidor durante la jornada · Ambiente: horario pico de votación, sistema bajo carga alta · Artefacto: servidor de votación / servicio de registro de votos · Respuesta: failover automático a un nodo de respaldo sin pérdida de votos ya emitidos · Medida de respuesta: tiempo de conmutación menor a 30 segundos, 0 votos perdidos o duplicados | (H, M) | H de negocio: una caída durante la jornada electoral puede forzar la extensión o repetición de la elección, con un costo político y de legitimidad enorme; M de riesgo: el failover es un patrón conocido y probable de haber sido probado en ensayos previos, aunque la presión de un evento único sin margen de reintento lo mantiene en riesgo medio |
| Usabilidad | Emisión del voto sin asistencia para votantes con baja alfabetización digital | Fuente de estímulo: votante adulto mayor sin experiencia previa con sistemas digitales · Estímulo: inicia el proceso de emisión de su voto en la terminal · Ambiente: jornada electoral, uso real sin capacitación previa · Artefacto: interfaz de la terminal/aplicación de votación · Respuesta: el votante completa todo el proceso de votación sin necesitar ayuda de un asistente · Medida de respuesta: al menos 90% de los votantes de este perfil completan el voto sin asistencia, en menos de 3 minutos y con 0 votos inválidos por error de interfaz | (M, M) | M de negocio: importante para la participación y legitimidad del proceso, pero no impide la elección si una minoría requiere asistencia humana; M de riesgo: requiere pruebas de usabilidad con población real representativa, no se resuelve solo con buen diseño visual |
| Rendimiento | Throughput en horas pico de votación | Fuente de estímulo: población de votantes concurriendo a votar · Estímulo: pico de solicitudes simultáneas de emisión de voto · Ambiente: hora de mayor concurrencia del día electoral (ej. mediodía) · Artefacto: servicio de registro y conteo de votos · Respuesta: el sistema procesa cada voto sin generar colas ni rechazos · Medida de respuesta: tiempo de respuesta p95 menor a 3 segundos por voto, soportando al menos 5.000 votos/minuto sin degradación | (M, L) | M de negocio: colas largas desalientan la participación y generan quejas públicas, pero no invalidan la elección por sí solas; L de riesgo: patrón de escalado horizontal conocido, ya validado en sistemas transaccionales de volumen similar |

### Candidatas a analizar primero en la evaluación de arquitectura (cuadrante H,H)

Las dos hojas de Seguridad **Integridad del voto** y **Confidencialidad/anonimato del voto** caen en (H,H) y son las candidatas prioritarias para la evaluación de arquitectura (ej. ATAM): son a la vez las de mayor valor de negocio (sin ellas la elección pierde legitimidad) y de mayor riesgo técnico (requieren mecanismos criptográficos complejos y sin margen de error). La hoja de Disponibilidad (H, M) y la de Verificabilidad (H, M) son de alto valor pero de riesgo medio, por lo que se ubican en segundo orden de prioridad. Usabilidad (M, M) y Rendimiento (M, L) quedan para etapas posteriores del análisis.

## Veredicto

- Seguridad aparece refinada en sub-categorías propias del dominio (integridad del voto, confidencialidad/anonimato, verificabilidad) y no como un nodo genérico. ✅ — Se generaron 3 refinamientos específicos del dominio electoral (integridad, confidencialidad/anonimato, verificabilidad end-to-end), cada uno con su propia hoja de 6 partes.
- Al menos una hoja de integridad/confidencialidad del voto cae razonablemente en (H,H) y se señala como prioritaria. ✅ — Tanto "Integridad del voto" como "Confidencialidad/anonimato del voto" quedaron puntuadas (H,H) y se las señala explícitamente como candidatas prioritarias en el cierre.
- Al menos 3 atributos de calidad en total. ✅ — Se incluyeron 4 atributos: Seguridad, Disponibilidad, Usabilidad y Rendimiento.

**Resultado: PASA**
