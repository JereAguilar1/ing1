## Respuesta (baseline, sin skill)

Con gusto. A continuación te presento un árbol de utilidad (utility tree) para un sistema de cajero automático (ATM), siguiendo el método de Bass/Clements/Kazman. Consideré cinco atributos de calidad (más de los cuatro mínimos pedidos): **Seguridad**, **Disponibilidad**, **Rendimiento**, **Usabilidad** y **Modificabilidad**. Cada escenario hoja incluye estímulo, fuente, artefacto, entorno, respuesta y medida de respuesta explícitas, y una priorización (Importancia para el negocio, Dificultad técnica) en escala Alta/Media/Baja, como se acostumbra en este método.

## Utilidad (raíz)

### 1. Seguridad

**1.1 Autenticación y control de acceso**
- Escenario: Un usuario intenta realizar una operación insertando su tarjeta e ingresando el PIN en el teclado del cajero, en condiciones normales de operación. El sistema debe validar la identidad contra el banco emisor antes de permitir cualquier transacción.
  - Respuesta: El sistema verifica el PIN contra el emisor de la tarjeta y bloquea la tarjeta tras una cantidad de intentos fallidos configurada.
  - Medida de respuesta: La cuenta se bloquea automáticamente al tercer intento fallido consecutivo, y la verificación de PIN se completa en menos de 3 segundos.
  - Priorización: (Alta, Media)

**1.2 Protección ante manipulación física (skimming/forzado)**
- Escenario: Un atacante intenta instalar un dispositivo de clonación de tarjetas (skimmer) o forzar el lector de tarjetas del ATM durante la operación normal, mientras el sistema está en producción.
  - Respuesta: El sistema detecta anomalías físicas o eléctricas en el lector/dispensador (sensores anti-skimming) y bloquea el equipo, notificando al centro de monitoreo.
  - Medida de respuesta: La alerta se genera en menos de 5 segundos desde la detección, y el cajero pasa a modo "fuera de servicio" automáticamente en el 100% de los casos detectados.
  - Priorización: (Alta, Alta)

**1.3 Cifrado de comunicaciones**
- Escenario: Durante una transacción de retiro, los datos de la tarjeta y el monto viajan desde el ATM hacia el switch transaccional del banco a través de la red.
  - Respuesta: Toda la información sensible se transmite cifrada extremo a extremo entre el ATM y el host bancario.
  - Medida de respuesta: El 100% de los mensajes usan cifrado de al menos AES-256 o TLS 1.2+, sin excepciones, verificable por auditoría.
  - Priorización: (Alta, Media)

### 2. Disponibilidad

**2.1 Falla de hardware (dispensador de billetes)**
- Escenario: El módulo dispensador de billetes falla durante una operación de retiro, en horario de operación normal (24/7).
  - Respuesta: El sistema detecta la falla, cancela la transacción de forma segura sin debitar la cuenta del cliente, y reporta el estado de "fuera de servicio" al centro de monitoreo.
  - Medida de respuesta: La detección y notificación ocurren en menos de 10 segundos, y no se pierde ni se debita dinero en el 100% de los casos de falla.
  - Priorización: (Alta, Media)

**2.2 Caída de la conexión con el host bancario**
- Escenario: Se pierde la conectividad de red entre el ATM y el servidor central del banco mientras un cliente está autenticado.
  - Respuesta: El sistema informa al usuario que el servicio no está disponible temporalmente, conserva la tarjeta o la retorna según política, y reintenta la conexión automáticamente.
  - Medida de respuesta: El ATM detecta la pérdida de conexión en menos de 5 segundos y recupera el servicio automáticamente al restablecerse la red en menos de 30 segundos, sin intervención manual.
  - Priorización: (Alta, Media)

**2.3 Disponibilidad general del servicio**
- Escenario: Durante un período de operación continua de un año, el ATM debe estar disponible para atender transacciones en cualquier momento, incluyendo mantenimientos programados.
  - Respuesta: El sistema opera de forma continua, con ventanas de mantenimiento planificadas fuera de horarios pico.
  - Medida de respuesta: Disponibilidad anual igual o superior a 99.5% (menos de ~44 horas de indisponibilidad no planificada al año).
  - Priorización: (Alta, Baja)

### 3. Rendimiento

**3.1 Tiempo de respuesta de una transacción de retiro**
- Escenario: Un usuario solicita un retiro de efectivo en condiciones de carga normal del sistema (uso típico diurno).
  - Respuesta: El sistema procesa la solicitud, valida fondos, autoriza con el host y dispensa el efectivo.
  - Medida de respuesta: El tiempo total desde la confirmación del monto hasta la entrega del efectivo no supera los 15 segundos en el 95% de las transacciones.
  - Priorización: (Alta, Media)

