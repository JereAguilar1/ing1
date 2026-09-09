# Testing de las skills: niveles, cobertura y resultados

Este documento organiza **todo** lo que se probó sobre las 3 skills del plugin y qué salió de cada prueba, mirado desde el ángulo que importa para este método: ¿los escenarios que producen/auditan quedan completos según las 6 partes del SEI? ¿los atributos de calidad quedan bien identificados y no mezclados? Para el proceso de *cómo* se construyeron las skills (RED-GREEN-REFACTOR), ver [`proceso-creacion-skills.md`](./proceso-creacion-skills.md).

## 1. Los 4 niveles de testing usados

No hay "tests" en el sentido de un framework de código — no hay assertions automáticas. Cada test es un **despacho a un subagente con memoria limpia** (sin haber visto esta conversación ni la skill previamente) al que se le da un prompt fijo; su respuesta completa se guarda como archivo `.md` de evidencia y se audita contra criterios de aceptación escritos *antes* de correr el test.

| Nivel | Carpeta | Qué mide | Cuántos casos |
|---|---|---|---|
| **Baseline (RED)** | `tests/baseline/` | Cómo responde Claude a la misma consigna **sin ninguna skill activa** — la falla de referencia que la skill debe corregir | 4 |
| **Post (GREEN)** | `tests/post/` | Cómo responde la skill recién escrita a esa misma consigna | 4 |
| **Refactor (presión)** | `tests/refactor/` | Casos diseñados para explotar un atajo/loophole específico que la skill podría tomar sin violar la letra de sus instrucciones | 3 |
| **QA Suite (regresión)** | `tests/qa-suite/<skill>/` | Batería amplia de regresión, 8 casos por skill, sobre dominios y gaps que ningún test anterior había cubierto | 24 |

Usar un subagente fresco en cada corrida es la parte que hace confiable el test: si la skill se prueba en la misma conversación donde se la escribió, el modelo "ya sabe" qué se espera de memoria de la conversación, y el test deja de medir si la skill *por sí sola* (solo con lo que dice su `SKILL.md`) induce el comportamiento correcto.

## 2. Resumen de RED vs. GREEN (4 casos) y presión (3 casos)

El detalle caso por caso ya está escrito en `tests/COMPARISON.md` (no se duplica acá). En síntesis:

- **Caso 1** (`generar-escenario-calidad`, disponibilidad): el baseline entregaba un menú de opciones por parte en vez de un valor cerrado, y la medida de respuesta numérica solo aparecía fuera de la tabla formal. GREEN cierra la tabla con un valor único y la medida (`< 10 s`, `< 5 min`, `≥ 99.9%`) queda dentro de la fila formal.
- **Caso 2** (`chequear-completitud-escenario`, mesa de ayuda): el baseline mezclaba huecos de Respuesta dentro de la fila de Medida de respuesta. GREEN evalúa cada fila de forma independiente.
- **Caso 3** (`construir-arbol-utilidad`, ATM): las 12 hojas del baseline fusionaban las 6 partes en una oración narrativa, sin justificar los puntajes H/M/L. GREEN separa y rotula cada parte y agrega una columna de justificación.
- **Caso 4** (`generar-escenario-calidad`, atributo no clásico): el baseline omitía Fuente/Ambiente en escenarios "técnicos" (no regulatorios). GREEN los mantiene explícitos en el 100% de los escenarios entregados.
- **Casos 5-7** (presión): las 3 skills, además de cumplir lo pedido, señalan explícitamente el loophole metodológico de cada escenario (medida no cuantificable disfrazada + mezcla de atributos; mini-proceso no sobreajustado a un solo dominio; árbol de una sola hoja que no sirve para priorizar) en vez de obedecer ciegamente.

**Resultado de estos 7 casos: 7/7 con la falla RED corregida, sin fallas nuevas detectadas.**

## 3. QA Suite — batería de regresión (24 casos, 8 por skill)

Cada test de esta batería sigue el mismo formato: `## Prompt` → `## Criterios de aceptación esperados` → `## Respuesta obtenida` (la respuesta completa del subagente) → `## Veredicto` (chequeo criterio por criterio + resultado PASA/FALLA). El archivo completo de cada caso vive en `tests/qa-suite/<skill>/test-NN-<slug>.md`; acá se resume qué gap probaba cada uno y qué salió.

