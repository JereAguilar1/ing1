## Respuesta (con skill)

Reviso el escenario contra las 6 partes del template SEI:

> "Cuando un usuario intenta iniciar sesión con credenciales inválidas tres veces seguidas, el sistema bloquea la cuenta de forma rápida y segura."

### Tabla de completitud

| Parte | Estado | Comentario |
|---|---|---|
| **Fuente de estímulo** | ⚠️ Ambiguo | Dice "un usuario", pero no aclara si importa distinguir un usuario legítimo que olvidó su contraseña de un posible atacante realizando fuerza bruta. En seguridad esta distinción suele cambiar la respuesta esperada (ej. bloqueo simple vs. alerta a seguridad + captcha). |
| **Estímulo** | ✅ Presente | "Intenta iniciar sesión con credenciales inválidas tres veces seguidas" describe con claridad la condición disparadora, sin mezclarla con la respuesta. |
| **Ambiente** | ❌ Ausente | No se especifica bajo qué circunstancias ocurre (operación normal, sistema expuesto a red pública, horario de carga pico, etc.). No se menciona, así que se asume faltante. |
| **Artefacto** | ⚠️ Ambiguo | Se infiere "la cuenta de usuario" / el módulo de autenticación, pero el sujeto de la respuesta es "el sistema" en términos genéricos. Conviene precisar qué componente aplica el bloqueo (ej. "el servicio de autenticación"). |
| **Respuesta** | ⚠️ Ambiguo | "Bloquea la cuenta" describe una acción, pero está incompleta como comportamiento: no dice si se notifica al usuario, si se registra el intento, si hay un mecanismo de desbloqueo (temporal, por soporte, por email), ni si se alerta a un equipo de seguridad. |
| **Medida de respuesta** | ❌ Ausente | "De forma rápida y segura" son calificativos sin número ni criterio verificable. Es el ejemplo típico de medida no cuantificable (equivalente a "rápido" o "seguro" sin unidad, umbral o forma de comprobarlo). |

### Observación adicional: mezcla de atributos de calidad

El escenario mezcla dos atributos de calidad distintos en una sola oración: **rendimiento** ("de forma rápida") y **seguridad** ("de forma segura"). Se recomienda separarlos en dos escenarios, cada uno con su propia medida de respuesta, en vez de dejarlos combinados bajo una misma frase.

### Propuestas de redacción concreta

- **Fuente de estímulo**: "Un usuario (potencialmente un atacante realizando fuerza bruta) intenta iniciar sesión..."
- **Ambiente**: "con el sistema expuesto a red pública, en operación normal"
- **Artefacto**: "el servicio de autenticación" (en vez de "el sistema" genérico)
- **Respuesta**: "el sistema bloquea la cuenta, registra el intento con timestamp e IP de origen, notifica al usuario por email, y permite el desbloqueo solo mediante verificación adicional (o tras un tiempo de espera definido)"
- **Medida de respuesta (separada en dos, por la mezcla de atributos)**:
  - Rendimiento: "el bloqueo se aplica en menos de 500ms desde el tercer intento fallido"
  - Seguridad: "0 accesos exitosos posteriores a los 3 intentos fallidos; 100% de los intentos registrados en el log de auditoría"

### Escenario reescrito (propuesta, a revisar por el usuario)

> Cuando **un usuario, potencialmente un atacante realizando fuerza bruta [agregado],** intenta iniciar sesión con credenciales inválidas tres veces seguidas, **con el sistema expuesto a red pública, en operación normal [agregado — ambiente]**, **el servicio de autenticación [precisado — artefacto]** bloquea la cuenta, **registra el intento con timestamp e IP de origen, notifica al usuario por email, y exige verificación adicional para el desbloqueo [agregado — respuesta]**, en **menos de 500ms desde el tercer intento fallido [agregado — medida de rendimiento]**, garantizando **0 accesos exitosos posteriores y 100% de los intentos registrados en el log de auditoría [agregado — medida de seguridad]**.

Nota: por la mezcla de atributos, se sugiere partir esto en dos escenarios independientes (uno de rendimiento del bloqueo, otro de seguridad del control de acceso) en vez de un único escenario combinado.