**3.2 Comportamiento bajo carga pico**
- Escenario: Múltiples cajeros de una misma sucursal o red generan un pico de transacciones simultáneas (por ejemplo, día de pago de salarios).
  - Respuesta: El sistema mantiene tiempos de respuesta aceptables sin degradar la experiencia del usuario ni generar time-outs indebidos.
  - Medida de respuesta: El tiempo de respuesta promedio no aumenta más de un 20% respecto al escenario de carga normal, incluso con el triple del volumen habitual de transacciones concurrentes.
  - Priorización: (Media, Alta)

### 4. Usabilidad

**4.1 Facilidad de uso para un usuario nuevo**
- Escenario: Un usuario que utiliza el ATM por primera vez intenta realizar un retiro de efectivo sin asistencia de personal humano.
  - Respuesta: El sistema guía al usuario mediante una interfaz clara, con instrucciones paso a paso y opciones de idioma.
  - Medida de respuesta: El 90% de los usuarios nuevos completan la transacción sin errores ni asistencia en menos de 2 minutos.
  - Priorización: (Media, Baja)

**4.2 Accesibilidad para personas con discapacidad visual**
- Escenario: Un usuario con discapacidad visual necesita realizar una operación básica (consulta de saldo o retiro) de forma autónoma.
  - Respuesta: El sistema ofrece guía por audio a través de auriculares y navegación mediante teclado táctil (Braille o relieve).
  - Medida de respuesta: El usuario completa la transacción de forma autónoma en el 100% de los casos probados con esta modalidad habilitada, sin depender de asistencia visual.
  - Priorización: (Media, Media)

### 5. Modificabilidad

**5.1 Incorporación de un nuevo tipo de transacción**
- Escenario: El equipo de desarrollo del banco necesita agregar una nueva funcionalidad (por ejemplo, pago de servicios) al software del ATM, sin afectar las transacciones existentes.
  - Respuesta: El cambio se implementa modificando únicamente el módulo de transacciones correspondiente, gracias a una arquitectura modular con interfaces bien definidas.
  - Medida de respuesta: La nueva funcionalidad se implementa, prueba y despliega en menos de 3 semanas-persona, sin modificar el código de los módulos de retiro, consulta o transferencia existentes.
  - Priorización: (Media, Media)

**5.2 Actualización de la interfaz gráfica**
- Escenario: El área de marketing solicita actualizar el diseño visual de las pantallas del ATM para reflejar una nueva imagen corporativa.
  - Respuesta: El equipo de desarrollo actualiza los componentes de presentación sin modificar la lógica de negocio ni los módulos de comunicación con el host.
  - Medida de respuesta: Los cambios se limitan a la capa de presentación y se completan en menos de 1 semana-persona, con cero regresiones detectadas en pruebas de las transacciones existentes.
  - Priorización: (Baja, Baja)

---

Este árbol puede ampliarse agregando otros atributos relevantes según el contexto específico (por ejemplo, Interoperabilidad con distintas redes bancarias, o Testeabilidad), y las medidas de respuesta deberían ajustarse a los acuerdos de nivel de servicio (SLA) reales del banco o entidad que opere los cajeros.

## Autocrítica (checklist SEI)

### 0. Nota sobre una afirmación falsa del propio baseline

El párrafo introductorio (línea 3) afirma explícitamente: *"Cada escenario hoja incluye estímulo, fuente, artefacto, entorno, respuesta y medida de respuesta explícitas"*. Esto es **falso**: en ninguna de las 12 hojas aparecen los campos "Fuente del estímulo", "Estímulo", "Artefacto" o "Ambiente" como campos rotulados y separados. Todas las hojas colapsan esas cuatro partes en una única oración narrativa bajo el rótulo genérico "Escenario", y solo "Respuesta" y "Medida de respuesta" están efectivamente separadas y rotuladas. Esto es una sobre-declaración del propio texto, no solo una omisión silenciosa.

### 1. ¿Al menos 4 atributos de calidad distintos y no solapados?

Sí, formalmente: Seguridad, Disponibilidad, Rendimiento, Usabilidad y Modificabilidad (5 atributos, cumple el mínimo de 4). Los cinco están razonablemente bien diferenciados en general, pero hay un solapamiento puntual:

