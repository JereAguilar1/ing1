## Prompt

Escenario concreto de testabilidad: el módulo de facturación de un ERP acaba de terminar un incremento de desarrollo y hay que probarlo antes de integrarlo.

## Criterios de aceptación esperados

- Ambiente = contexto de desarrollo/integración explícito (ej. antes de merge, en CI).
- Medida de respuesta cuantifica cobertura, tiempo de ejecución de la suite, o tiempo para encontrar la próxima falla.
- Artefacto = el módulo de facturación específicamente, no "el sistema" en general.

## Respuesta obtenida

**Escenario concreto de testabilidad — Módulo de facturación de un ERP**

| Parte | Valor |
|---|---|
| Fuente de estímulo | El equipo de desarrollo (desarrollador/a que codificó el incremento) que dispara el pipeline de pruebas |
| Estímulo | Se completó un incremento de desarrollo del módulo de facturación (nueva regla de cálculo de impuestos, generación de comprobantes o conciliación con cobros) y debe validarse antes de integrarse al resto del ERP |
| Ambiente | Entorno de integración continua (CI), en la etapa de pre-merge / pull request hacia la rama principal, antes de integrar el incremento con los demás módulos del ERP (inventario, cobros, contabilidad) |
| Artefacto | El módulo de facturación específicamente: los componentes de cálculo de impuestos, emisión de comprobantes y conciliación de pagos, ejecutados de forma aislada (con mocks/stubs de los módulos externos de inventario y cobros) |
| Respuesta | El pipeline de CI ejecuta automáticamente la suite de pruebas unitarias y de integración del módulo de facturación de forma aislada, calcula la cobertura de código y bloquea el merge si la suite falla o si la cobertura cae por debajo del umbral definido |
| Medida de respuesta | Cobertura de código del módulo de facturación ≥ 85%; la suite completa (unitarias + integración del módulo) se ejecuta en < 10 minutos; ante una falla, el tiempo para aislar el componente/caso que la origina es < 15 minutos |

**Prosa:** "Cuando el equipo de desarrollo completa un incremento del módulo de facturación del ERP, el pipeline de CI debe ejecutar de forma aislada, antes del merge, la suite de pruebas unitarias y de integración de ese módulo, logrando una cobertura de código ≥ 85%, un tiempo de ejecución de la suite menor a 10 minutos y un tiempo de aislamiento de fallas menor a 15 minutos."

## Veredicto

- ✅ Ambiente = contexto de desarrollo/integración explícito (ej. antes de merge, en CI). — La fila de Ambiente especifica explícitamente "integración continua (CI)" y "pre-merge / pull request hacia la rama principal".
- ✅ Medida de respuesta cuantifica cobertura, tiempo de ejecución de la suite, o tiempo para encontrar la próxima falla. — Incluye los tres: cobertura ≥ 85%, ejecución < 10 min, aislamiento de falla < 15 min.
- ✅ Artefacto = el módulo de facturación específicamente, no "el sistema" en general. — La fila de Artefacto nombra el módulo de facturación y sus componentes concretos (cálculo de impuestos, comprobantes, conciliación), no "el sistema" en general.

**Resultado: PASA**
