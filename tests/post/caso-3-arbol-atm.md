## Respuesta (con skill)

# Árbol de utilidad — Cajero automático (ATM)

Atributos de calidad considerados (5, cubriendo el mínimo de 4 pedido): **Seguridad, Disponibilidad, Rendimiento, Usabilidad y Modificabilidad**.

## Árbol indentado

```
Utility
├── Seguridad
│   ├── Autenticación / bloqueo por intentos fallidos de PIN
│   │   └── [Escenario 1.1] — (H, L)
│   └── Protección contra manipulación física del lector de tarjetas (skimming)
│       └── [Escenario 1.2] — (H, H)
├── Disponibilidad
│   ├── Recuperación ante falla de conexión con el core bancario
│   │   └── [Escenario 2.1] — (H, M)
│   └── Falla de hardware: atasco del dispensador de billetes
│       └── [Escenario 2.2] — (H, M)
├── Rendimiento
│   ├── Tiempo de respuesta en retiro de efectivo
│   │   └── [Escenario 3.1] — (M, L)
│   └── Tiempo de respuesta en consulta de saldo bajo carga pico
│       └── [Escenario 3.2] — (M, M)
├── Usabilidad
│   ├── Accesibilidad para usuarios con discapacidad visual
│   │   └── [Escenario 4.1] — (M, M)
│   └── Cancelación de operación por el usuario
│       └── [Escenario 4.2] — (M, L)
└── Modificabilidad
    └── Agregar nuevo tipo de transacción (pago de servicios)
        └── [Escenario 5.1] — (L, M)
```

## Tabla de escenarios (formato de 6 partes)

