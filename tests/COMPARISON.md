# Comparación RED vs. GREEN — 7 casos

Para los casos 1-4 hay un archivo `baseline/` (respuesta sin skill, con una sección
"Autocrítica" que audita la propia respuesta contra las 6 partes del template SEI) y
un archivo `post/` (respuesta con la skill correspondiente). Para los casos 5-7 no
hay baseline: son escenarios de presión ("refactor") escritos para poner a prueba un
loophole específico que las skills debían resistir; solo existe `refactor/` (con skill).

## Caso 1 — Escenario general de disponibilidad

**Skill:** generar-escenario-calidad

**Falla RED más importante** (`baseline/caso-1-disponibilidad.md`, sección Autocrítica,
"Resumen"): el baseline confunde "escenario general" con "taxonomía de posibles
valores por categoría". Para cada una de las 6 partes entrega un menú de opciones
(p. ej. Fuente: "componente de hardware... software... personas... entorno físico...")
en vez de comprometerse con un único valor instanciado por parte. Como consecuencia,
la Medida de respuesta nunca fija un umbral dentro del template formal: los únicos
números concretos ("menos de 5 segundos", "99.95%") aparecen recién en un párrafo de
ejemplo fuera de las 6 partes, es decir, no cuentan como la medida entregada.

**Confirmación GREEN** (`post/caso-1-disponibilidad.md`): la skill entrega una tabla
de 6 partes con un único valor instanciado por fila (Fuente: "Un componente interno...
u otro sistema externo del que depende"; Estímulo: "Falla por omisión, crash, timing
incorrecto..."; etc.) y, específicamente, la fila de Medida de respuesta ya trae los
umbrales dentro del template formal: "Tiempo de detección de la falla < 10 s; tiempo
de reparación/recuperación (MTTR) < 5 min; disponibilidad ≥ 99.9% medida en ventana
mensual". El check que antes fallaba
(umbral numérico dentro de la medida de respuesta formal, no solo en un ejemplo aparte)
ahora pasa.

## Caso 2 — Completitud de escenario de mesa de ayuda

**Skill:** chequear-completitud-escenario

**Falla RED más importante** (`baseline/caso-2-completitud-mesa-ayuda.md`, Autocrítica,
"Resumen"): una vez que el baseline marca una parte como "presente", deja de exigirle
el mismo estándar de precisión que aplica a la Medida de respuesta. La genericidad de
Fuente ("un cliente") y Artefacto ("el sistema") queda señalada pero nunca se corrige
en el ejemplo final reescrito; y los vacíos reales de la Respuesta (canal de
notificación, reintentos, escalamiento) terminan disueltos dentro de la discusión de
la Medida de respuesta en vez de reconocerse como un problema propio de la Respuesta,
mezclando los límites entre dos partes distintas del template.

**Confirmación GREEN** (`post/caso-2-completitud-mesa-ayuda.md`): la tabla de
completitud usa un estado independiente por parte (✅/❌/⚠️) y mantiene los límites
entre partes: la fila "Respuesta" (⚠️) señala específicamente que faltan datos del
ticket, canal de notificación y qué pasa si nadie confirma — sin mezclarlo con la fila
separada "Medida de respuesta" (❌), que solo habla de la ausencia de números. Las
"Propuestas concretas" resuelven cada parte por separado (Ambiente, Artefacto,
Respuesta, Medida de respuesta), y el escenario reescrito final sí instancia Artefacto
de forma concreta ("el módulo de gestión de tickets", "el gateway de notificaciones")
en vez de dejarlo señalado-pero-sin-corregir como hacía el baseline. Fuente de
estímulo, en cambio, se marca ✅ tal como está ("un cliente" alcanza porque este no es
un escenario de seguridad donde la confiabilidad de la fuente cambie la respuesta
esperada) y no se toca en la reescritura — a diferencia de Artefacto, no era una parte
que necesitara corrección, así que no corresponde decir que también se "instanció de
forma concreta".

## Caso 3 — Árbol de utilidad de un ATM

**Skill:** construir-arbol-utilidad

**Falla RED más importante** (`baseline/caso-3-arbol-atm.md`, Autocrítica, "Resumen"):
ninguna de las 12 hojas separa explícitamente las 4 partes iniciales del escenario
(Fuente, Estímulo, Artefacto, Ambiente) — todas quedan fusionadas en una única oración
narrativa bajo el rótulo genérico "Escenario", pese a que el propio texto introductorio
afirma falsamente que estas partes están "explícitas". A esto se suma que las 12
priorizaciones (H/M/L) no llevan ninguna justificación textual.

**Confirmación GREEN** (`post/caso-3-arbol-atm.md`): la tabla "Tabla de escenarios
(formato de 6 partes)" separa y rotula explícitamente cada parte dentro de la celda de
escenario ("**Fuente:**... · **Estímulo:**... · **Ambiente:**... · **Artefacto:**...
· **Respuesta:**... · **Medida:**...") para las 9 hojas del árbol, resolviendo la
fusión de campos detectada en RED. Además cada fila agrega una columna
"Justificación" con una frase que explica el porqué de la tupla (Valor, Riesgo) — por
ejemplo, para skimming: "H de negocio: el fraude... implica pérdidas... graves; H de
riesgo: la detección confiable de hardware manipulado es compleja..." — resolviendo
también la ausencia total de justificación de las priorizaciones detectada en RED.

## Caso 4 — Atributo de calidad no clásico (sustentabilidad energética)

**Skill:** generar-escenario-calidad

**Falla RED más importante** (`baseline/caso-4-qa-no-clasico.md`, Autocrítica,
"Resumen"): la omisión inconsistente de "fuente del estímulo" y "ambiente" en casi la
mitad de los escenarios concretos — estas dos partes solo quedan bien resueltas
cuando el escenario es "fácil" (regulatorio, evolución), pero se pierden o quedan
implícitas en los escenarios técnicos (térmico, contingencia, general), como si el
modelo tratara Fuente y Ambiente como opcionales frente a Estímulo/Respuesta/Medida,
que sí aparecen siempre completos.

**Confirmación GREEN** (`post/caso-4-qa-no-clasico.md`): los 3 escenarios concretos
(refrigeración, energía renovable, consumo por transacción) y el escenario general
final se entregan como tablas de 6 filas, y en los 4 casos las filas "Fuente de
estímulo" y "Ambiente" están completas y no genéricas — p. ej., Escenario 2: Fuente =
"El sistema de gestión energética del data center, al detectar una caída..."; Ambiente
= "Operación normal, 24/7, sin condición de emergencia". El check que antes fallaba de
forma inconsistente (Fuente/Ambiente presentes en todos los escenarios, no solo en los
"fáciles") ahora pasa en el 100% de los escenarios entregados.

## Caso 5 — Medida no cuantificable + mezcla de atributos (presión)

**Skill:** chequear-completitud-escenario

**Loophole que el escenario de presión buscaba probar:** el escenario de entrada
("...el sistema bloquea la cuenta de forma rápida y segura") usa calificativos sin
número ("rápida y segura") como si fueran una medida de respuesta válida, y además
mezcla dos atributos de calidad distintos (rendimiento y seguridad) en una sola
oración/medida. El riesgo era que la skill aceptara "rápida y segura" como una medida
"presente" solo por tener palabras en esa posición, sin exigir un umbral verificable,
y que no detectara la mezcla de atributos.

**Confirmación GREEN pasó** (`refactor/caso-5-medida-no-cuantificable.md`): la fila
"Medida de respuesta" se marca ❌ Ausente con el comentario explícito: "'De forma
rápida y segura' son calificativos sin número ni criterio verificable. Es el ejemplo
típico de medida no cuantificable". Además hay una sección dedicada "Observación
adicional: mezcla de atributos de calidad" que identifica rendimiento vs. seguridad
mezclados y recomienda separarlos en dos escenarios con medidas propias (rendimiento:
"<500ms"; seguridad: "0 accesos exitosos... 100% de intentos registrados"). Ambos
checks — rechazar calificativos sin número, y detectar mezcla de atributos — pasan.

## Caso 6 — Segundo atributo no clásico, dominio distinto: "capacidad de pago" (presión)

**Skill:** generar-escenario-calidad

**Loophole que el escenario de presión buscaba probar:** que el mini-proceso del Cap. 4
para atributos no clásicos, verificado en Caso 4 con "sustentabilidad energética" de un
data center, no esté sobreajustado (*overfit*) al vocabulario de ese caso concreto (PUE,
kWh, refrigeración). Para probarlo hace falta un segundo atributo no clásico en un
dominio completamente distinto — "capacidad de pago" (*affordability*) de una plataforma
de e-commerce — y chequear que la skill construya sub-características, escenarios y
medidas propias del nuevo dominio en vez de arrastrar vocabulario o estructura copiada
del caso del data center.

**Confirmación GREEN pasó** (`refactor/caso-6-atributo-no-clasico-2.md`): la skill
ejecuta el mismo mini-proceso de 3 pasos (a: refinar en 4 sub-características propias
del e-commerce — costo de infraestructura por transacción, costo de evolución, costo de
terceros como % del GMV, TCO; b: 3 escenarios concretos con tabla de 6 partes y medidas
cuantificadas en términos de e-commerce como % del GMV, horas-persona y presupuesto de
infraestructura; c: generalización) sin ninguna mención de PUE, kWh, refrigeración ni
ningún otro término propio del dominio de data centers del Caso 4 — cero fuga de
vocabulario entre casos. Además, de forma incidental, la skill también señala la
ambigüedad del nombre del atributo (interpreta *affordability* y aclara textualmente que
la lectura alternativa, "medios de pago que el cliente puede usar", sería un atributo
distinto) y deja explícito un placeholder `[A DEFINIR: presupuesto anual...]` en vez de
inventar una cifra real de presupuesto que nadie proveyó.

## Caso 7 — Árbol de utilidad de una sola hoja (presión)

**Skill:** construir-arbol-utilidad

**Loophole que el escenario de presión buscaba probar:** se pide explícitamente un
árbol de utilidad con un solo atributo de calidad y una sola hoja — un pedido que
técnicamente se puede "cumplir" en formato pero que rompe el propósito del árbol de
utilidad (priorizar entre escenarios en competencia vía la matriz Valor/Riesgo). El
riesgo era que la skill obedeciera sin más, entregando una hoja aislada en el
cuadrante (H,H) como si fuera una "priorización" real.

**Confirmación GREEN pasó** (`refactor/caso-7-arbol-una-hoja.md`): la skill antepone
una nota explícita ("Nota antes de entregar el árbol") que señala que "el árbol de
utilidad sirve para priorizar entre múltiples escenarios en competencia" y que con una
sola hoja "el par (Valor de negocio, Riesgo técnico) queda como un dato aislado, no
como un ranking", recomendando expandir a 3-4 atributos para una preparación real de
ATAM — y aun así entrega el árbol de una hoja tal como se pidió, con las 6 partes
completas y rotuladas. Al final reitera la limitación: "al haber una sola hoja, cae
automáticamente en el cuadrante (H,H) — pero... esto no es una priorización real: es
la única opción disponible porque no hay otras hojas... con las que compararla". El
check pasó: la skill entrega lo pedido sin fingir que resuelve para lo que el método
no sirve en ese caso degenerado.

## Veredicto general

En los 4 casos con baseline (1-4), la falla RED dominante es siempre la misma familia
de problema — una parte del template SEI queda presente en apariencia pero sin
comprometerse con un valor concreto y verificable (taxonomía en vez de instancia,
genericidad sin corregir, campos fusionados sin rotular, u omisión inconsistente de
Fuente/Ambiente) — y en los 4 casos GREEN corrige exactamente ese punto sin introducir
nuevas fallas visibles. En los 3 casos de presión (5-7), las skills no solo cumplen el
pedido formal sino que además detectan y señalan explícitamente el loophole
metodológico de cada escenario (medida no cuantificable + mezcla de atributos; que el
mini-proceso de atributos no clásicos no esté sobreajustado al vocabulario del Caso 4;
degenerar el árbol de utilidad a una sola hoja)
en vez de obedecer ciegamente o fabricar contenido no verificable.
