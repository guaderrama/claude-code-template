---
name: generar-prp
description: "Generate a comprehensive Product Requirements Proposal (PRP) document for software feature implementation. Includes codebase analysis, external research, implementation blueprint, validation gates, and confidence scoring. Use when the user says 'generar PRP', 'create PRP', 'product requirements', 'feature specification', or needs a detailed implementation plan for a new feature."
allowed-tools: Read, Write, Edit, Bash, Glob, Grep
argument-hint: "[feature-description]"
model: opus
effort: high
---

# Generador de PRP (Propuesta de Requerimientos de Producto)

## Objetivo
Crear un documento PRP comprensivo que permita "exito de implementacion en un solo pase a traves de contexto comprensivo" para desarrollar una nueva caracteristica de software.

## Proceso de Investigacion

### 1. Analisis del Codebase
- **Arquitectura**: Entender la estructura general y patrones de diseno
- **Convenciones**: Identificar estandares de codificacion y nomenclatura
- **Dependencias**: Mapear bibliotecas y frameworks utilizados
- **Patrones existentes**: Encontrar implementaciones similares como referencia
- **Puntos de integracion**: Identificar donde la nueva caracteristica encajara

### 2. Investigacion Externa
- **Documentacion oficial** de frameworks/bibliotecas utilizadas
- **Mejores practicas** para el tipo de caracteristica a implementar
- **Patrones de diseno** aplicables
- **Consideraciones de seguridad** relevantes

### 3. Clarificacion del Usuario (si es necesario)
- Recopilar requerimientos especificos
- Aclarar casos de uso esperados
- Definir criterios de aceptacion
- Establecer restricciones o limitaciones

## Estructura del PRP

### Secciones Requeridas
1. **Resumen Ejecutivo** - Descripcion concisa y valor
2. **Contexto e Investigacion** - Todo el contexto recopilado
3. **Blueprint de Implementacion** - Plan detallado paso a paso
   - Pseudocodigo de alto nivel
   - Archivos a modificar/crear
   - Estrategias de manejo de errores
   - Secuencia de tareas
4. **Puertas de Validacion** - Verificaciones y criterios de exito
5. **Riesgos y Mitigaciones** - Problemas potenciales
6. **Puntuacion de Confianza** - X/10 con justificacion

### Archivo de Salida
- Guardar como `prp-[nombre-caracteristica]-[fecha].md`
- Incluir metadata: Fecha, autor, version

### Lista de Verificacion de Calidad
- [ ] Contexto comprensivo incluido
- [ ] Blueprint de implementacion claro
- [ ] Puertas de validacion definidas
- [ ] Referencias externas documentadas
- [ ] Riesgos identificados y mitigados
- [ ] Criterios de exito claros

**Objetivo final**: Crear un documento que permita a cualquier desarrollador (o IA) implementar la caracteristica exitosamente sin investigacion adicional significativa.

## Referencias

Ver `references/plantilla-prp.md` para la plantilla completa del PRP, blueprint de implementacion, puertas de validacion, y sistema de puntuacion de confianza.