### 3.1 `chequear-completitud-escenario` (8/8 PASA)

| # | Caso | Escenario de entrada (resumen) | Qué probaba | Resultado desde la óptica de completitud |
|---|---|---|---|---|
| 01 | Seguridad muy incompleto | "El sistema debe protegerse de ataques" | Un enunciado casi sin ninguna de las 6 partes — ¿la skill lo marca todo, o solo lo obvio? | 6/6 partes ❌; señala explícitamente que no es un escenario/ASR utilizable; propone redacción concreta para cada una de las 6. |
| 02 | Rendimiento con medida vaga | 1000 usuarios en Black Friday, "responder de manera eficiente" | Falso positivo parcial: 5 partes están bien, solo la medida es vaga — ¿la skill sigue exigiendo rigor solo donde falta? | Fuente/Estímulo/Ambiente/Artefacto ✅, Respuesta ✅; Medida ❌ ("eficiente" sin número); propone p95 < 500 ms, error < 1%, throughput ≥ 200 req/s. |
| 03 | Escenario ya completo | Sensor de temperatura de data center, 30°C→25°C en <10 min, 95% de casos | Test de **falso positivo real**: ¿la skill inventa huecos que no existen? | 6/6 ✅; concluye explícitamente que el escenario está completo, sin proponer cambios innecesarios. |
| 04 | Mezcla de atributos | "Agregar un tipo de reporte... implementarlo rápido y sin bugs" | Modificabilidad y rendimiento/confiabilidad mezclados en una oración | Detecta la mezcla y recomienda separar en 2 escenarios; Fuente/Ambiente ❌, Artefacto ⚠️, Respuesta ❌, Medida ❌ (evaluado como escenario de modificabilidad, la lectura principal). |
| 05 | Disponibilidad, Fuente/Ambiente omitidos | "El servidor de BD falla y el sistema debe recuperarse automáticamente" | El gap más frecuente detectado en RED (Fuente/Ambiente salteados) | Ambiente ❌, Fuente ⚠️, Respuesta ⚠️, Medida ❌; propone tipo de falla, ventana operativa, plan de failover y umbrales (MTTR < 30 s, ≥ 99%, RPO ≤ 5 s). |
| 06 | Usabilidad, artefacto genérico | "Un usuario nuevo... el sistema debe guiarlo" | "El sistema" como artefacto genérico cuando el contexto sugiere un flujo puntual | Artefacto ⚠️ ("el sistema" → propone "el flujo de checkout"); Medida ❌ (sin tasa de finalización/tiempo/error); resto ⚠️ por intención sin evento/comportamiento concreto. |
| 07 | Estímulo confundido con respuesta | "El sistema valida el formulario y muestra un mensaje de error" | El texto describe la Respuesta, no hay Estímulo real | Marca Estímulo ❌ explicando la confusión, propone el evento real ("el usuario envía el formulario con datos inválidos"); explica el problema en una sección dedicada, no solo en la celda. |
| 08 | Atributo no clásico: compliance | Nueva regulación de protección de datos, "el sistema debe adaptarse para cumplirla" | ¿Baja el estándar de análisis por no ser uno de los 7 atributos del libro? | Aclara que el template es agnóstico al atributo; Ambiente ❌, Artefacto ⚠️, Medida ❌; propone plazo de 90 días y medida verificable (100% de flujos auditados, checklist firmado por el DPO). |

### 3.2 `generar-escenario-calidad` (8/8 PASA)

