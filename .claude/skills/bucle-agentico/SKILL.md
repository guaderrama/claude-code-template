---
name: bucle-agentico
description: "Systematic problem-solving methodology using deconstruction, hierarchical planning, and iterative execution from 0% to 100%. Use when tackling complex multi-step features, end-to-end implementations, large refactors, or any problem that needs structured decomposition with progress tracking. Triggers on 'bucle agentico', 'systematic approach', 'complex feature', or when a task clearly requires 5+ steps."
allowed-tools: Read, Write, Edit, Bash, Glob, Grep
model: opus
effort: high
---

# Bucle Agentico

Metodologia sistematica para resolver problemas complejos mediante deconstruccion, planificacion jerarquica y ejecucion iterativa.

## Proceso de Ejecucion

### 1. DELIMITAR PROBLEMA(S)
- Identificar el problema principal
- Detectar subproblemas relacionados
- Definir criterios de exito (que es "100% completo"?)
- Establecer scope y limitaciones

### 2. INGENIERIA INVERSA
- Que componentes/partes tiene el problema?
- Que dependencias existen? (orden de ejecucion)
- Que patrones del codebase son aplicables?
- Que casos edge deben considerarse?

### 3. PLANIFICACION JERARQUICA (TodoTask)
- Usar TodoWrite para crear estructura de tareas
- Organizar en niveles (tareas -> subtareas)
- Asignar dependencias cronologicas
- Estimar complejidad relativa

**Estructura de Plan:**
```
|- Tarea Principal 1
|  |- Subtarea 1.1
|  |- Subtarea 1.2
|  +- Subtarea 1.3 (depende de 1.1)
|- Tarea Principal 2 (depende de Tarea 1)
|  |- Subtarea 2.1
|  +- Subtarea 2.2
+- Tarea de Validacion (depende de todas)
```

### 4. EJECUCION ITERATIVA (0->100)

**Bucle por cada tarea:**
```
WHILE tareas pendientes:
  1. Marcar tarea como in_progress
  2. Ejecutar tarea
  3. Validar resultado
  4. IF error:
       - Analizar causa
       - Ajustar plan si necesario
       - Reintentar
     ELSE:
       - Marcar como completed
       - Actualizar % progreso
  5. Pasar a siguiente tarea
```

**Principios:**
- Una tarea a la vez (evitar paralelismo hasta dominarlo)
- Validar antes de marcar como completada
- Documentar decisiones importantes
- Refactorizar plan si aparecen nuevos requisitos

### 5. VALIDACION CONTINUA
- Despues de cada tarea: validacion local
- Despues de cada grupo de tareas: validacion de integracion
- Al final: validacion end-to-end

### 6. REPORTE FINAL
- Estado de todas las tareas (completed/pending/failed)
- Problemas encontrados y soluciones aplicadas
- Deuda tecnica pendiente (si aplica)
- Siguientes pasos recomendados

## Cuando Usar

- Problemas complejos con multiples partes
- Features nuevas end-to-end
- Refactorings grandes
- Debugging sistematico de bugs complejos
- NO para tareas simples de 1-2 pasos

## Referencias

Ver `references/ejemplos-detallados.md` para ejemplos completos de ingenieria inversa, planes TodoTask, y casos de uso reales.
