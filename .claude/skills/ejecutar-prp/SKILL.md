---
name: ejecutar-prp
description: "Execute a Product Requirements Proposal (PRP) file to implement a feature end-to-end: loads context, plans with TodoWrite, implements code, validates, and verifies completion. Use when the user says 'ejecutar PRP', 'execute PRP', 'implement PRP', or provides a PRP file to implement."
allowed-tools: Read, Write, Edit, Bash, Glob, Grep
argument-hint: "[prp-file-path]"
model: opus
effort: high
---

# Ejecutar PRP BASE

Implementar una caracteristica usando el archivo PRP.

## Proceso de Ejecucion

### 1. Cargar PRP
- Leer el archivo PRP especificado
- Entender todo el contexto y requerimientos
- Seguir todas las instrucciones en el PRP y extender la investigacion si es necesario
- Asegurar que tienes todo el contexto necesario para implementar el PRP completamente
- Realizar mas busquedas web y exploracion del codebase segun sea necesario

### 2. ULTRATHINK
- Piensa profundamente antes de ejecutar el plan
- Crea un plan comprensivo que aborde todos los requerimientos
- Divide tareas complejas en pasos mas pequenos y manejables usando TodoWrite
- Identifica patrones de implementacion del codigo existente a seguir

### 3. Ejecutar el plan
- Ejecutar el PRP paso a paso
- Implementar todo el codigo

### 4. Validar
- Ejecutar cada comando de validacion
- Arreglar cualquier falla
- Re-ejecutar hasta que todos pasen

### 5. Completar
- Asegurar que todos los elementos del checklist esten hechos
- Ejecutar suite de validacion final
- Reportar estado de completacion
- Leer el PRP nuevamente para asegurar que has implementado todo

### 6. Referenciar el PRP
- Siempre puedes referenciar el PRP nuevamente si es necesario

**Nota:** Si la validacion falla, usa patrones de error en PRP para arreglar e intentar nuevamente.