| Atributo | Refinamiento | Escenario (6 partes) | (Valor, Riesgo) | Justificación |
|---|---|---|---|---|
| Seguridad | Autenticación / bloqueo por PIN incorrecto | **Fuente:** usuario (titular o atacante) ingresando el PIN · **Estímulo:** se ingresa un PIN incorrecto por 3ra vez consecutiva · **Ambiente:** operación normal, ATM en línea con el core · **Artefacto:** módulo de autenticación del ATM · **Respuesta:** el sistema retiene la tarjeta, cancela la sesión y notifica al banco emisor · **Medida:** tarjeta retenida en el 100% de los casos al 3er intento fallido; notificación al core en <5s | (H, L) | H de negocio: evita fraude y pérdida económica directa; L de riesgo: regla de negocio simple, patrón estándar ya probado en la industria |
| Seguridad | Protección contra manipulación física del lector (skimming) | **Fuente:** dispositivo de skimming instalado por un atacante · **Estímulo:** el sensor anti-manipulación detecta un objeto extraño acoplado al lector · **Ambiente:** operación 24/7, ATM desatendido · **Artefacto:** módulo lector de tarjetas / hardware del ATM · **Respuesta:** se deshabilita el lector, se bloquean nuevas transacciones y se alerta a seguridad y monitoreo remoto · **Medida:** ATM pasa a "fuera de servicio" en <10s desde la detección; alerta recibida por el centro de monitoreo en <1 min | (H, H) | H de negocio: el fraude por skimming implica pérdidas económicas y daño reputacional graves; H de riesgo: la detección confiable de hardware manipulado es compleja, con riesgo real de falsos positivos/negativos |
| Disponibilidad | Recuperación ante falla de conexión con el core | **Fuente:** infraestructura de red / enlace de comunicaciones · **Estímulo:** se pierde la conexión con el servidor central durante una transacción · **Ambiente:** horario de operación normal · **Artefacto:** módulo de comunicaciones del ATM · **Respuesta:** se cancela la transacción de forma segura (sin debitar ni entregar efectivo), se informa al usuario y se reintenta por enlace de respaldo · **Medida:** conmutación al enlace de respaldo en <15s; 0% de discrepancias entre saldo del banco y efectivo entregado | (H, M) | H de negocio: un error aquí implica pérdida de dinero o de confianza del cliente; M de riesgo: el failover es una solución conocida, pero exige pruebas exhaustivas de consistencia transaccional |
| Disponibilidad | Falla de hardware: atasco del dispensador | **Fuente:** mecanismo dispensador de efectivo (hardware) · **Estímulo:** el sensor detecta un atasco de billetes durante la entrega · **Ambiente:** operación normal, alta demanda de efectivo · **Artefacto:** módulo dispensador de efectivo · **Respuesta:** se detiene la dispensación, se revierte el débito si no se completó la entrega, y se marca el ATM fuera de servicio para esa operación · **Medida:** reversión del débito en <30s; 0 casos de débito sin entrega; ticket de servicio técnico generado automáticamente | (H, M) | H de negocio: afecta directamente la confianza y puede generar reclamos económicos; M de riesgo: escenario conocido, con mecanismos de reversión ya estandarizados en el dominio bancario |
| Rendimiento | Tiempo de respuesta en retiro de efectivo | **Fuente:** usuario del ATM · **Estímulo:** solicitud de retiro de efectivo por un monto válido · **Ambiente:** operación normal, carga promedio · **Artefacto:** servicio de procesamiento de transacciones del ATM · **Respuesta:** se autoriza, debita la cuenta y dispensa el efectivo · **Medida:** tiempo total desde confirmación hasta entrega, p95 < 20s | (M, L) | M de negocio: importante para la experiencia pero tolera demoras ocasionales pequeñas; L de riesgo: operación estándar, ya optimizada en productos ATM existentes |
| Rendimiento | Consulta de saldo bajo carga pico | **Fuente:** usuario del ATM · **Estímulo:** solicitud de consulta de saldo · **Ambiente:** horario pico (ej. día de pago de salarios, múltiples ATMs consultando el core) · **Artefacto:** servicio de consulta de saldo / core bancario · **Respuesta:** se devuelve el saldo actualizado · **Medida:** tiempo de respuesta p95 < 3s con 1000 consultas concurrentes | (M, M) | M de negocio: demoras cortas son tolerables; M de riesgo: depende de la capacidad del core bancario, parcialmente fuera del control del equipo del ATM |
| Usabilidad | Accesibilidad para usuarios con discapacidad visual | **Fuente:** usuario con discapacidad visual · **Estímulo:** se activa el modo de asistencia auditiva (audio jack) al iniciar sesión · **Ambiente:** operación normal · **Artefacto:** interfaz de usuario del ATM (software + hardware de audio) · **Respuesta:** el sistema guía toda la operación mediante audio sincronizado con el teclado numérico braille · **Medida:** 100% de las operaciones (consulta, retiro, depósito) completables solo con audio y teclado, sin necesidad de leer la pantalla | (M, M) | M de negocio: requisito regulatorio de accesibilidad en muchos países, aunque no es el uso mayoritario; M de riesgo: exige sincronización cuidadosa entre hardware de audio y flujo de UI |
| Usabilidad | Cancelación de operación por el usuario | **Fuente:** usuario del ATM · **Estímulo:** se presiona "Cancelar" en cualquier paso antes de la confirmación final · **Ambiente:** operación normal · **Artefacto:** interfaz de usuario / módulo de control de transacción · **Respuesta:** se aborta la operación en curso, no se realiza ningún cargo, se expulsa la tarjeta y se vuelve a la pantalla inicial · **Medida:** tarjeta expulsada y pantalla reiniciada en <3s; 0% de cargos indebidos en operaciones canceladas | (M, L) | M de negocio: mejora la confianza y satisfacción del usuario, aunque no es la operación principal; L de riesgo: funcionalidad de control de flujo simple y bien establecida |
| Modificabilidad | Agregar nuevo tipo de transacción (pago de servicios) | **Fuente:** equipo de desarrollo del banco · **Estímulo:** se solicita agregar "pago de servicios" (luz, agua, etc.) como nuevo tipo de transacción · **Ambiente:** tiempo de diseño, próximo ciclo de release · **Artefacto:** módulo de gestión de transacciones del ATM · **Respuesta:** se agrega el nuevo tipo sin modificar los módulos de retiro, depósito ni de seguridad · **Medida:** cambio confinado a ≤2 componentes (módulo de transacciones + UI); ≤80 horas-persona; sin regresión en pruebas de las transacciones existentes | (L, M) | L de negocio: mejora deseable de producto, no crítica para la operación core del ATM; M de riesgo: depende de qué tan desacoplado esté hoy el módulo de transacciones, no confirmado |

## Candidatas a analizar primero (cuadrante H, H)

La única hoja en el cuadrante **(H, H)** es **Seguridad → Protección contra manipulación física del lector de tarjetas (skimming)**: alto valor de negocio (evita fraude y daño reputacional grave) combinado con alto riesgo técnico (detección confiable de manipulación de hardware, propensa a falsos positivos/negativos). Esta hoja debería priorizarse en la evaluación de arquitectura (ATAM) sobre el resto, que caen en cuadrantes de menor urgencia combinada (H,M / H,L / M,M / M,L / L,M).
