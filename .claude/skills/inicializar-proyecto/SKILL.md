---
name: inicializar-proyecto
description: "Automatically restructure any existing project to use the optimized Claude Code template. Reads the existing CLAUDE.md, analyzes the project structure, rewrites CLAUDE.md to <150 lines, moves overflow content to .claude/rules/ and .claude/docs/, and configures settings. Use when entering a new or messy project, or when the user says 'inicializar proyecto', 'setup project', 'aplicar template', 'organizar proyecto', or 'reestructurar'."
allowed-tools: Read, Write, Edit, Bash, Glob, Grep
model: opus
effort: high
---

# Inicializar Proyecto

Reestructura automaticamente cualquier proyecto para usar el template optimizado de Claude Code.

## Prerequisito

La carpeta `.claude/` con skills, docs y rules ya debe existir en el proyecto. Si no existe, informar al usuario que copie la carpeta `.claude/` del template primero.

## Paso 1: Analizar el proyecto actual

Lee y analiza TODO antes de cambiar nada:

```
1. Leer CLAUDE.md actual (si existe) — guardar su contenido completo en memoria
2. Leer .claude/ existente — que ya tiene configurado
3. Explorar la raiz del proyecto:
   - package.json (si existe) → tech stack JS
   - requirements.txt / pyproject.toml → tech stack Python
   - Cargo.toml → Rust
   - go.mod → Go
   - Gemfile → Ruby
   - composer.json → PHP
   - Archivos sueltos → detectar lenguajes
4. Listar carpetas principales → entender estructura
5. Leer README.md (si existe) → entender proposito del proyecto
```

Resultado: tener claro el tech stack, proposito, estructura y reglas existentes.

## Paso 2: Generar nuevo CLAUDE.md (<150 lineas)

Reescribir el CLAUDE.md siguiendo EXACTAMENTE esta estructura:

```markdown
# Proyecto: [nombre detectado]

[1-2 lineas describiendo el proyecto, extraidas del CLAUDE.md viejo o README]

## Tech Stack
- **[Categoria]**: [tecnologia detectada]
- (listar solo lo real, no inventar)

## Architecture
[Describir la estructura de carpetas REAL del proyecto, no la ideal]

## IMPORTANT: Commands
- [comandos reales del proyecto, extraidos de package.json scripts o equivalente]

## IMPORTANT: Code Conventions
- [convenciones del CLAUDE.md viejo que sigan siendo relevantes]
- [Si no habia, agregar las basicas: naming, max lines, etc.]

## IMPORTANT: Prohibitions
- [prohibiciones del CLAUDE.md viejo]
- [Si no habia, agregar las universales: no secrets, no any, etc.]

## Workflow: PLAN > DIFFS > VERIFY

## References
- See @.claude/INDEX.md for complete project map
- See @.claude/docs/ for detailed guides
- See @.claude/memory/TODO.md for current priorities
```

REGLAS CRITICAS:
- Maximo 150 lineas
- Solo informacion que aplique a TODOS los archivos SIEMPRE
- NO copiar bloques de codigo, tutoriales o ejemplos extensos
- NO inventar tech stack que no existe en el proyecto
- PRESERVAR toda regla o prohibicion del CLAUDE.md viejo que tenga sentido

## Paso 3: Mover contenido sobrante a rules/

Todo el contenido detallado del CLAUDE.md viejo que no cupo en las 150 lineas va a `.claude/rules/`:

Para cada bloque de contenido sobrante:
1. Identificar a que archivos aplica (paths)
2. Crear un archivo en `.claude/rules/[tema].md` con frontmatter:

```markdown
---
paths:
  - "patron/que/aplica/**"
---

# [Titulo]

[Contenido movido del CLAUDE.md viejo]
```

Ejemplos de separacion:
- Reglas de frontend → `rules/frontend.md` (paths: src/**/*.tsx, *.css)
- Reglas de API → `rules/api.md` (paths: src/app/api/**, routes/**)
- Reglas de DB → `rules/database.md` (paths: supabase/**, prisma/**)
- Reglas de testing → `rules/testing.md` (paths: **/*.test.*, tests/**)
- Guias extensas → `docs/[tema].md` (sin paths, referencia manual)

Si ya existen rules/ del template que NO aplican al proyecto (ej: frontend.md en un proyecto solo Python), BORRARLAS.

## Paso 4: Configurar settings

Leer `.claude/settings.local.json`. Verificar que tenga:
- `autoMemoryEnabled: true`
- `autoDreamEnabled: true`
- Permissions relevantes al proyecto

Si le faltan, agregarlos.

## Paso 5: Verificacion

Mostrar al usuario:
1. Resumen de lo que se detecto del proyecto
2. Nuevo CLAUDE.md (completo)
3. Rules creados/modificados
4. Rules eliminados por no aplicar
5. Cualquier contenido del CLAUDE.md viejo que se descarto y por que

Preguntar: "Quieres que aplique estos cambios?"

Solo aplicar despues de confirmacion.

## Notas

- NUNCA borrar el CLAUDE.md viejo sin antes haber leido y procesado todo su contenido
- NUNCA inventar tecnologias o comandos que no se detectaron en el proyecto
- Si el proyecto no tiene CLAUDE.md, crear uno desde cero basado en el analisis
- Si no hay package.json ni equivalente, inferir stack de las extensiones de archivo
- Respetar la personalidad del proyecto — no forzar Next.js/React si es un proyecto Python
