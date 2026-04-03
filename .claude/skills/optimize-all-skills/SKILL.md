---
name: optimize-all-skills
description: "Ejecuta auto-research en TODOS los skills del proyecto en secuencia. Primero migra cada skill al formato Skills 2.0 (frontmatter correcto, structure de carpeta, description pushy) y luego lo optimiza con evaluación binaria iterativa. Usar cuando el usuario diga 'optimizar todos', 'mejorar todos los skills', 'auto research masivo', o 'actualizar skills'."
disable-model-invocation: true
allowed-tools: Read, Write, Edit, Bash, Glob, Grep
model: opus
effort: high
---

# Optimización Masiva de Skills — Auto Research + Migración 2.0

## Paso 1: Descubrir todos los skills

```bash
find . -name "SKILL.md" -not -path "*/node_modules/*" -not -path "*/.git/*" -not -path "*/auto-research/*" | sort
```

También busca commands legacy que deban migrarse:
```bash
find . -path "*/.claude/commands/*.md" -not -path "*/node_modules/*" | sort
```

## Paso 2: Para CADA skill encontrado

### 2a. Migrar a formato Skills 2.0

Lee `references/skills-2-format.md` para el checklist completo. En resumen:

**Si está en `.claude/commands/nombre.md` (formato legacy):**
1. Crear carpeta `.claude/skills/nombre/`
2. Mover contenido a `.claude/skills/nombre/SKILL.md`
3. Agregar frontmatter YAML con `name` y `description`
4. Eliminar el archivo viejo de `commands/` (o dejarlo como referencia)

**Si ya está en `.claude/skills/nombre/SKILL.md`:**
1. Verificar que `name` sea lowercase + hyphens, max 64 chars
2. Verificar que `description` sea específica, en tercera persona, max 1024 chars
3. Hacer description "pushy": incluir triggers y cuándo Claude debe usarlo
4. Si SKILL.md excede 500 líneas: mover contenido pesado a `references/`
5. Agregar `allowed-tools` si el skill usa herramientas específicas
6. Agregar `disable-model-invocation: true` si es peligroso (deploy, delete, etc.)

### 2b. Cargar o generar evals

Busca en `references/evals.md`. Si no hay evals para este skill, genera 8-12 criterios binarios.

### 2c. Evaluar (3 outputs, criterios SÍ/NO)

Genera 3 outputs con escenarios realistas → evalúa → calcula puntaje.

### 2d. Iterar (máx 3 rondas en modo masivo)

Si puntaje < 90%: identificar fallos, modificar SKILL.md, repetir.
Si puntaje >= 90%: marcar ✅, pasar al siguiente.
Si después de 3 rondas < 90%: marcar ⚠️ con notas, pasar al siguiente.

### 2e. Registrar resultado

Agregar al changelog individual y al reporte consolidado.

## Paso 3: Orden de prioridad

### Prioridad 1 — Alto impacto
1. suno-worship-creator
2. trudiagnostic-interpreter
3. gut-zoomer-interpreter

### Prioridad 2 — SEO
4-15. seo, seo-audit, seo-technical, seo-content, seo-page, seo-schema, seo-geo, seo-images, seo-sitemap, seo-hreflang, seo-competitor-pages, seo-programmatic, seo-plan

### Prioridad 3 — Proceso
16-18. brainstorming, prd, firebase-docs-readme-generator

### Otros (detectados automáticamente)
Cualquier skill no listado arriba → procesar al final.

## Paso 4: Reporte consolidado

Guardar en `.claude/skills/auto-research/REPORTE-CONSOLIDADO.md`:

```markdown
# Reporte de Optimización Masiva
**Fecha:** [fecha]
**Skills procesados:** X de Y

## Migración a Skills 2.0
| Skill | Estado anterior | Migrado | Notas |
|-------|----------------|---------|-------|
| nombre | commands/ | ✅ skills/ | description mejorada |

## Optimización Auto Research
| # | Skill | Inicial | Final | Rondas | Estado |
|---|-------|---------|-------|--------|--------|
| 1 | nombre | X% | Y% | N | ✅/⚠️ |

## Mejora promedio: X%

## Skills que necesitan atención manual:
- [skill]: [razón]

## Próximos pasos:
1. [acción]
```

## Reglas modo masivo

- **Máximo 3 rondas por skill** (no 5)
- **Migrar formato ANTES de optimizar**
- **Backup antes de cada cambio**
- **Revertir si empeora**
- **Si el contexto se llena**: reportar progreso parcial, indicar dónde continuar. Al reabrir, leer REPORTE-CONSOLIDADO.md para retomar.
