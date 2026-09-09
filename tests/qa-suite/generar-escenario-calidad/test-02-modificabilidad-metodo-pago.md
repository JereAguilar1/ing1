## Prompt

Generá un escenario concreto de modificabilidad: un e-commerce necesita agregar un nuevo método de pago (billetera virtual) sin tocar el módulo de facturación.

## Criterios de aceptación esperados

- Estímulo = pedido de agregar el nuevo método de pago.
- Artefacto = el módulo de pagos (no el de facturación).
- La Medida de respuesta cuantifica el costo del cambio (horas-persona y/o cantidad de componentes afectados) y deja explícito que no afecta el módulo de facturación.
- Las 6 partes aparecen separadas y cerradas (no fusionadas en una oración).

## Respuesta obtenida

### Escenario de modificabilidad: nuevo método de pago (billetera virtual) sin afectar facturación

**Tipo de escenario: concreto** (específico del sistema de e-commerce bajo análisis).

| Parte | Valor |
|---|---|
| Fuente de estímulo | Un desarrollador del equipo, a pedido del Product Owner del e-commerce |
| Estímulo | Pedido de agregar un nuevo método de pago (billetera virtual) al proceso de checkout |
| Ambiente | Tiempo de diseño/desarrollo, durante la construcción de un nuevo release (no en producción en caliente) |
| Artefacto | El módulo de pagos, específicamente su capa de adaptadores/gateway de medios de pago (NO el módulo de facturación) |
| Respuesta | El desarrollador implementa un nuevo adaptador de pago dentro del módulo de pagos, cumpliendo la interfaz de pago existente, sin modificar código, datos ni interfaces del módulo de facturación |
| Medida de respuesta | El cambio se completa en ≤ 40 horas-persona [SUPUESTO: valor propuesto, no provisto por el usuario] y afecta ≤ 3 componentes, todos dentro del módulo de pagos; 0 archivos/interfaces modificados en el módulo de facturación; el módulo de facturación no requiere recompilación ni redespliegue |

**Prosa:** "Cuando el equipo de desarrollo recibe el pedido de agregar un nuevo método de pago (billetera virtual) durante el desarrollo de un release, el módulo de pagos debe incorporar el nuevo adaptador cumpliendo la interfaz de pago existente en no más de 40 horas-persona y afectando como máximo 3 componentes, todos internos al módulo de pagos, sin modificar ni redesplegar el módulo de facturación."

Nota: el valor de 40 horas-persona es un supuesto ilustrativo (marcado como tal) porque el prompt no especificó un costo objetivo; se puede ajustar si el usuario tiene un dato real de esfuerzo esperado.

## Veredicto

- ✅ Estímulo = pedido de agregar el nuevo método de pago. La fila "Estímulo" dice exactamente "Pedido de agregar un nuevo método de pago (billetera virtual) al proceso de checkout".
- ✅ Artefacto = el módulo de pagos (no el de facturación). La fila "Artefacto" identifica el módulo de pagos/capa de adaptadores y aclara explícitamente "(NO el módulo de facturación)".
- ✅ La Medida de respuesta cuantifica el costo del cambio y deja explícito que no afecta facturación. Incluye "≤ 40 horas-persona", "≤ 3 componentes" y "0 archivos/interfaces modificados en el módulo de facturación".
- ✅ Las 6 partes aparecen separadas y cerradas. Se presentan en una tabla de 6 filas con valores cerrados (el supuesto de horas está marcado como tal, no como opción abierta), y luego una prosa aparte de una sola oración.

**Resultado: PASA**
