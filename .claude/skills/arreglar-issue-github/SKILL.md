---
name: arreglar-issue-github
description: "Analyze and fix GitHub issues end-to-end: reads the issue, finds relevant code, implements the fix, runs tests, and creates a PR. Use when the user says 'fix issue', 'arreglar issue', provides a GitHub issue number, or references a bug tracked in GitHub Issues."
allowed-tools: Read, Write, Edit, Bash, Glob, Grep
argument-hint: "[issue-number-or-url]"
model: sonnet
effort: medium
---

# Arreglar Issue de GitHub

Analiza y arregla un issue de GitHub de principio a fin.

## Proceso

1. **Obtener detalles del issue**
   - Usa `gh issue view` para obtener los detalles del issue
   - Entiende el problema descrito en el issue

2. **Investigar el codebase**
   - Busca en el codebase los archivos relevantes
   - Identifica la causa raíz del problema

3. **Implementar la corrección**
   - Implementa los cambios necesarios para arreglar el issue
   - Sigue las convenciones del proyecto

4. **Validar**
   - Escribe y ejecuta pruebas para verificar la corrección
   - Asegura que el código pase linting y verificación de tipos

5. **Crear commit y PR**
   - Crea un mensaje de commit descriptivo que referencia el issue (e.g., `fix(scope): description - closes #123`)
   - Hace push a una rama feature: `git push -u origin fix/issue-<number>`
   - Crea PR con `gh pr create --title "Fix #<number>: description" --body "Closes #<number>\n\n## Changes\n- ..."`
   - El PR DEBE referenciar el issue original con "Closes #N" o "Fixes #N"

Recuerda usar GitHub CLI (`gh`) para todas las tareas relacionadas con GitHub.
