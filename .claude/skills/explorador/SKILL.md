---
name: explorador
description: "Systematically explore and understand a software project's structure, documentation, key files, and architecture. Use when the user says 'explorar proyecto', 'explore project', 'understand codebase', 'show me the project', or when starting work on an unfamiliar project."
allowed-tools: Read, Bash, Glob, Grep
model: haiku
effort: low
---

# Explorador de Proyecto

Explora y entiende el contexto de un proyecto de software.

## Pasos de Exploracion

### 1. Estructura del Proyecto
- Usa `tree` o `ls` para ver la estructura del proyecto
- Identifica directorios principales y su proposito
- Nota patrones de organizacion de archivos

### 2. Documentacion Principal
- Lee CLAUDE.md (si existe) para entender principios de desarrollo
- Lee README.md para entender el proposito y configuracion del proyecto
- Busca otros archivos de documentacion relevantes

### 3. Archivos Clave
- Examina archivos importantes en src/ o directorio raiz
- Identifica puntos de entrada de la aplicacion
- Revisa configuraciones principales (package.json, requirements.txt, etc.)

### 4. Resumen del Proyecto
Al finalizar la exploracion, generar un resumen estructurado que incluya:
- **Proposito**: Que hace el proyecto en 1-2 oraciones
- **Stack**: Tecnologias principales detectadas
- **Estructura**: Patron de organizacion (monorepo, feature-first, etc.)
- **Puntos de entrada**: Archivos principales para empezar
- **Dependencias clave**: Librerias/frameworks principales
- **Estado**: Maturo, en desarrollo, prototipo, etc.

Este resumen debe ser util tanto para desarrolladores humanos como para IAs que necesiten contexto rapido del proyecto.

## Objetivo

Proporcionar una comprension sistematica del layout del proyecto, proposito y componentes clave a traves de un enfoque estructurado de exploracion, enfocandose en documentacion y archivos fuente.