| # | Caso | Pedido | Qué probaba | Resultado desde la óptica de completitud |
|---|---|---|---|---|
| 01 | Disponibilidad, banca móvil | Pico de pago de sueldos fin de mes | Ambiente = pico real, no genérico; medida numérica dentro de la tabla | 6/6 filas cerradas; Ambiente nombra explícitamente la ventana de fin de mes (10x carga); Medida con 4 números (99.95%, <30s, <2min, 0%). |
| 02 | Modificabilidad, método de pago | Agregar billetera virtual sin tocar facturación | Artefacto correcto (módulo de pagos, no facturación) + medida de costo de cambio | Artefacto aclara explícitamente "(NO el módulo de facturación)"; Medida cuantifica horas-persona, componentes afectados y 0 archivos tocados en facturación (el supuesto de horas queda marcado como tal). |
| 03 | Rendimiento, reservas de vuelos | Cyber Monday de aerolíneas | Ambiente = carga pico explícita; Estímulo no confundido con Respuesta | Ambiente nombra el pico y deja `[A DEFINIR]` el factor exacto en vez de inventarlo; Estímulo es la llegada de la ráfaga, la Respuesta es el escalado — sin mezclarse. |
| 04 | Seguridad, API fintech | Intento de acceso no autorizado a cuentas | Fuente caracteriza al atacante, no queda "alguien" genérico | Fuente = "atacante externo no autenticado... sin credenciales válidas ni sesión activa"; Respuesta cubre detección + bloqueo + registro; Medida con 4 valores numéricos, marcados como supuestos a validar. |
| 05 | Usabilidad, adultos mayores (general) | Escenario general de accesibilidad | ¿Un escenario "general" sigue cerrando las 6 partes, o vuelve a listar opciones? | Se distingue explícitamente como general; las 6 filas están cerradas con un valor único; Medida con 3 números (80% finalización, <3 min, SUS ≥70). |
| 06 | Testabilidad, módulo de facturación ERP | Probar un incremento antes de integrar | Ambiente = contexto de CI/pre-merge explícito; Artefacto específico | Ambiente nombra CI y pre-merge/PR explícitamente; Artefacto es el módulo de facturación (no "el sistema"); Medida cubre cobertura, tiempo de suite y tiempo de aislar fallas. |
| 07 | Interoperabilidad, pasarela de pago | Integrar una pasarela externa | No confundirse con seguridad ni rendimiento puro | Fuente/Estímulo nombran explícitamente el protocolo de la pasarela; Medida cuantifica % transacciones correctas, tiempo de integración e incidentes de incompatibilidad; foco se mantiene en intercambio de datos, no en autenticación como fin en sí. |
| 08 | Atributo no clásico: privacidad por diseño | App de salud | Mini-proceso de 3 pasos (refinar → 2-3 ejemplos → generalizar) en un dominio nuevo, con foco en no perder Fuente/Ambiente | No rechaza el pedido; sigue las 3 etapas visiblemente; los 3 escenarios concretos y el general tienen Fuente y Ambiente explícitos; medidas propias del dominio (k-anonimato ≥5, borrado en ≤30 días, detección de accesos indebidos <5s). |

### 3.3 `construir-arbol-utilidad` (8/8 PASA)

