## Prompt

Armá un árbol de utilidad para una plataforma de e-commerce, considerando un mínimo de 4 atributos de calidad.

## Criterios de aceptación esperados

- Raíz "Utility" explícita.
- Al menos 4 atributos distintos y no solapados.
- Refinamientos específicos del dominio (carrito, checkout, catálogo, etc.), no nombres de atributo pelados.
- Cada hoja con las 6 partes separables, Respuesta y Medida de respuesta explícitas.
- Cada score (H/M/L, H/M/L) con justificación de una línea.
- Ninguna hoja es una meta agregada de SLA; cada una dispara por un único estímulo.
- Se señala el cuadrante (H,H) como prioritario.

## Respuesta obtenida

Para priorizar los requisitos no funcionales de la plataforma de e-commerce, construí el árbol de utilidad con 4 atributos de calidad: **Rendimiento**, **Disponibilidad**, **Seguridad** y **Modificabilidad**. Elegí estos cuatro porque son los que más tensionan a un e-commerce (picos de tráfico en fechas comerciales, dependencia de pagos de terceros, datos de tarjetas, y evolución constante del catálogo/promociones), y no se solapan entre sí.

### Árbol indentado

```
Utility
├── Rendimiento
│   ├── Tiempo de respuesta del checkout bajo carga pico
│   │   └── Confirmación de pago en Cyber Monday — (H, H)
│   └── Tiempo de respuesta del catálogo/búsqueda
│       └── Búsqueda de productos con filtros en carga normal — (M, L)
├── Disponibilidad
│   ├── Recuperación ante caída del gateway de pagos
│   │   └── Timeout del proveedor de pago durante checkout — (H, H)
│   └── Continuidad del catálogo ante falla de base de datos
│       └── Falla del nodo primario de la BD de catálogo — (M, M)
├── Seguridad
│   ├── Protección de datos de tarjetas (PCI-DSS)
│   │   └── Intento de acceso indebido a datos de pago — (H, H)
│   └── Prevención de takeover de cuentas
│       └── Credential stuffing contra el login — (M, M)
└── Modificabilidad
    ├── Agregar un nuevo método de pago
    │   └── Integrar una billetera digital nueva — (M, L)
    └── Cambiar reglas de precios/promociones
        └── Nueva regla de descuento por categoría — (L, L)
```

### Tabla completa

| Atributo | Refinamiento | Escenario (6 partes) | (Valor, Riesgo) | Justificación |
|---|---|---|---|---|
| Rendimiento | Tiempo de respuesta del checkout bajo carga pico | Fuente de estímulo: cliente autenticado · Estímulo: confirma el pago en el checkout · Ambiente: pico de tráfico (ej. Cyber Monday, ~10x carga normal) · Artefacto: servicio de checkout/pagos · Respuesta: el sistema procesa el pago y confirma la orden · Medida de respuesta: tiempo de respuesta p95 < 2s y p99 < 4s, 0% de timeouts | (H, H) | H de negocio: un checkout lento en el pico de ventas del año se traduce directamente en carritos abandonados e ingresos perdidos; H de riesgo: la carga de Cyber Monday es difícil de replicar en pruebas y depende de la latencia de un gateway de pago externo fuera de nuestro control. |
| Rendimiento | Tiempo de respuesta del catálogo/búsqueda | Fuente de estímulo: usuario anónimo navegando · Estímulo: ejecuta una búsqueda de productos con filtros (categoría, precio, marca) · Ambiente: operación normal, carga promedio · Artefacto: servicio de búsqueda/catálogo · Respuesta: el sistema retorna resultados paginados y relevantes · Medida de respuesta: tiempo de respuesta p95 < 500ms | (M, L) | M de negocio: una búsqueda lenta afecta la experiencia y la conversión, pero no bloquea la compra en sí; L de riesgo: el catálogo ya está indexado con un motor de búsqueda (ej. Elasticsearch) y es un patrón bien conocido. |
| Disponibilidad | Recuperación ante caída del gateway de pagos | Fuente de estímulo: falla del gateway de pago externo (proveedor tercero) · Estímulo: el gateway deja de responder (timeout o caída) · Ambiente: operación normal, cualquier horario · Artefacto: servicio de checkout · Respuesta: el sistema reintenta con un proveedor de pago alternativo o encola la transacción y notifica al usuario sin perder el carrito · Medida de respuesta: failover en < 5s, 0% de pérdida de carritos, tasa de éxito de checkout > 99% durante el incidente | (H, H) | H de negocio: si no se puede cobrar, se pierde toda venta mientras dure la falla; H de riesgo: depende de integraciones externas fuera de nuestro control y el patrón de failover multi-proveedor de pago no es trivial de implementar ni de probar. |
| Disponibilidad | Continuidad del catálogo ante falla de base de datos | Fuente de estímulo: hardware del nodo primario de la base de datos de catálogo · Estímulo: falla por crash del nodo primario · Ambiente: horario de operación normal · Artefacto: base de datos de catálogo (réplica primaria) · Respuesta: failover automático a una réplica secundaria · Medida de respuesta: tiempo de conmutación < 30s, 0% de pérdida de datos de catálogo | (M, M) | M de negocio: un catálogo caído afecta la navegación pero el cliente puede reintentar más tarde, no es tan crítico como no poder cobrar; M de riesgo: la replicación ya está configurada, pero el failover automático nunca se probó a escala de producción completa. |
| Seguridad | Protección de datos de tarjetas (PCI-DSS) | Fuente de estímulo: atacante externo · Estímulo: intento de interceptar o acceder a datos de tarjeta durante el pago · Ambiente: sistema expuesto en producción, tráfico público · Artefacto: servicio de checkout/almacenamiento de datos de pago · Respuesta: los datos de tarjeta se tokenizan y nunca se almacenan en texto plano; todo intento de acceso no autorizado se bloquea y se registra · Medida de respuesta: 100% de los datos de tarjeta tokenizados, 0 datos de tarjeta en logs/BD, alerta emitida en < 1 min ante un intento de acceso indebido | (H, H) | H de negocio: incumplir PCI-DSS implica multas, pérdida de la capacidad de procesar pagos y daño reputacional severo; H de riesgo: requiere certificación externa y la arquitectura de tokenización todavía no fue auditada por completo. |
| Seguridad | Prevención de takeover de cuentas de usuario | Fuente de estímulo: atacante con credenciales filtradas (credential stuffing) · Estímulo: múltiples intentos de login fallidos desde la misma IP/rango en poco tiempo · Ambiente: operación normal, endpoint de login público · Artefacto: servicio de autenticación · Respuesta: el sistema bloquea temporalmente la cuenta/IP y exige verificación adicional (captcha o MFA) · Medida de respuesta: bloqueo tras 5 intentos fallidos en 1 minuto, 100% de esos intentos registrados | (M, M) | M de negocio: afecta la confianza del usuario y puede derivar en fraude, pero no detiene la operación general de la plataforma; M de riesgo: rate limiting y MFA son patrones conocidos, pero todavía no están integrados por completo en el flujo de login. |
| Modificabilidad | Agregar un nuevo método de pago | Fuente de estímulo: desarrollador del equipo de pagos · Estímulo: pedido de negocio de integrar un nuevo método de pago (ej. una billetera digital) · Ambiente: tiempo de diseño, próximo sprint · Artefacto: módulo de checkout/pagos · Respuesta: se agrega el nuevo método sin modificar el módulo de órdenes ni el de inventario · Medida de respuesta: cambio confinado a ≤ 2 componentes (adaptador de pago + configuración), ≤ 24 horas-persona | (M, L) | M de negocio: expandir métodos de pago puede aumentar la conversión en ciertos mercados, pero no es urgente; L de riesgo: el checkout ya usa un patrón adaptador para proveedores de pago, agregar uno nuevo es un caso ya resuelto. |
| Modificabilidad | Cambiar reglas de precios/promociones | Fuente de estímulo: analista de marketing · Estímulo: pedido de agregar una nueva regla de descuento (ej. 2x1 por categoría) · Ambiente: tiempo de diseño, antes de una campaña · Artefacto: motor de reglas de precios/promociones · Respuesta: la nueva regla se configura y despliega sin modificar el servicio de checkout ni el de catálogo · Medida de respuesta: cambio confinado al motor de reglas, ≤ 8 horas-persona, sin regresión en checkout | (L, L) | L de negocio: mejora la conversión en campañas puntuales pero no es crítico para la operación; L de riesgo: el motor de reglas ya está desacoplado y soporta configuración externa sin tocar otros módulos. |

