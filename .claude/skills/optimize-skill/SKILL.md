---
name: optimize-skill
description: "Optimiza un skill específico usando el método Auto Research de Karpathy. Ejecuta ciclos de generación, evaluación binaria e iteración del SKILL.md hasta alcanzar 90%+ de consistencia. Usar siempre que el usuario diga 'optimizar skill', 'mejorar skill', 'auto research', 'evaluar skill', o nombre un skill y pida mejorarlo."
argument-hint: "[nombre-del-skill]"
disable-model-invocation: true
allowed-tools: Read, Write, Edit, Bash, Glob, Grep
model: opus
effort: high
---

# Optimizar Skill: $ARGUMENTS

Usa el método Auto Research para mejorar iterativamente el skill indicado.

## Paso 1: Localizar el skill

Busca `$ARGUMENTS` en estas rutas (en orden):
1. `.claude/skills/$ARGUMENTS/SKILL.md`
2. `skills/$ARGUMENTS/SKILL.md`
3. Si no lo encuentras: `find . -path "*$ARGUMENTS*SKILL.md" -not -path "*/node_modules/*" -type f`

Lee el SKILL.md completo y cualquier archivo en `references/`.

## Paso 2: Verificar formato Skills 2.0

Antes de optimizar, verifica que el skill cumple con el formato actual. Si NO cumple, migrarlo primero:

**Checklist de formato (ver `references/skills-2-format.md`):**
- [ ] Ubicado en `.claude/skills/[nombre]/SKILL.md` (no en `commands/`)
- [ ] Frontmatter tiene `name` (max 64 chars, lowercase + hyphens)
- [ ] Frontmatter tiene `description` (max 1024 chars, específica y "pushy")
- [ ] Description escrita en tercera persona
- [ ] Description incluye triggers: cuándo Claude debe activar el skill
- [ ] SKILL.md tiene menos de 500 líneas
- [ ] Si excede 500 líneas: contenido pesado movido a `references/`
- [ ] Estructura de carpeta correcta: `SKILL.md` + opcional `scripts/`, `references/`, `assets/`

Si falla alguno → migrar al formato correcto antes de continuar.

## Paso 3: Cargar evals

Lee `.claude/skills/auto-research/references/evals.md` para los criterios de `$ARGUMENTS`.

Si no tiene evals definidos:
1. Analiza qué promete hacer el SKILL.md
2. Genera 8-12 criterios binarios (SÍ/NO)
3. Agrégalos a `references/evals.md`

## Paso 4: Ronda de evaluación

1. Genera **3 outputs completos** con escenarios realistas:
   - Caso típico
   - Caso edge/atípico
   - Caso complejo
2. Evalúa cada output contra TODOS los criterios (solo SÍ/NO)
3. Puntaje = (pasados) / (total × 3) × 100%
4. Registra en `.claude/skills/auto-research/logs/$ARGUMENTS-changelog.md`

## Paso 5: Analizar e iterar

1. Criterios que fallan en 2+ outputs = **PRIORIDAD ALTA**
2. Para cada fallo, identifica la causa en el SKILL.md:
   - ¿Falta instrucción? ¿Es ambigua? ¿Contradictorias?
3. Propone cambios específicos
4. **REGLA:** No sacrificar criterios que ya pasan

## Paso 6: Aplicar cambios

```bash
cp [ruta/SKILL.md] [ruta/SKILL.md.backup-ronda-N]
```
Aplica cambios → Vuelve al Paso 4

## Paso 7: Resultado

- Mejoró → siguiente ciclo
- Empeoró → revertir al backup, probar otro approach
- 90%+ → ÉXITO → reporte final

## Paso 8: Reporte

```markdown
## Resultado: [nombre-del-skill]
- Formato Skills 2.0: ✅ Migrado / ✅ Ya estaba
- Puntaje inicial: X%
- Puntaje final: Y%
- Rondas: N
- Cambios clave:
  1. [cambio]
  2. [cambio]
- Criterios pendientes:
  - [criterio]: [razón]
```

Guardar en `.claude/skills/auto-research/logs/$ARGUMENTS-changelog.md`

## Reglas

- **Solo binario**: SÍ o NO
- **Siempre backup** antes de editar
- **Revertir si empeora**
- **No hacer trampa**: output que repite los criterios = fallo
- **Log todo**: cada ronda registrada
- **Máximo 5 rondas** por skill