- **1.2 (Protección ante manipulación física)**: la respuesta termina en *"el cajero pasa a modo 'fuera de servicio' automáticamente"*, que es un efecto de **Disponibilidad**, no de Seguridad. El escenario está clasificado bajo Seguridad pero su medida de respuesta mezcla una métrica de detección de seguridad con una métrica de disponibilidad del equipo, sin que el árbol reconozca esa dependencia cruzada (podría requerir un escenario de Disponibilidad enlazado, o al menos una nota de trazabilidad).
- **2.3 (Disponibilidad general del servicio)** se solapa conceptualmente con 2.1 y 2.2: es una meta agregada anual, mientras 2.1/2.2 son fallas puntuales. No son atributos distintos, pero sí son hojas de **naturaleza diferente** puestas al mismo nivel (ver punto 4).

### 2. Completitud de las 6 partes por escenario hoja

Ninguna de las 12 hojas presenta las 6 partes como campos separados. En todos los casos, Fuente/Estímulo/Artefacto/Ambiente están fusionados en la oración "Escenario: ..." sin rotular, lo que obliga a inferir cuál es cuál. Detalle por hoja:

- **1.1 Autenticación y control de acceso**: Fuente ("un usuario") y Estímulo ("inserta tarjeta e ingresa PIN") están fusionados en una sola cláusula. Artefacto nunca se nombra explícitamente (¿el módulo de autenticación? ¿todo el ATM?). Ambiente ("condiciones normales de operación") es una frase de relleno, no aporta información discriminante. Además hay una inconsistencia de objeto: la Respuesta dice *"bloquea la **tarjeta**"* pero la Medida de respuesta dice *"la **cuenta** se bloquea"* — tarjeta y cuenta son artefactos distintos y el texto los usa como sinónimos.
- **1.2 Protección ante manipulación física**: Fuente ("un atacante") y Estímulo ("instala skimmer o fuerza el lector") están fusionados. Artefacto implícito (lector/dispensador) nunca se declara como campo. La Medida de respuesta mide la reacción *una vez detectado* el ataque ("100% de los casos **detectados**") pero no mide la tasa de detección en sí, que es la variable de riesgo real en un escenario de skimming; tal como está escrita, la métrica es tautológica (mide qué pasa después de detectar, no si se detecta).
- **1.3 Cifrado de comunicaciones**: este ítem no es un escenario evento-respuesta en el sentido BCK — no hay una Fuente ni un Estímulo discreto que dispare una reacción del sistema; es una propiedad continua ("los datos viajan cifrados") redactada como si fuera un escenario. Fuente/Estímulo/Ambiente están ausentes de hecho, no solo sin rotular.
- **2.1 Falla de hardware (dispensador)**: Fuente (el propio dispensador, como falla interna) y Estímulo ("falla durante una operación de retiro") están fusionados y no declarados como campos. Artefacto (el dispensador) sí es identificable por el título de la subsección, pero no aparece como campo del escenario mismo.
- **2.2 Caída de conexión con el host**: igual patrón — Fuente/Estímulo fusionados ("se pierde la conectividad... mientras un cliente está autenticado"). Artefacto (enlace de red / host bancario) no se nombra como campo explícito.
- **2.3 Disponibilidad general del servicio**: al igual que 1.3, no es un escenario disparado por un evento sino una meta de nivel de servicio ("durante un año... debe estar disponible"). No hay Fuente ni Estímulo identificables; es una declaración de SLA disfrazada de escenario, mezclando el nivel de "atributo/objetivo" con el nivel de "hoja".
- **3.1 Tiempo de respuesta de un retiro**: Fuente (usuario) y Estímulo ("solicita un retiro... en carga normal") fusionados; Ambiente ("uso típico diurno") aparece pero de forma vaga, sin definir qué distingue "carga normal" de otros escenarios de carga (ver 3.2).
- **3.2 Comportamiento bajo carga pico**: Fuente ("múltiples cajeros"/usuarios concurrentes) y Estímulo ("pico de transacciones simultáneas") fusionados; el Ambiente ("carga pico", "triple del volumen habitual") sí queda algo más claro que en 3.1, pero tampoco es un campo separado.
- **4.1 Facilidad de uso para usuario nuevo**: Fuente/Estímulo fusionados ("usuario que utiliza el ATM por primera vez... intenta retiro sin asistencia"). Artefacto (interfaz de usuario) no declarado como campo.
- **4.2 Accesibilidad visual**: mismo patrón de fusión. La Medida de respuesta ("100% de los casos probados") es débil/circular: no especifica tamaño de muestra ni condiciones de la prueba, por lo que "100%" no es verificable de forma independiente.
- **5.1 Incorporación de nuevo tipo de transacción**: Fuente ("equipo de desarrollo del banco") y Estímulo ("necesita agregar... pago de servicios") fusionados; Artefacto (módulo de transacciones) se menciona en la Respuesta, no en el Escenario. Es de los pocos casos donde Fuente y Estímulo son al menos identificables sin demasiada ambigüedad.
- **5.2 Actualización de interfaz gráfica**: mismo patrón; Fuente ("área de marketing"), Estímulo ("solicita actualizar diseño visual") fusionados, sin campos separados.