### Candidatas a analizar primero (cuadrante H, H)

Tres hojas caen en el cuadrante de mayor valor de negocio y mayor riesgo técnico, y son las que recomiendo analizar primero en una evaluación de arquitectura (ATAM):

1. **Rendimiento — checkout bajo carga pico** (confirmación de pago en Cyber Monday).
2. **Disponibilidad — recuperación ante caída del gateway de pagos** (timeout del proveedor durante el checkout).
3. **Seguridad — protección de datos de tarjetas (PCI-DSS)**.

Las tres giran en torno al mismo punto crítico del sistema (el flujo de pago/checkout), lo cual sugiere que ese es el componente arquitectónico que más justifica un análisis de tácticas (por ejemplo: colas de reintento, tokenización, circuit breakers hacia el gateway externo) antes que el resto del árbol.

## Veredicto

- ✅ Raíz "Utility" explícita — aparece como raíz tanto en el árbol indentado como estructura organizadora de la tabla.
- ✅ Al menos 4 atributos distintos y no solapados — Rendimiento, Disponibilidad, Seguridad y Modificabilidad, cada uno cubre una preocupación arquitectónica distinta sin superposición.
- ✅ Refinamientos específicos del dominio — "checkout bajo carga pico", "catálogo/búsqueda", "gateway de pagos", "PCI-DSS", "takeover de cuentas", "nuevo método de pago", "reglas de precios/promociones"; ninguno es el nombre pelado del atributo.
- ✅ Cada hoja con las 6 partes separables, Respuesta y Medida de respuesta explícitas — las 8 hojas de la tabla tienen Fuente de estímulo, Estímulo, Ambiente, Artefacto, Respuesta y Medida de respuesta separadas con "·", cada una con un valor cuantificable (tiempos, porcentajes, conteos).
- ✅ Cada score (H/M/L, H/M/L) con justificación de una línea — las 8 hojas tienen su par de puntaje y una justificación de una línea que distingue explícitamente la razón de negocio de la razón de riesgo.
- ✅ Ninguna hoja es una meta agregada de SLA; cada una dispara por un único estímulo — cada hoja parte de un evento puntual (una confirmación de pago, una falla de gateway, un intento de acceso, un pedido de agregar un método de pago), no de un objetivo anual agregado como "99.9% de disponibilidad".
- ✅ Se señala el cuadrante (H,H) como prioritario — sección final identifica explícitamente las 3 hojas en (H,H) y las recomienda como primeras candidatas para ATAM.

**Resultado: PASA**
