# 🛡️ sei-quality-attributes

> **Plugin de skills para Claude Code** que implementa el método de "Atributos de Calidad" y "Árboles de Utilidad" de *Software Architecture in Practice* (Bass, Clements & Kazman — Caps. 4, 19 y 21).

[![Claude Code Plugin](https://img.shields.io/badge/Claude_Code-Plugin-7C3AED?style=for-the-badge&logo=anthropic&logoColor=white)](https://claude.ai)
[![Methodology](https://img.shields.io/badge/Methodology-writing--skills%20(TDD)-059669?style=for-the-badge)](./proceso-creacion-skills.md)
[![QA Suite](https://img.shields.io/badge/QA_Suite-24%2F24_PASA_(100%25)-2563EB?style=for-the-badge)](./testing.md)
[![License](https://img.shields.io/badge/License-MIT-D97706?style=for-the-badge)](#)

---

## 👥 Integrantes del grupo

| Nombre y apellido | Rol / contribución |
| :--- | :--- |
| **Jeremías Aguilar** | Desarrollo de skills & testing RED/GREEN |
| **Francisco Lozano** | Diseño metodológico & QA Suite |
| **Ivo Iriarte** | Refactorización de loopholes & documentación |
| **Juan Emilio Pardo** | Validación SEI & trazabilidad de atributos |

---

## 📌 Descripción general

**`sei-quality-attributes`** le enseña a Claude Code a actuar como un **Arquitecto de Software Senior** especializado en las metodologías del *Software Engineering Institute (SEI)*. El plugin provee 3 skills autocontenidas e independientes que automatizan la especificación, auditoría y priorización de Requisitos No Funcionales (RNF) mediante escenarios formales de 6 partes y árboles de utilidad (ATAM).

### 🛠️ Las 3 skills incluidas

```
                  ┌─────────────────────────────────────────┐
                  │          sei-quality-attributes          │
                  └────────────────────┬────────────────────┘
                                       │
         ┌─────────────────────────────┼─────────────────────────────┐
         ▼                             ▼                             ▼
┌───────────────────┐        ┌───────────────────┐        ┌───────────────────┐
│ generar-escenario │        │ chequear-comple-  │        │  construir-arbol- │
│    -calidad       │        │  tud-escenario    │        │     utilidad      │
└────────┬──────────┘        └─────────┬─────────┘        └─────────┬─────────┘
         │                             │                            │
  Crea escenarios               Audita escenarios           Arma árboles ATAM
  formales (6 partes)           existentes (✅/⚠️/❌)           priorizados (H/M/L)
```

1. **`generar-escenario-calidad`**: Redacta escenarios de calidad generales o concretos cumpliendo estrictamente el template de 6 partes del SEI. Evita respuestas ambiguas o listas de opciones sin resolver.
2. **`chequear-completitud-escenario`**: Realiza una auditoría rigurosa fila por fila sobre un escenario redactado en texto plano, identifica faltantes, detecta mezclas de atributos y propone reescrituras formales.
3. **`construir-arbol-utilidad`**: Sintetiza requisitos de arquitectura en un árbol de utilidad estructurado (`Utility → Atributo → Refinamiento → Escenario`), evaluando **(Valor de negocio, Riesgo técnico)** en escala `H/M/L` con justificación explícita por hoja.

---

## 📐 El template de 6 partes del SEI

Todas las skills operan y garantizan la presencia de las **6 partes fundamentales** definidas por Bass et al.:

```
┌────────────────────────────────────────────────────────────────────────────────┐
│ 1. Fuente de Estímulo │ ¿Quién o qué genera el evento? (Atacante, Usuario, BD) │
│ 2. Estímulo           │ La condición o evento que llega al sistema             │
│ 3. Ambiente           │ Estado del sistema (Carga pico, Normal, Recuperación)  │
│ 4. Artefacto          │ Módulo o componente afectado (NO "el sistema" genérico)│
│ 5. Respuesta          │ Acción ejecutada tras el estímulo                      │
│ 6. Medida de Respuesta│ Métrica cuantificable (s, ms, %, req/s, horas-persona) │
└────────────────────────────────────────────────────────────────────────────────┘
```

---

## 🚀 Guía de uso rápido y disparadores

### Instalación

Copiá la carpeta de skills a tu directorio personal de configuración de Claude Code:

```bash
# Copiar las skills a tu entorno local de Claude Code
cp -r skills/* ~/.claude/skills/
```

### Ejemplos de prompts por skill

| Skill | Cuándo se activa (triggers) | Ejemplo de prompt |
| :--- | :--- | :--- |
| **`generar-escenario-calidad`** | "Escribir un escenario de...", nombra un atributo (clásico o no clásico), o pide formalizar un ASR. | *"Generá un escenario de disponibilidad para una app de banca móvil durante el pago de sueldos."* |
| **`chequear-completitud-escenario`** | Pega un texto y pregunta "¿está completo?", "¿qué le falta a este escenario?", o audita un requisito. | *"Chequeá si este escenario está completo: 'El servidor de BD falla y el sistema se recupera automáticamente'."* |
| **`construir-arbol-utilidad`** | "Armar árbol de utilidad", "Priorizar requisitos de calidad", o prepara una evaluación ATAM. | *"Construí un árbol de utilidad para un e-commerce considerando disponibilidad, rendimiento y seguridad."* |

---

## 🔄 Matriz de entradas y salidas

| Skill | Inputs requeridos | Comportamiento si faltan datos | Entregable / output |
| :--- | :--- | :--- | :--- |
| **`generar-escenario-calidad`** | Atributo de calidad + Dominio/Sistema. | Marca celdas dudosas como `[A DEFINIR: ...]` o supuestos explícitos. Nunca inventa en silencio. | Tabla cerrada de 6 filas + Redacción del escenario en prosa de una sola oración. |
| **`chequear-completitud-escenario`** | Escenario en prosa o borrador. | Evalúa cada parte de forma independiente sin autocompletar sin avisar. | Matriz con estado `✅/⚠️/❌` por fila + Propuesta concreta de llenado + Alerta de mezcla de atributos. |
| **`construir-arbol-utilidad`** | Dominio + atributos o metas de negocio. | Si no hay metas, infiere 3-4 atributos relevantes. Si no hay datos para el riesgo, lo declara abiertamente. | Árbol jerárquico indentado + Tabla con (Valor, Riesgo) + Identificación del cuadrante crítico `(H, H)`. |

---

## 🧪 Metodología de desarrollo: `writing-skills` (TDD)

Las skills no se escribieron a mano alzada. Se construyeron siguiendo una **disciplina tipo TDD para instrucciones en lenguaje natural** (`superpowers:writing-skills`), respaldada por una regla inflexible: *ninguna skill se commitea sin pasar su propio test en GREEN sobre un subagente fresco*.

```
   🔴 RED              🟢 GREEN            🔵 REFACTOR            🛡️ REGRESIÓN
┌──────────────┐    ┌──────────────┐    ┌─────────────────┐    ┌──────────────────┐
│ Captura de   │───>│ Subagente    │───>│ Escenarios de   │───>│ QA Suite de      │
│ fallas sin   │    │ fresco con   │    │ presión para    │    │ 24 casos totales │
│ skill        │    │ skill activa │    │ cerrar loopholes│    │ (8 por skill)    │
└──────────────┘    └──────────────┘    └─────────────────┘    └──────────────────┘
```

### Hallazgos principales del baseline (fallas RED corregidas)

1. **Taxonomía en vez de instancia**: El modelo base solía listar menús de opciones en lugar de comprometerse con un valor único.
2. **Escrutinio superficial**: Al ver una parte "presente", dejaba de exigir métricas numéricas exactas.
3. **Omisión recurrente de Fuente y Ambiente**: Eran las dos partes más salteadas en texto plano.
4. **Hojas narrativas sin justificación**: Los árboles de utilidad mezclaban las 6 partes en párrafos confusos sin justificar los puntajes **H/M/L**.

### Resumen de cobertura de tests

| Nivel de test | Cantidad de casos | Resultado |
| :--- | :---: | :---: |
| **Baseline (RED)** | 4 casos | ❌ Identified 5 critical failure patterns |
| **Post (GREEN)** | 4 casos | ✅ 100% Corrected |
| **Refactor (Presión / Loopholes)** | 3 casos | ✅ 100% Passed with methodology alerts |
| **QA Suite (Regresión Broad)** | 24 casos | ✅ **24/24 PASA (100%)** |
| **TOTAL EVIDENCIAS** | **31 casos** | **Garantía total de comportamiento** |

*El detalle minucioso de cada prueba se encuentra en [`testing.md`](./testing.md) y [`proceso-creacion-skills.md`](./proceso-creacion-skills.md).*

---

## 📁 Estructura del repositorio

```text
.
├── .claude-plugin/               
│   └── plugin.json
├── skills/                       # Skills del plugin para Claude Code
│   ├── chequear-completitud-escenario/
│   │   ├── references/
│   │   │   └── checklist-completitud.md
│   │   └── SKILL.md
│   ├── construir-arbol-utilidad/
│   │   ├── references/
│   │   │   └── ejemplo-arbol-salud.md
│   │   └── SKILL.md
│   └── generar-escenario-calidad/
│       ├── references/
│       │   └── plantilla-6-partes.md
│       └── SKILL.md            
├── tests/                        # Evidencias de ejecuciones
│   ├── baseline/                 # Respuestas crudas sin skill (RED)
│   ├── post/                     # Respuestas con skill activa (GREEN)
│   ├── qa-suite/                 # Batería de regresión de 24 casos
│   ├── refactor/                 # Pruebas de resistencia a loopholes
│   └── COMPARISON.md             # Matriz comparativa caso por caso RED vs GREEN
├── proceso-creacion-skills.md    # Explicación detallada del proceso TDD (RED-GREEN-REFACTOR)
├── README.md                     # Visión general, guía de instalación y manual del plugin
└── testing.md                    # Reporte completo de la QA Suite (24 casos + 7 iniciales)
```

---

## 📖 Referencias bibliográficas

- **Bass, L., Clements, P., & Kazman, R.** (2021). *Software Architecture in Practice* (4th ed.). Addison-Wesley Professional.
  - **Capítulo 4**: *Understanding Quality Attributes & Quality Attribute Scenarios*.
  - **Capítulo 19**: *Architectural Reviews & ATAM (Architecture Tradeoff Analysis Method)*.
  - **Capítulo 21**: *Utility Trees and Risk Identification*.