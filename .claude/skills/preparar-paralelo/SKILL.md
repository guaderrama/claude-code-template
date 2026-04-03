---
name: preparar-paralelo
description: "Initialize git worktree directories for parallel Claude Code agents. Creates isolated copies of the repository so multiple agents can work on the same feature simultaneously. Use when the user says 'preparar paralelo', 'prepare parallel', 'setup worktrees', or before running /ejecutar-paralelo."
allowed-tools: Bash
argument-hint: "[feature-name] [number-of-worktrees]"
model: haiku
effort: low
---

# Inicializar Directorios Git Worktree Paralelos

## Variables
- NOMBRE_CARACTERISTICA: nombre de la feature
- NUMERO_DE_WORKTREES_PARALELOS: cantidad de worktrees a crear

## Proceso

1. Crear directorio `trees/`
2. Para cada worktree (1 a N):
   - Ejecutar `git worktree add -b NOMBRE_CARACTERISTICA-i ./trees/NOMBRE_CARACTERISTICA-i`
   - Ejecutar `cd trees/NOMBRE_CARACTERISTICA-i && git ls-files` para validar
3. Ejecutar `git worktree list` para verificar que todos los trees fueron creados correctamente

## Resultado

```
trees/
  |- <feature>-1/   (worktree 1, branch feature-1)
  |- <feature>-2/   (worktree 2, branch feature-2)
  +- <feature>-N/   (worktree N, branch feature-N)
```

Cada worktree contiene una copia identica del codigo en la rama actual, lista para que un agente trabaje de forma aislada.

## Manejo de Errores

- Si `git worktree add` falla, verificar que no exista ya un worktree con ese nombre (`git worktree list`)
- Si el directorio `trees/` ya existe, verificar contenido antes de sobrescribir
- En Windows, usar paths con forward slashes para compatibilidad
- Si un worktree falla la validacion, eliminarlo con `git worktree remove` y reintentar
