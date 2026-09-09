# Proceso de creación de las skills (metodología `writing-skills`)

Este documento explica **cómo se construyeron** las tres skills del plugin (no qué hacen — eso está en `README.md` — sino el proceso de trabajo que se siguió para crearlas y qué necesita cada una para funcionar).

## 1. Qué es una skill de Claude Code, en este contexto

Una skill es una carpeta autocontenida (`SKILL.md` + opcionalmente `references/`) que le enseña a Claude un procedimiento específico: cuándo activarse (frontmatter `description`) y qué pasos seguir. No es código que se ejecuta — es una instrucción estructurada que el modelo sigue como si fuera un experto en ese procedimiento.

Este plugin (`sei-quality-attributes`) tiene 3 skills hermanas, cada una independiente (sin dependencias de archivo entre sí — si una necesita a otra, la referencia **por nombre**, nunca por path):

| Skill | Rol |
|---|---|
| `generar-escenario-calidad` | Redacta escenarios nuevos con el template de 6 partes del SEI |
| `chequear-completitud-escenario` | Audita un escenario ya escrito contra esas 6 partes |
| `construir-arbol-utilidad` | Arma un árbol de utilidad (ATAM) a partir de varios escenarios priorizados |

## 2. La metodología: `superpowers:writing-skills` con ciclo RED → GREEN → REFACTOR

Este es el "creador de skills" que se usó — no es una skill que genera otras skills automáticamente, sino una **disciplina de trabajo tipo TDD aplicada a instrucciones en lenguaje natural**, con una ley de hierro: *ninguna skill se da por terminada (ni se commitea) antes de pasar su propio test en GREEN*.

### RED — capturar la falla real, sin la skill

Antes de escribir una sola línea de `SKILL.md`, se le dieron 4 prompts a un Claude "limpio" (sin ninguna skill activa) y se guardó su respuesta cruda en `tests/baseline/caso-{1..4}.md`. Cada respuesta se autoauditó contra el template estricto de 6 partes, con una sección "Autocrítica" al final.

Esto no es opcional ni cosmético: sin este paso no hay forma de saber si la skill realmente **corrige** algo o si solo agrega texto. El baseline reveló 5 patrones de falla concretos (ver §4) que se convirtieron en los objetivos de diseño de las 3 skills.

### GREEN — escribir la skill y probarla en frío

Para cada skill, en este orden:

1. Se escribió `SKILL.md` (y su `references/*.md` si aplica) apuntando explícitamente a cerrar las fallas del RED.
2. Se despachó un **subagente nuevo, sin memoria de esta conversación**, con la instrucción de cargar y seguir esa skill como si estuviera activa, y se le dio el mismo prompt que se le había dado al baseline.
3. Su respuesta se guardó en `tests/post/caso-N.md` y se verificó contra criterios de aceptación explícitos (no "¿quedó lindo?", sino chequeos puntuales: ¿la medida de respuesta tiene un número dentro de la tabla formal? ¿Ambiente quedó explícito? etc.).
4. Si algún chequeo fallaba, se parcheaba el `SKILL.md` en el punto exacto de la falla y se repetía el subagente — nunca se avanzaba con un GREEN parcial.

Usar un subagente fresco en cada intento es la parte no negociable del método: si se sigue probando en la misma conversación que escribió la skill, el modelo "sabe" lo que se espera y el test deja de medir si la skill *por sí sola* induce el comportamiento correcto.

### REFACTOR — cerrar loopholes con presión adicional

Una vez las 3 skills en GREEN sobre sus 4 casos originales, se diseñaron **3 escenarios de presión nuevos**, pensados específicamente para explotar atajos que una skill podría tomar sin violar la letra de sus instrucciones (ej.: aceptar "rápido y seguro" como medida válida porque *suena* completo; degenerar un árbol de utilidad de una sola hoja sin avisar que eso rompe el propósito de priorizar). Cada uno se corrió igual que un GREEN test — subagente fresco, criterios de aceptación explícitos — y donde falló algo, se ajustó el `SKILL.md` correspondiente.

### Regresión final

Al cerrar el REFACTOR, se corrió una batería de **24 casos de regresión (8 por skill)** para confirmar que los ajustes no habían roto nada y que el comportamiento generalizaba a dominios que ninguno de los 7 casos anteriores había tocado. El detalle completo de esa batería está en [`testing.md`](./testing.md).

## 3. Restricciones globales de diseño (aplican a las 3 skills por igual)

Estas reglas se fijaron *antes* de escribir cualquier `SKILL.md`, para que las 3 skills fueran interoperables entre sí sin traducción:

