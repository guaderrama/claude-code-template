---
name: migrar-ui-ux-2026
description: "Safely migrates an existing project to UI/UX Pro 2026 setup with intelligent merge: installs ui-ux-pro-max-skill, configures shadcn/Playwright MCPs, adds Frontend Aesthetics guidelines, and creates backup branch. Use when the user says 'migrar UI/UX', 'upgrade design system', 'install ui-ux-pro-max', or wants to modernize their project's frontend tooling."
disable-model-invocation: true
model: sonnet
effort: medium
---

# Migración Automática UI/UX Pro 2026

Este comando migra un proyecto existente aplicando las optimizaciones de UI/UX Pro 2026:
- ✅ MCP optimization (reducción de token overhead)
- ✅ ui-ux-pro-max-skill installation (25,300+ stars)
- ✅ Frontend Aesthetics guidelines
- ✅ UI design starter prompts
- ✅ shadcn MCP configuration
- ✅ Visual testing setup (Playwright)

## 🔒 Garantías de Seguridad

- **MERGE inteligente**: Preserva TODO tu contenido existente
- **NO overwrite**: Solo agrega lo que falta
- **Backup automático**: Crea branch de respaldo antes de empezar
- **Preview mode**: Muestra cambios antes de aplicar
- **Rollback fácil**: Un comando para revertir todo

## 🎯 Uso

```bash
# En el proyecto que quieres migrar
/migrar-ui-ux-2026

# O especifica ruta
/migrar-ui-ux-2026 /ruta/al/proyecto
```

## 📋 Pre-requisitos

Antes de ejecutar, asegúrate de:
- [ ] El proyecto tiene Git inicializado
- [ ] No hay cambios sin commitear (o haz commit primero)
- [ ] Tienes Node.js instalado (para shadcn)
- [ ] Estás en la raíz del proyecto

## 🔄 Proceso Automático

### Fase 1: Análisis y Backup (30 segundos)

Claude analiza tu proyecto y detecta:
- ✓ Archivos existentes (CLAUDE.md, .claude/, mcp.json, etc.)
- ✓ Configuraciones personalizadas
- ✓ Skills y commands actuales
- ✓ Estado de Git

Luego crea:
- ✓ Backup branch: `backup-pre-ui-ux-migration`
- ✓ Snapshot del estado actual

### Fase 2: Instalación de Componentes Nuevos (1 minuto)

**Se agregan SOLO los archivos que faltan:**

```diff
+ .claude/skills/ui-ux-pro-max-skill/     (clonar de GitHub)
+ .claude/prompts/ui-design-starter.md    (templates de prompts)
+ .claude/docs/MIGRATION_UI_UX_2026.md    (documentación de migración)
+ .claude/scripts/validate-migration.sh   (script de validación)
```

### Fase 3: Actualización de Archivos Existentes (1 minuto)

**MERGE inteligente (preserva tu contenido):**

#### CLAUDE.md
- ✅ Preserva: Todo tu contenido actual
- ⭐ Agrega: Sección "Frontend Aesthetics & UI/UX Guidelines"
- 📍 Posición: Después de "Tech Stack & Architecture"

#### INDEX.md (si existe)
- ✅ Preserva: Todas tus secciones personalizadas
- ⭐ Agrega: Sección "UI/UX & Design" en Skills
- ⭐ Agrega: Referencias en "Common Tasks" y "Working with Claude"

#### mcp.json
- ✅ Preserva: Todos tus MCPs existentes
- ⭐ Agrega: shadcn, playwright, chrome-devtools (si no existen)
- 💡 Sugiere: Optimizaciones (qué MCPs puedes desactivar)

### Fase 4: Validación Automática (30 segundos)

Claude ejecuta:
```bash
bash .claude/scripts/validate-migration.sh
```

Verifica:
- ✓ Todos los archivos nuevos creados
- ✓ MCPs configurados correctamente
- ✓ Skills accesibles
- ✓ CLAUDE.md actualizado
- ✓ No hay archivos rotos

