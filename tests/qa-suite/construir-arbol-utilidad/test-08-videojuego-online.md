## Prompt

Armá un árbol de utilidad para un videojuego online multijugador, con foco en rendimiento (latencia de red) y escalabilidad ante picos de jugadores concurrentes, más al menos 2 atributos adicionales.

## Criterios de aceptación esperados

- Rendimiento/latencia y escalabilidad (puede tratarse como parte de rendimiento o disponibilidad, según el criterio de la skill, pero debe estar cubierta explícitamente) están presentes.
- Refinamientos de dominio gamer (netcode/latencia, matchmaking, anti-cheat, etc.).
- Al menos una medida de respuesta en milisegundos de latencia o en cantidad de jugadores concurrentes soportados.
- Al menos 4 atributos en total, hojas completas.

## Respuesta obtenida

# Árbol de utilidad — Videojuego online multijugador

Elegí 4 atributos de calidad: **Rendimiento** (foco en latencia de red/netcode) y **Escalabilidad** (foco en picos de jugadores concurrentes), pedidos explícitamente, más **Seguridad** y **Disponibilidad**, que son críticos en este dominio (cheating y caídas de servidor son las dos causas más comunes de fuga de jugadores en juegos online).

## Formato árbol indentado

```
Utility
├── Rendimiento
│   ├── Latencia de sincronización de estado (netcode)
│   │   └── [R1.1] Sincronización de inputs bajo partida con 100 jugadores — (H, H)
│   └── Responsividad de acciones del jugador (input lag / predicción cliente)
│       └── [R1.2] Predicción local y reconciliación en combate — (H, M)
├── Escalabilidad
│   ├── Picos de jugadores concurrentes en servidores de partida
│   │   └── [R2.1] Auto-scaling ante spike de lanzamiento/evento — (H, H)
│   └── Escalado del servicio de matchmaking
│       └── [R2.2] Cola de matchmaking en horario pico — (M, M)
├── Seguridad
│   ├── Anti-cheat
│   │   └── [R3.1] Detección de software de trampa en partida — (H, H)
│   └── Protección contra ataques de red
│       └── [R3.2] Mitigación de ataque DDoS — (M, M)
└── Disponibilidad
    ├── Tolerancia a fallos del servidor de partida
    │   └── [R4.1] Failover ante caída de servidor de partida en curso — (H, M)
    └── Recuperación ante falla de región de datacenter
        └── [R4.2] Redirección de tráfico tras caída de región — (M, L)
```

## Formato tabla

