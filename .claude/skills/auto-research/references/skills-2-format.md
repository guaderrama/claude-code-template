# Checklist de Formato Skills 2.0

Referencia oficial para migrar y validar skills al formato actual de Claude Code.

## Estructura de carpeta requerida

```
.claude/skills/
└── nombre-del-skill/
    ├── SKILL.md          ← Requerido (max 500 líneas)
    ├── references/       ← Opcional: docs que Claude lee bajo demanda
    ├── scripts/          ← Opcional: código ejecutable
    └── assets/           ← Opcional: templates, iconos, fuentes
```

## Frontmatter YAML requerido

```yaml
---
name: nombre-del-skill
description: "Descripción específica y pushy de lo que hace y cuándo usarlo."
---
```

### Reglas del campo `name`
- Solo lowercase letters, numbers, hyphens
- Máximo 64 caracteres
- Sin espacios, underscores, ni caracteres especiales
- Considerar formato gerund (verb + -ing): `code-reviewing`, `deploying`

### Reglas del campo `description`
- Máximo 1024 caracteres
- Escrita en TERCERA PERSONA (no "I" ni "You")
- Debe incluir QUÉ hace Y CUÁNDO usarlo
- Ser "pushy": incluir triggers específicos
- NO incluir XML tags

**Ejemplo malo:**
```
description: "Analyzes SEO"
```

**Ejemplo bueno:**
```
description: "Performs comprehensive SEO audits across 9 categories including crawlability, indexability, security, URL structure, mobile, Core Web Vitals, structured data, JavaScript rendering, and IndexNow. Use whenever the user mentions 'SEO audit', 'technical SEO', 'crawl issues', 'robots.txt', 'Core Web Vitals', 'site speed', 'security headers', or asks to analyze a website's search performance, even if they don't explicitly say 'audit'."
```

## Frontmatter opcional

```yaml
allowed-tools: Read, Write, Edit, Bash, Glob, Grep
disable-model-invocation: true  # Solo invocación manual (/nombre)
user-invocable: false            # Solo Claude lo invoca (background knowledge)
argument-hint: "[url] [options]"
context: fork                    # Ejecutar en subagent aislado
model: claude-sonnet-4-6        # Override de modelo
```

### Cuándo usar `disable-model-invocation: true`
- Skills con side effects: deploy, delete, send-message
- Workflows que el usuario debe controlar el timing

### Cuándo usar `user-invocable: false`
- Background knowledge que no es un comando accionable
- Contexto de sistemas legacy, guías de estilo

## Reglas del body (contenido markdown)

1. **Máximo 500 líneas** en SKILL.md
2. Si excede → mover contenido pesado a `references/`
3. Referenciar archivos extra en el SKILL.md para que Claude sepa que existen
4. Scripts en `scripts/` — Claude los ejecuta sin cargarlos a contexto
5. Templates/assets en `assets/`

## Migración desde formato legacy

### Desde `.claude/commands/nombre.md`:
1. Crear `.claude/skills/nombre/SKILL.md`
2. Agregar frontmatter YAML (name + description)
3. Copiar contenido del .md al body del SKILL.md
4. Si tenía `allowed-tools` en frontmatter, mantenerlo
5. Verificar que funciona con `/nombre`

### Desde skills sin frontmatter:
1. Agregar bloque `---` con name y description
2. Validar que name es lowercase + hyphens
3. Escribir description pushy en tercera persona

## Validación

Después de migrar, verificar:
- [ ] `name` es lowercase, hyphens, max 64 chars
- [ ] `description` presente, max 1024 chars, tercera persona, pushy
- [ ] SKILL.md tiene menos de 500 líneas
- [ ] Archivos de referencia están en `references/` (no inline)
- [ ] `/nombre` funciona desde Claude Code
- [ ] Claude activa el skill automáticamente cuando es relevante (si no tiene disable-model-invocation)