### Fase 5: Resumen y Commit (30 segundos)

Claude genera:
- 📊 Resumen detallado de cambios
- 📝 Commit automático (opcional)
- 🔙 Instrucciones de rollback

## �� Ejemplo de Output

```bash
🔍 Analizando proyecto "mi-ecommerce-app"...

📂 Estructura detectada:
✓ CLAUDE.md encontrado (14 KB)
✓ .claude/ encontrado (12 archivos)
  ✓ commands/ (3 archivos personalizados)
  ✓ memory/ (5 archivos)
  ✓ skills/ (2 skills personalizados)
✗ ui-ux-pro-max-skill NO encontrado
✗ prompts/ NO encontrado
✓ mcp.json encontrado (5 MCPs activos)

📝 Cambios propuestos:

AGREGAR (4 nuevos):
+ .claude/skills/ui-ux-pro-max-skill/
+ .claude/prompts/ui-design-starter.md
+ .claude/docs/MIGRATION_UI_UX_2026.md
+ .claude/scripts/validate-migration.sh

ACTUALIZAR (3 merge):
↻ CLAUDE.md (+ Frontend Aesthetics section)
↻ INDEX.md (+ UI/UX references)
↻ mcp.json (+ shadcn, playwright, chrome-devtools)

PRESERVAR (12 sin tocar):
✓ .claude/commands/custom-command.md
✓ .claude/commands/otro-comando.md
✓ .claude/skills/my-skill/
✓ .claude/memory/* (todos)
✓ Tu contenido en CLAUDE.md
✓ Tus secciones en INDEX.md

¿Proceder? (y/n/preview) _
```

**Opciones:**
- `y` - Aplicar cambios inmediatamente
- `n` - Cancelar migración
- `preview` - Ver diffs detallados antes de aplicar

## 🔍 Preview Mode

Si eliges `preview`, Claude muestra:

```diff
=== CLAUDE.md ===
  ## 🏗️ Tech Stack & Architecture
  ...contenido existente...

+ ## 🎨 Frontend Aesthetics & UI/UX Guidelines
+
+ ### Design Philosophy: Evita "AI Slop Aesthetic"
+
+ **PROHIBIDO (Generic AI Look):**
+ - �� Inter/Roboto fonts everywhere
+ ...
```

```diff
=== mcp.json ===
  {
    "mcpServers": {
+     "shadcn": {
+       "disabled": false,
+       "command": "npx",
+       "args": ["-y", "shadcn@latest", "mcp"]
+     },
      "github": { "disabled": false },
      "stripe": { "disabled": false }
    }
  }
```

## 🎉 Migración Completada

```bash
✅ Backup creado: branch "backup-pre-ui-ux-migration"
✅ ui-ux-pro-max-skill clonado (25,300+ stars)
✅ Frontend Aesthetics agregado a CLAUDE.md
✅ ui-design-starter.md creado (15+ templates)
✅ mcp.json actualizado (3 MCPs agregados)
✅ INDEX.md actualizado (referencias UI/UX)
✅ Validación completada

���� Resumen:
━━━��━━━━━━━━━━━━━━━━━━━━━━���━━━━━━━━━━━
Archivos agregados:        4
Archivos actualizados:     3 (merge)
Archivos preservados:      12 (sin tocar)
━━���━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Token overhead:            -50% (16K → 8K)
UI templates disponibles:  +10 dashboards
Color palettes:            +96 profesionales
Component styles:          +67 variants
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Tiempo total:              2 min 47 seg
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

🚀 ¡Listo para usar!

Próximos pasos:
1. npm run dev
2. Probar: "Usa ui-ux-pro-max-skill para generar dashboard"
3. Instalar components: npx shadcn@latest add button

📖 Documentación completa:
   .claude/docs/MIGRATION_UI_UX_2026.md

🔙 Para revertir:
   git checkout backup-pre-ui-ux-migration
```

