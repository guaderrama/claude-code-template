---
name: auto-research
description: "Sistema de auto-mejora iterativa para skills de Claude Code basado en Auto Research de Andrej Karpathy. Evalúa skills con criterios binarios, itera el SKILL.md, y converge hacia 90%+ de consistencia. Incluye migración automática al formato Skills 2.0. Usar cuando se quiera optimizar, mejorar, evaluar, migrar, o actualizar cualquier skill existente, incluso si el usuario dice 'auto research', 'mejorar prompts', o 'skill optimization'."
user-invocable: false
model: opus
effort: high
---

# Auto Research — Sistema de Auto-Mejora de Skills

Sistema que mejora iterativamente cualquier skill usando evaluación binaria.

## Proceso
1. Generar outputs usando el skill actual
2. Evaluar cada output contra criterios binarios (SÍ/NO)
3. Identificar qué falló y por qué
4. Modificar el SKILL.md para arreglar los fallos
5. Repetir hasta 90%+

## Archivos de referencia
- `references/evals.md` — Criterios binarios por skill
- `references/skills-2-format.md` — Checklist de formato Skills 2.0
- `logs/` — Changelogs por skill

## Invocación
- `/optimize-skill [nombre]` — Un skill específico
- `/optimize-all-skills` — Todos en secuencia

## Principios
- Solo binario: SÍ o NO
- Siempre backup antes de modificar
- Revertir si empeora
- Log cada ronda
- Máximo 5 rondas (individual) o 3 (masivo)