| # | Caso | Dominio / pedido | Qué probaba | Resultado desde la óptica de completitud |
|---|---|---|---|---|
| 01 | E-commerce | Mínimo 4 atributos | Caso base: 4 atributos, hojas completas, cuadrante (H,H) señalado | Rendimiento/Disponibilidad/Seguridad/Modificabilidad, 8 hojas con las 6 partes separadas y justificación de 1 línea por score; 3 hojas en (H,H) identificadas. |
| 02 | Historia clínica electrónica | Mínimo 5 atributos, datos sensibles | ¿Seguridad/privacidad recibe el peso que el dominio exige? | 5 atributos; Seguridad y privacidad con 3 refinamientos (vs. 2 en el resto) y 1 hoja en (H,H); 11 hojas completas. |
| 03 | Trading de alta frecuencia | 3 atributos dados (rendimiento, disponibilidad, seguridad) | Refinamientos con latencia extrema (µs), no genéricos de "rendimiento" | Refinamientos en microsegundos (tick-to-trade p99 <50µs, jitter <10µs); 3 hojas en (H,H), una por atributo pedido; 9 hojas justificadas. |
| 04 | App de transporte tipo Uber | Mínimo 4, con disponibilidad + seguridad del usuario | Caso trabajado también en esta conversación — ¿el resultado es reproducible y completo? | 4 atributos con Disponibilidad y Seguridad del usuario explícitas; 9 hojas, 8 con score completo y 1 (accesibilidad) declarando explícitamente falta de información en vez de inventar el riesgo. |
| 05 | Voto electrónico | Foco fuerte en seguridad (integridad/confidencialidad) + 2 atributos | ¿Seguridad se refina en subcategorías propias del dominio electoral, o queda un nodo genérico? | 3 refinamientos de Seguridad (integridad, confidencialidad/anonimato, verificabilidad end-to-end), 2 en (H,H); 4 atributos totales. |
| 06 | IoT industrial | Disponibilidad/seguridad/modificabilidad mínimo | Refinamientos ligados a sensores/actuadores/redes de planta, no software genérico | Refinamientos sobre fieldbus/Profinet, actuadores OT, firmware de dispositivos de campo; agregó Rendimiento explicando el motivo; 11 hojas completas. |
| 07 | Atención al cliente | Solo meta de negocio dada ("reducir 30% el tiempo de resolución"), sin atributos | ¿La skill explica de dónde salen los atributos, o los tira sin justificar? | Sección dedicada trazando cada uno de los 4 atributos (Rendimiento, Usabilidad, Disponibilidad, Modificabilidad) a la meta de negocio; 6 hojas completas. |
| 08 | Videojuego online multijugador | Rendimiento (latencia) + escalabilidad + 2 más | Medidas en milisegundos/jugadores concurrentes; refinamientos de dominio gamer | Refinamientos de netcode/matchmaking/anti-cheat; medidas en ms (RTT ≤80ms p95) y jugadores concurrentes (hasta 100.000); 4 atributos, 8 hojas. |

## 4. Totales

| Nivel | Casos | Resultado |
|---|---|---|
| Baseline → GREEN (con falla RED corregida) | 4 | 4/4 |
| Presión / REFACTOR | 3 | 3/3 |
| QA Suite — `chequear-completitud-escenario` | 8 | 8/8 PASA |
| QA Suite — `generar-escenario-calidad` | 8 | 8/8 PASA |
| QA Suite — `construir-arbol-utilidad` | 8 | 8/8 PASA |
| **Total QA Suite** | **24** | **24/24 PASA** |

## 5. Qué patrones de gap cubre la suite en conjunto

Mirando los 24 casos como grupo (no skill por skill), los tipos de hueco que se pusieron a prueba repetidamente son:

- **Medida no cuantificable disfrazada de completa** ("eficiente", "rápido y seguro", "sin bugs") — casos 02/04 de completitud, base de todos los casos de generación (siempre exige número).
- **Fuente de estímulo y Ambiente omitidos u genéricos** — el gap más recurrente del baseline original; probado explícitamente en completitud (caso 05), generación (casos 01, 03, 06, 08) y en cada hoja de los 8 árboles de utilidad.
- **Artefacto genérico ("el sistema")** cuando el contexto sugiere un componente puntual — completitud caso 06, generación casos 02/06.
- **Estímulo confundido con Respuesta** (redactado como acción del sistema en vez de evento disparador) — completitud caso 07, chequeado también como criterio negativo en generación (caso 03).
- **Mezcla de dos atributos de calidad en un mismo escenario** — completitud caso 04.
- **Atributos "no clásicos" tratados con menos rigor que los 7 del libro** — completitud caso 08, generación caso 08.
- **Puntajes H/M/L sin justificación, u hojas de árbol degeneradas a una sola oración narrativa** — cubierto en todos los 8 casos de `construir-arbol-utilidad`, con foco particular en refinamientos específicos de dominio (no genéricos) y en no inventar un score cuando falta información (caso 04, hoja de accesibilidad).

## 6. Referencias

- Evidencia completa de cada test: `tests/baseline/`, `tests/post/`, `tests/refactor/`, `tests/qa-suite/<skill>/`.
- Comparación narrativa RED vs. GREEN de los 7 casos originales: `tests/COMPARISON.md`.
- Proceso de creación de las skills (metodología, restricciones de diseño, qué necesita cada skill): [`proceso-creacion-skills.md`](./proceso-creacion-skills.md).
