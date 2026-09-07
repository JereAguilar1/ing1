## Respuesta (con skill)

"Capacidad de pago" (*affordability*) no es uno de los atributos clásicos del libro (disponibilidad, interoperabilidad, modificabilidad, rendimiento, seguridad, testabilidad, usabilidad), así que sigo el mini-proceso del Cap. 4 en vez de inventar la tabla directamente.

### Paso a — Aclarar el atributo y refinarlo en sub-características

**Supuesto a validar con el usuario:** interpreto "capacidad de pago" en el sentido en que la literatura extendida del SEI usa *affordability* — la medida en que el costo de **construir, operar y evolucionar** la plataforma se mantiene dentro del presupuesto disponible del negocio a lo largo de su ciclo de vida. No lo interpreto como "medios de pago que el cliente puede usar" (eso sería más bien una faceta de usabilidad/interoperabilidad de checkout, un atributo distinto). Si el usuario quiso decir esto último, hay que replantear las sub-características.

Con esa interpretación, refino "capacidad de pago" de una plataforma de e-commerce en cuatro sub-características:

1. **Costo de infraestructura por transacción/carga** — cuánto cuesta en cómputo y hosting procesar cada compra, en especial cuando el tráfico escala.
2. **Costo de evolución** — cuánto cuesta en horas-persona agregar o modificar funcionalidad del e-commerce (nuevo medio de pago, nueva feature de checkout, etc.) respecto del presupuesto de desarrollo asignado.
3. **Costo de servicios de terceros como proporción del valor transaccionado** — comisiones de pasarela de pago, antifraude, notificaciones, CDN, etc., medidas como % del GMV (Gross Merchandise Value).
4. **Costo total de propiedad (TCO)** — la suma de infraestructura + desarrollo + terceros + soporte a lo largo de un período (ej. anual) frente al presupuesto de IT/negocio asignado.

### Paso b — Escenarios concretos de ejemplo (2-3)

**Ejemplo 1 — Costo de escalado en pico estacional de ventas**

| Parte | Valor |
|---|---|
| Fuente de estímulo | El sistema de autoescalado de infraestructura cloud |
| Estímulo | Un pico de tráfico (ej. CyberMonday) dispara el aprovisionamiento automático de instancias adicionales |
| Ambiente | Pico estacional de demanda (Black Friday / CyberMonday) |
| Artefacto | La capa de cómputo y base de datos del e-commerce |
| Respuesta | El sistema escala los recursos necesarios para sostener la carga y los libera automáticamente al bajar la demanda, evitando sobreaprovisionamiento permanente |
| Medida de respuesta | El costo de infraestructura por orden procesada durante el pico no supera el 130% del costo promedio en operación normal; el gasto cloud de ese mes no supera el 15% del presupuesto anual de infraestructura |

**Ejemplo 2 — Costo de evolución (nueva funcionalidad de checkout)**

| Parte | Valor |
|---|---|
| Fuente de estímulo | El área de producto/negocio |
| Estímulo | Solicita incorporar un nuevo medio de pago local al checkout |
| Ambiente | Ciclo de planificación trimestral, dentro del presupuesto de desarrollo ya asignado |
| Artefacto | El módulo de checkout/pagos de la plataforma |
| Respuesta | El equipo de desarrollo estima, implementa y despliega el cambio |
| Medida de respuesta | El costo de implementación (horas-persona × tarifa) no supera el 8% del presupuesto trimestral asignado a esa línea de producto |

**Ejemplo 3 — Costo de servicios de terceros sobre el valor transaccionado**

| Parte | Valor |
|---|---|
| Fuente de estímulo | Los proveedores de pasarela de pago y antifraude (facturan por transacción) |
| Estímulo | El crecimiento sostenido del volumen de ventas incrementa el total cobrado en comisiones |
| Ambiente | Operación normal, con crecimiento mes a mes del volumen de ventas |
| Artefacto | El módulo de procesamiento de pagos y los contratos de servicios asociados (pasarela, antifraude, notificaciones) |
| Respuesta | El negocio monitorea el costo de terceros como % del GMV y dispara una alerta/renegociación si se desvía del objetivo |
| Medida de respuesta | El costo combinado de servicios de terceros no supera el 2.5% del GMV mensual |

### Paso c — Generalización

Tomando los estímulos (variación de costo por carga, por cambio de funcionalidad o por tarifas de terceros), las respuestas (contener el costo automáticamente o mediante gestión) y las medidas (desvío acotado sobre un presupuesto o baseline) de los tres ejemplos, construyo el escenario general:

| Parte | Valor |
|---|---|
| **Fuente de estímulo** | Un evento de negocio o técnico que impacta el costo de la plataforma (pico de demanda, pedido de nueva funcionalidad, cambio en tarifas de un proveedor externo) |
| **Estímulo** | Un incremento en el costo de operar, escalar o evolucionar la plataforma de e-commerce |
| **Ambiente** | Operación normal, pico estacional de demanda, o ciclo de planificación/presupuesto |
| **Artefacto** | La plataforma de e-commerce (infraestructura, módulos de desarrollo, integraciones de terceros) |
| **Respuesta** | El sistema o la organización detecta el incremento de costo y lo contiene (autoescalado eficiente, priorización de alcance, renegociación de tarifas) para mantenerse dentro del presupuesto asignado |
| **Medida de respuesta** | El desvío del costo (por transacción, por incremento de funcionalidad o total) respecto del presupuesto/baseline aprobado no supera el 10-15%; el costo de servicios de terceros se mantiene ≤ 2.5% del GMV mensual |

**Prosa:** "Cuando un pico de demanda, un pedido de nueva funcionalidad o un cambio en las tarifas de un proveedor externo incrementan el costo de operar, escalar o evolucionar la plataforma de e-commerce, el sistema y la organización deben contener ese incremento (mediante autoescalado eficiente, priorización o renegociación) de forma que el desvío respecto del presupuesto aprobado no supere el 10-15%, y el costo de servicios de terceros se mantenga por debajo del 2.5% del GMV mensual."

**Nota:** los porcentajes (130%, 15%, 8%, 2.5%, 10-15%) son valores ilustrativos propuestos para cerrar la medida de forma verificable, no cifras provistas por el usuario — deben validarse o ajustarse contra el presupuesto real del negocio. `[A DEFINIR: presupuesto anual de infraestructura y de desarrollo de referencia]`.