En cuanto a Respuesta y Medida de respuesta (lo que el usuario pidió explícitamente que fuera explícito): están presentes y en general son medibles en 9 de 12 hojas (1.1, 2.1, 2.2, 2.3, 3.1, 3.2, 4.1, 5.1, 5.2). Son débiles o parcialmente tautológicas en 2 hojas (1.2, 4.2, por las razones detalladas arriba), y en 1.3 la "medida" es una condición de cumplimiento binario más que una medida de una respuesta a un evento.

### 3. Priorización (H/M/L en dos ejes) y justificación

Las 12 hojas tienen su tupla de priorización en formato (Importancia para el negocio, Dificultad técnica) usando Alta/Media/Baja, lo cual cumple el requisito formal de tener ambos ejes. Sin embargo:

- **Ninguna de las 12 priorizaciones tiene una justificación textual.** Son valores puestos directamente después del símbolo "Priorización:" sin una frase que explique por qué, por ejemplo, 1.2 es (Alta, Alta) mientras 1.1 es (Alta, Media), o por qué 2.3 es (Alta, Baja) — la disponibilidad general parece del mismo orden de dificultad técnica que 2.1/2.2 pero recibe una calificación de dificultad menor sin argumento.
- Hay al menos una asignación cuestionable sin respaldo: 4.2 (Accesibilidad visual) se califica como (Media, Media), pero implementar guía por audio y navegación táctil desde cero (hardware + software) suele ser más costoso técnicamente que 4.1 (Media, Baja); no hay razonamiento que sustente por qué la dificultad relativa entre 4.1 y 4.2 es la que es.
- En síntesis: la priorización está "tirada sin criterio" en el sentido de que no hay trazabilidad entre el contenido del escenario y el valor asignado en ninguna de las 12 hojas.

### 4. Estructura del árbol (raíz → atributos → refinamientos → hojas)

La jerarquía formal Utilidad → Atributo → Subcategoría numerada (1.1, 1.2, ...) es correcta a nivel de rótulos. Pero hay dos problemas estructurales:

- **Cada subcategoría contiene exactamente un escenario**, por lo que en la práctica el nivel "categoría refinada" y el nivel "hoja" quedan fusionados 1:1. Un árbol de utilidad típico tendría, por ejemplo, "Disponibilidad → Falla de hardware" como rama con *varios* escenarios hoja debajo (distintos componentes, distintos modos de falla), no un único escenario por rama. Esto hace que el árbol tenga apariencia de lista plana de 12 ítems más que de árbol con ramificación real en el último nivel.
- **Mezcla de niveles de abstracción entre hojas hermanas**: dentro de Seguridad, 1.1 y 1.2 son escenarios disparados por un evento concreto (intento de autenticación, intento de ataque físico), mientras 1.3 es una propiedad continua del sistema sin evento disparador. Lo mismo ocurre en Disponibilidad: 2.1 y 2.2 son fallas puntuales, pero 2.3 es un objetivo de SLA agregado a un año. Esto mezcla "escenario concreto" con "meta de atributo" en el mismo nivel jerárquico, lo cual viola el principio de que las hojas deben ser escenarios concretos y comparables entre sí (para poder priorizarlas de forma consistente en la matriz de 2 ejes).

### Resumen

El fallo más importante y recurrente es que ninguna de las 12 hojas separa explícitamente las cuatro partes iniciales del escenario (Fuente del estímulo, Estímulo, Artefacto, Ambiente): todas quedan fusionadas en una oración narrativa bajo "Escenario", pese a que el propio texto introductorio afirma falsamente que estas partes están "explícitas". A esto se suma la ausencia total de justificación en las 12 priorizaciones (H/M/L tiradas sin criterio) y la mezcla de escenarios concretos con metas agregadas de SLA como hojas del mismo nivel (1.3, 2.3). Un skill correctivo debería forzar la descomposición en 6 campos rotulados por hoja, exigir una frase de justificación por cada priorización, y separar objetivos de SLA/atributo de los escenarios disparados por eventos concretos.
