# Context Management Guide

> Como optimizar el uso de contexto en Claude Code para reducir costos y mejorar calidad.

---

## Por Que Importa

- El contexto de entrada es 85-92% del costo total de tokens
- CLAUDE.md se inyecta en CADA mensaje — cada linea cuesta
- Rules en `.claude/rules/` solo cargan cuando se tocan archivos relevantes
- Skills cargan on-demand, no permanentemente

---

## Jerarquia de Contexto

| Capa | Cuando carga | Costo |
|------|-------------|-------|
| `CLAUDE.md` (raiz) | SIEMPRE, cada mensaje | Alto — mantener <150 lineas |
| `.claude/rules/*.md` | Solo con archivos matching `paths:` | Bajo — solo cuando relevante |
| `.claude/skills/*/SKILL.md` | On-demand via slash commands | Cero hasta invocacion |
| Auto-memory | Automatico, contextual | Minimo — sistema nativo |

---

## Reglas de Oro

### CLAUDE.md (<150 lineas)
- Solo lo que aplica a TODOS los archivos, SIEMPRE
- Tech stack, convenciones universales, prohibiciones
- Punteros a docs detallados (no contenido completo)
- NO ejemplos de codigo, NO tutoriales, NO guias extensas

### .claude/rules/ (path-scoped)
- Reglas especializadas que solo aplican a ciertos archivos
- Frontmatter `paths:` determina cuando carga
- Ejemplo: reglas de frontend solo cargan al tocar `.tsx`

### Skills (on-demand)
- Conocimiento especializado que carga solo cuando se invoca
- `model:` field permite routing a modelos mas baratos/caros
- `effort:` field controla profundidad de razonamiento

---

## Comandos de Gestion de Contexto

| Comando | Efecto |
|---------|--------|
| `/compact [tema]` | Comprime conversacion manteniendo tema clave |
| `/clear` | Reinicia contexto desde cero |
| `/cost` | Muestra consumo de tokens actual |
| `/effort low` | Reduce razonamiento (menos tokens) |
| `/model haiku` | Cambia a modelo mas barato para tareas simples |

---

## Patrones Anti-Contexto (evitar)

1. **CLAUDE.md gigante**: >200 lineas = Claude ignora reglas al final
2. **Duplicar contenido**: Mismo texto en CLAUDE.md Y rules Y skills
3. **Skills sin model routing**: Usar opus para tareas que haiku resuelve
4. **No usar /compact**: Conversaciones largas degradan calidad
5. **Leer archivos enteros**: Usar `offset` + `limit` para leer solo lo necesario

---

## Optimizacion de Costos

### Modelo por tarea
- **haiku**: explorar codebase, brainstorming, validacion rapida
- **sonnet**: desarrollo standard, testing, refactoring
- **opus**: debugging complejo, arquitectura, security review, PRPs

### Esfuerzo por tarea
- **low**: tareas mecanicas con instrucciones claras
- **medium**: desarrollo con algo de decision
- **high**: problemas abiertos que requieren razonamiento profundo

### Auto-memory vs manual
- Auto-memory (nativo): Claude recuerda entre sesiones automaticamente
- Auto-dream (nativo): Claude consolida aprendizajes al cerrar sesion
- NO necesitas actualizar NOTES.md manualmente — el sistema lo hace