## 💾 Crear Commit (Opcional)

```bash
¿Crear commit con estos cambios? (y/n) _
```

Si eliges `y`, Claude crea:

```bash
git add .
git commit -m "feat: Migrate to UI/UX Pro 2026 setup

- Optimize MCPs (8→4 active, -50% token overhead)
- Install ui-ux-pro-max-skill (25,300+ stars)
- Add Frontend Aesthetics guidelines to CLAUDE.md
- Create UI design starter prompts
- Configure shadcn MCP for component installation
- Update INDEX.md with UI/UX references

Migration preserves all existing customizations:
- Custom commands (3)
- Custom skills (2)
- All memory files
- Personal sections in CLAUDE.md

🤖 Generated with Claude Code
Co-Authored-By: Claude Sonnet 4.5 <noreply@anthropic.com>"
```

## 🔙 Rollback (Si algo sale mal)

```bash
# Método 1: Volver al backup branch
git checkout backup-pre-ui-ux-migration

# Método 2: Revertir commit (si hiciste commit)
git revert HEAD

# Método 3: Reset hard (PELIGROSO - solo si no has pusheado)
git reset --hard HEAD~1
```

## 🆘 Troubleshooting

### Error: "Git no inicializado"
```bash
# Solución
git init
git add .
git commit -m "Initial commit"
# Luego ejecuta /migrar-ui-ux-2026 nuevamente
```

### Error: "Cambios sin commitear"
```bash
# Solución
git add .
git commit -m "Pre-migration checkpoint"
# Luego ejecuta /migrar-ui-ux-2026 nuevamente
```

### Error: "No se puede clonar skill"
```bash
# Solución manual
cd .claude/skills
git clone https://github.com/nextlevelbuilder/ui-ux-pro-max-skill.git
# Luego ejecuta /migrar-ui-ux-2026 nuevamente (detectará que ya existe)
```

### Error: "shadcn MCP no funciona"
```bash
# Verificar Node.js
node --version  # Debe ser v18+

# Reinstalar
npm install -g npm@latest
```

## 📚 Documentación Adicional

- **Guía completa**: [.claude/docs/MIGRATION_UI_UX_2026.md](./../docs/MIGRATION_UI_UX_2026.md)
- **Validation script**: [.claude/scripts/validate-migration.sh](./../scripts/validate-migration.sh)
- **UI design prompts**: [.claude/prompts/ui-design-starter.md](./../prompts/ui-design-starter.md)
- **UI/UX skill**: [.claude/skills/ui-ux-pro-max-skill/README.md](./../skills/ui-ux-pro-max-skill/README.md)

## 🎯 Después de la Migración

### Probar el skill inmediatamente:

```bash
"Usa ui-ux-pro-max-skill para generar un dashboard de analytics.
Color palette: profesional (no purple), neutros + accent blue.
Charts: line chart (revenue), bar chart (users), pie chart (sources).
Layout: sidebar navigation, responsive, dark mode.
Evita: AI slop aesthetic (Inter font, glass morphism)."
```

### Instalar componentes shadcn:

```bash
"Instala button component usando shadcn MCP.
Customiza con brand colors, spacing system (4px base).
Variants: primary, secondary, ghost, outline.
States: hover, focus, disabled, loading."
```

### Testing visual:

```bash
"Usa Playwright MCP para capturar screenshots del dashboard.
Viewports: mobile (375px), tablet (768px), desktop (1440px).
Compara vs design requirements y lista discrepancias."
```

---

**Nota:** Este comando está diseñado para ser **100% seguro**. Siempre hace backup, nunca borra contenido existente, y permite rollback fácil.

**Tiempo estimado:** 3-4 minutos por proyecto

**Prerequisitos:** Git, Node.js v18+, npm

**Soporte:** Ver [MIGRATION_UI_UX_2026.md](./../docs/MIGRATION_UI_UX_2026.md) para guía completa