- Las 6 partes del template siempre aparecen **en este orden y con estos nombres exactos**: Fuente de estímulo, Estímulo, Ambiente, Artefacto, Respuesta, Medida de respuesta.
- El puntaje de un árbol de utilidad siempre es el par **(Valor de negocio, Riesgo técnico)**, cada uno en escala H/M/L, y nunca sin una justificación de una línea.
- Todo el contenido, ejemplos y salidas generadas están en español.
- Cada `SKILL.md` es autocontenido: si necesita el conocimiento de otra skill, la nombra pero no depende de leer su archivo.
- El contenido de dominio (definiciones, ejemplos por atributo) está parafraseado de *Software Architecture in Practice* (Bass, Clements, Kazman), capítulos 4, 19 y 21 — nunca copiado textual.

## 4. Los 5 hallazgos del baseline que definieron el diseño

Estas fallas, detectadas en el RED, son la razón de ser de casi cada regla explícita dentro de los 3 `SKILL.md`:

1. **Taxonomía en vez de instancia**: al pedir un escenario "general", el baseline listaba categorías de opciones por cada parte (ej. Fuente: "hardware... software... personas...") en vez de comprometerse con un valor único. → `generar-escenario-calidad` exige una tabla cerrada, un valor por celda, con `[A DEFINIR: ...]` explícito si falta información — nunca un menú sin resolver.
2. **La completitud aparente detiene el escrutinio**: una vez marcada una parte "presente", el baseline dejaba de exigirle precisión, y mezclaba huecos de una parte (ej. Respuesta) dentro de la discusión de otra (Medida de respuesta). → `chequear-completitud-escenario` evalúa y redacta cada fila de forma independiente, por regla explícita.
3. **Fuente de estímulo y Ambiente son las partes más salteadas**, tanto al generar como al chequear como al construir árboles. → Es el primer punto que ambas skills de completitud/generación revisan explícitamente, con recordatorio propio en la sección "errores típicos".
4. **Hojas de árbol de utilidad narrativas y sin justificar**: las 12 hojas del baseline fusionaban las 4 partes en una sola oración bajo "Escenario", y ningún puntaje H/M/L tenía justificación. → `construir-arbol-utilidad` exige hojas con las 6 partes separables y prohíbe un puntaje sin una línea de "por qué".
5. **(Hallazgo positivo)** El mini-proceso del libro para atributos "no clásicos" (ej. sustentabilidad energética) ya funcionaba bien en el baseline — no necesitaba rediseño, solo una prueba de regresión para confirmarlo (ver test-08 de `generar-escenario-calidad` en el testing doc).

## 5. Qué necesita cada skill (inputs) y qué entrega (outputs)

| Skill | Se activa cuando el usuario... | Necesita del usuario | Si falta un dato, la skill... | Qué entrega |
|---|---|---|---|---|
| `generar-escenario-calidad` | Pide "escribir un escenario de calidad", nombra un atributo (clásico o no) y quiere formalizarlo, o pide un escenario "general"/"concreto" | El atributo de calidad de interés; si es concreto, el sistema/dominio; si el atributo es "no clásico", disposición a refinarlo junto con la skill | Marca el valor como supuesto o `[A DEFINIR: ...]` — nunca lo deja vacío ni lo inventa sin avisar | Tabla de 6 filas con un valor cerrado por celda + versión en prosa de una oración |
| `chequear-completitud-escenario` | Pega un escenario ya redactado y pregunta "¿está completo?", "¿le falta algo?" | El escenario en prosa (o parcialmente estructurado) | No aplica — la skill nunca completa por su cuenta, solo señala y propone | Tabla de 6 filas con estado ✅/⚠️/❌ por parte + propuesta concreta por cada hueco + escenario reescrito marcando qué es propuesta + alerta de mezcla de atributos si corresponde |
| `construir-arbol-utilidad` | Pide "árbol de utilidad", "priorizar requisitos no funcionales", o prepara una evaluación ATAM | El sistema/dominio bajo análisis; opcionalmente metas de negocio explícitas o atributos ya elegidos | Si no da metas ni atributos, infiere un mínimo de 3-4 relevantes al dominio y explica de dónde salen; si no hay información para puntuar el riesgo con confianza, lo dice en vez de inventar el score | Árbol indentado + tabla (Atributo, Refinamiento, Escenario de 6 partes, (Valor, Riesgo), Justificación) + señalamiento del cuadrante (H,H) prioritario |

## 6. Referencias

- Evidencia RED/GREEN/REFACTOR: `tests/baseline/`, `tests/post/`, `tests/refactor/`.
- Comparación caso por caso RED vs. GREEN: `tests/COMPARISON.md`.
- Batería de regresión (24 casos): [`testing.md`](./testing.md) en este mismo directorio.
- Plan de implementación original (task-by-task, con el detalle completo de cada `SKILL.md` tal como se escribió): `docs/superpowers/plans/2026-09-06-sei-quality-attributes-plugin.md` (raíz del repo).
