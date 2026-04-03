---
name: ejecutar-paralelo
description: "Create N parallel subagents that implement the same feature simultaneously in isolated git worktrees, enabling comparison of different implementations. Use when the user says 'ejecutar paralelo', 'parallel execution', 'run in parallel', or wants multiple competing implementations of the same feature."
allowed-tools: Read, Write, Edit, Bash, Glob, Grep
argument-hint: "[feature-name] [plan-file] [number-of-worktrees]"
model: sonnet
effort: medium
---

# Ejecucion de Version de Tareas Paralelas

## Variables
- NOMBRE_CARACTERISTICA: nombre de la feature
- PLAN_A_EJECUTAR: archivo con el plan de implementacion
- NUMERO_DE_WORKTREES_PARALELOS: cantidad de implementaciones paralelas

## Instrucciones

Crea NUMERO_DE_WORKTREES_PARALELOS nuevos subagentes que usan la herramienta Task para crear N versiones de la misma caracteristica en paralelo.

Asegurate de leer PLAN_A_EJECUTAR.

Esto permite construir concurrentemente la misma caracteristica en paralelo para probar y validar los cambios de cada subagente de forma aislada y luego elegir los mejores cambios.

## Estructura de Worktrees

```
trees/
  |- <NOMBRE_CARACTERISTICA>-1/   (agente 1)
  |- <NOMBRE_CARACTERISTICA>-2/   (agente 2)
  +- <NOMBRE_CARACTERISTICA>-N/   (agente N)
```

El codigo en cada worktree sera identico al codigo en la rama actual. Cada agente implementara independientemente el plan de ingenieria detallado en PLAN_A_EJECUTAR en su respectivo espacio de trabajo.

## Output

Cuando el subagente complete su trabajo, debe reportar sus cambios finales en un archivo `RESULTADOS.md` en la raiz de su respectivo espacio de trabajo.

Los agentes NO ejecutan pruebas u otro codigo - se enfocan solo en los cambios de codigo.