| Atributo | Refinamiento | Escenario (6 partes) | (Valor, Riesgo) | Justificación |
|---|---|---|---|---|
| Rendimiento | Latencia de sincronización de estado (netcode) | Fuente: jugador conectado a partida en curso · Estímulo: envía un paquete de input (movimiento/disparo) al servidor · Ambiente: operación normal, shard con hasta 100 jugadores concurrentes, red pública con jitter variable · Artefacto: servidor de juego y módulo de sincronización de estado (netcode) · Respuesta: el servidor procesa el input, actualiza el estado autoritativo y lo propaga a los clientes relevantes · Medida de respuesta: latencia extremo-a-extremo (RTT) ≤ 80 ms en percentil 95 | (H, H) | H de negocio: la latencia es el corazón de la experiencia en un juego competitivo online, si es mala los jugadores se van; H de riesgo: sincronizar estado en tiempo real con jitter de red variable es técnicamente muy difícil de garantizar de forma consistente |
| Rendimiento | Responsividad de acciones del jugador (input lag) | Fuente: jugador · Estímulo: presiona un botón de acción (disparo) durante combate activo · Ambiente: carga pico, servidor con ocupación máxima de jugadores por shard · Artefacto: cliente del juego y capa de predicción local · Respuesta: el cliente renderiza la acción localmente (predicción) y reconcilia luego con el estado autoritativo del servidor · Medida de respuesta: tiempo entre input y renderizado ≤ 50 ms; corrección de reconciliación imperceptible (<100 ms) en el 99% de los casos | (H, M) | H de negocio: el input lag percibido define la sensación de "responsividad" del juego; M de riesgo: la predicción del lado del cliente (rollback netcode) es un patrón conocido en la industria pero exige tuning fino |
| Escalabilidad | Picos de jugadores concurrentes en servidores de partida | Fuente: evento de marketing/lanzamiento de temporada · Estímulo: aumento súbito de jugadores intentando conectarse simultáneamente · Ambiente: pico de tráfico, de 10.000 a 100.000 jugadores concurrentes en 10 minutos · Artefacto: infraestructura de orquestación de servidores de partida (auto-scaler) · Respuesta: el sistema aprovisiona automáticamente nuevas instancias de servidores de partida · Medida de respuesta: soporta hasta 100.000 jugadores concurrentes sin degradar el tiempo de emparejamiento por encima de 30 s, y agrega capacidad en menos de 3 minutos | (H, H) | H de negocio: los picos en lanzamientos/eventos son momentos críticos de reputación y monetización; H de riesgo: el auto-scaling de servidores de partida con estado (stateful) es complejo y deja poco margen de error bajo picos reales |
| Escalabilidad | Escalado del servicio de matchmaking | Fuente: cola de jugadores buscando partida · Estímulo: incremento del volumen de solicitudes de matchmaking en horario pico (viernes noche) · Ambiente: carga pico, 50.000 solicitudes simultáneas · Artefacto: servicio de matchmaking (cola distribuida) · Respuesta: el servicio distribuye la carga entre múltiples instancias y arma partidas balanceadas · Medida de respuesta: tiempo de espera promedio para encontrar partida ≤ 45 s con 50.000 solicitudes concurrentes | (M, M) | M de negocio: importante para la experiencia pero no bloquea el juego si tarda un poco más; M de riesgo: escalar colas distribuidas es un patrón conocido, pero balancear partidas justas agrega complejidad |
| Seguridad | Anti-cheat | Fuente: jugador malicioso · Estímulo: usa un software de trampa (aimbot/wallhack) que modifica memoria del cliente o inyecta paquetes anómalos · Ambiente: operación normal, partida en curso · Artefacto: sistema anti-cheat (validación server-side + cliente) · Respuesta: el sistema detecta el comportamiento anómalo, invalida las acciones sospechosas y marca al jugador para revisión/baneo · Medida de respuesta: detección en menos de 2 minutos de juego, con tasa de falsos positivos menor al 0,1% | (H, H) | H de negocio: el cheating destruye la confianza de la comunidad y la retención a largo plazo; H de riesgo: la detección de trampas es una carrera armamentista constante, difícil de garantizar sin falsos positivos |
| Seguridad | Protección contra ataques de red | Fuente: atacante externo · Estímulo: lanza un ataque de denegación de servicio distribuido (DDoS) contra matchmaking/servidores de juego · Ambiente: bajo ataque activo, tráfico malicioso de gran volumen · Artefacto: infraestructura de red y capa de mitigación DDoS · Respuesta: el sistema de mitigación detecta y filtra el tráfico malicioso, manteniendo el servicio disponible para jugadores legítimos · Medida de respuesta: degradación de latencia menor al 20% para jugadores legítimos durante un ataque de hasta 50 Gbps | (M, M) | M de negocio: un ataque DDoS afecta la reputación pero es menos frecuente que otros riesgos del día a día; M de riesgo: existen soluciones de mitigación probadas (CDN/WAF) pero dimensionarlas correctamente sigue siendo un desafío |
| Disponibilidad | Tolerancia a fallos del servidor de partida | Fuente: falla de hardware/software del servidor de partida · Estímulo: el proceso del servidor cae de forma inesperada durante una partida activa · Ambiente: operación normal, partida en curso con jugadores conectados · Artefacto: orquestador de servidores de partida (game server manager) · Respuesta: el sistema detecta la caída y migra/reconecta a los jugadores a un servidor de respaldo · Medida de respuesta: reconexión a un servidor de reemplazo en menos de 15 s, con pérdida de estado de partida menor al 5% | (H, M) | H de negocio: perder una partida en curso por caída de servidor genera frustración y quejas directas de los jugadores; M de riesgo: los patrones de checkpointing/reconexión ya están bien documentados en la industria de juegos online |
| Disponibilidad | Recuperación ante falla de región de datacenter | Fuente: falla de infraestructura de datacenter/región cloud · Estímulo: una región completa de datacenter queda fuera de servicio · Ambiente: falla mayor de infraestructura, jugadores distribuidos en múltiples regiones · Artefacto: arquitectura multi-región del backend (matchmaking, autenticación, perfiles) · Respuesta: el sistema redirige el tráfico de los jugadores afectados a una región alternativa · Medida de respuesta: servicio restablecido para los jugadores afectados en menos de 5 minutos, con disponibilidad global mensual ≥ 99,9% | (M, L) | M de negocio: es un evento raro pero afecta a muchos jugadores a la vez si ocurre; L de riesgo: las arquitecturas multi-región son un patrón maduro y ampliamente documentado en proveedores cloud |

## Candidatas a analizar primero (cuadrante H,H)

Las hojas con mayor valor de negocio Y mayor riesgo técnico son las que conviene evaluar primero en una evaluación de arquitectura (ATAM):

1. **[R1.1] Sincronización de inputs bajo partida con 100 jugadores** (Rendimiento) — (H, H)
2. **[R2.1] Auto-scaling ante spike de lanzamiento/evento** (Escalabilidad) — (H, H)
3. **[R3.1] Detección de software de trampa en partida** (Seguridad) — (H, H)

Estas tres combinan lo que el negocio no puede permitirse fallar con lo que técnicamente es más incierto lograr, por lo que son las candidatas prioritarias de análisis arquitectónico.

## Veredicto

- ✅ Rendimiento/latencia y escalabilidad están presentes explícitamente — Rendimiento y Escalabilidad son atributos de primer nivel en el árbol, cada uno con refinamientos y hojas propias (R1.1, R1.2, R2.1, R2.2).
- ✅ Refinamientos de dominio gamer presentes — netcode/latencia (R1.1), predicción de input/reconciliación (R1.2), matchmaking (R2.2), anti-cheat (R3.1) y auto-scaling de servidores de partida (R2.1) son específicos del dominio, no genéricos.
- ✅ Al menos una medida en ms de latencia o cantidad de jugadores concurrentes — R1.1 mide RTT ≤ 80 ms p95, R1.2 mide ≤ 50 ms, y R2.1 mide soporte de hasta 100.000 jugadores concurrentes.
- ✅ Al menos 4 atributos en total con hojas completas — hay 4 atributos (Rendimiento, Escalabilidad, Seguridad, Disponibilidad) con 8 hojas, cada una con las 6 partes del escenario, puntaje (Valor, Riesgo) y justificación de una línea.

**Resultado: PASA**
