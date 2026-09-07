# sei-quality-attributes

Plugin de skills para Claude Code que aplica el método de atributos de calidad y árboles de utilidad de *Software Architecture in Practice* (Bass, Clements & Kazman) — capítulos 4, 19 y 21.

## Skills incluidas

- **generar-escenario-calidad** — genera escenarios (generales o concretos) con el template de 6 partes del SEI.
- **chequear-completitud-escenario** — audita un escenario ya escrito contra las 6 partes y propone cómo completarlo.
- **construir-arbol-utilidad** — arma un árbol de utilidad (Utility → Atributo → Refinamiento → Escenario) puntuado en H/M/L.

## Instalación

Copiá la carpeta `skills/` a tu directorio de skills personal:

```bash
cp -r skills/* ~/.claude/skills/
```

O instalá como plugin local desde este repo si tu runtime lo soporta.

## Evidencia de que funciona

Ver `tests/baseline/` (comportamiento sin la skill), `tests/post/` (comportamiento con la skill), `tests/refactor/` (los 3 escenarios de presión/loopholes usados para el REFACTOR) y `tests/COMPARISON.md` para la comparación caso por caso.

## Fuente

Contenido de dominio parafraseado de *Software Architecture in Practice* (Bass, Clements, Kazman), capítulos 4, 19 y 21.
