## Respuesta (con skill)

**Nota antes de entregar el árbol:** un árbol de utilidad con un solo atributo de calidad y una sola hoja cumple el formato que pediste, pero pierde el propósito central de la herramienta: el árbol de utilidad sirve para **priorizar entre múltiples escenarios en competencia** por recursos y tiempo de evaluación (ATAM). Con una sola hoja no hay nada contra qué compararla — el par (Valor de negocio, Riesgo técnico) queda como un dato aislado, no como un ranking. Para una preparación real de ATAM te recomiendo expandir a al menos 3-4 atributos de calidad (por ejemplo, además de seguridad: disponibilidad, rendimiento y modificabilidad), con 1-3 refinamientos cada uno, para que el cuadrante (H,H) tenga sentido comparativo. Dicho esto, acá está el árbol de una sola hoja tal como lo pediste.

### Árbol indentado

```
Utility
└── Seguridad
    └── Confidencialidad de la factura electrónica en tránsito y en reposo
        └── Escenario (hoja):
            - Fuente de estímulo: atacante externo no autenticado
            - Estímulo: intento de interceptar o acceder a una factura electrónica (con datos fiscales y del cliente) sin autorización
            - Ambiente: operación normal, factura ya emitida y almacenada, expuesta a través de la API de consulta/descarga
            - Artefacto: servicio de emisión/consulta de facturas electrónicas y su base de datos
            - Respuesta: el sistema rechaza el acceso no autorizado, mantiene la factura cifrada en tránsito (TLS) y en reposo, y registra el intento junto con una alerta al equipo de seguridad
            - Medida de respuesta: 100% de los intentos de acceso no autorizado son bloqueados y quedan registrados; alerta emitida en menos de 1 minuto; 0 facturas expuestas en texto plano
        → (Valor de negocio: H, Riesgo técnico: H)
```

### Tabla

| Atributo | Refinamiento | Escenario (resumen 6 partes) | (Valor, Riesgo) | Justificación |
|---|---|---|---|---|
| Seguridad | Confidencialidad de la factura electrónica en tránsito y en reposo | Fuente: atacante externo no autenticado · Estímulo: intento de interceptar o acceder sin autorización a una factura electrónica · Ambiente: operación normal, factura ya emitida, expuesta vía API de consulta/descarga · Artefacto: servicio de emisión/consulta de facturas y su base de datos · Respuesta: rechazo del acceso no autorizado, cifrado en tránsito (TLS) y en reposo, registro y alerta al equipo de seguridad · Medida: 100% de intentos bloqueados y registrados, alerta en < 1 min, 0 facturas expuestas en texto plano | (H, H) | H de negocio: una factura electrónica contiene datos fiscales y personales — su exposición implica sanciones del organismo fiscal (ej. AFIP/SAT según el país) y pérdida de confianza del cliente; H de riesgo: la API de facturación suele estar expuesta a integraciones externas (clientes, proveedores, organismo fiscal), lo que amplía la superficie de ataque y aún no está claro si ya pasó una auditoría de seguridad completa |

**Candidata a analizar primero en la evaluación de arquitectura:** al haber una sola hoja, cae automáticamente en el cuadrante (H,H) — pero, como se señaló arriba, esto no es una priorización real: es la única opción disponible porque no hay otras hojas ni atributos con los que compararla. Con un solo par de datos no se puede decidir "qué ver primero" en ATAM; esa decisión requiere al menos varias hojas en distintos cuadrantes.
