---
name: sincronizar-template
description: "Propaga de forma segura este template de Claude Code (hooks, skills, rules, docs, agents) a cualquier proyecto existente usando merge inteligente con consolidacion opcional de CLAUDE.md. Genera backup automatico via git stash + branch, preview obligatorio, escritura atomica, nunca sobrescribe contenido custom, detecta tech stack para omitir reglas irrelevantes. Fase 9 opcional consolida CLAUDE.md extenso en resumen ejecutivo ~100 lineas redistribuyendo contenido a .claude/docs/. Usar cuando el usuario diga 'sincronizar template', 'aplicar template a este proyecto', 'actualizar proyecto con lo nuevo', 'propagar skills', 'traer los hooks a este repo', 'sync template', 'consolidar CLAUDE.md', o 'limpiar CLAUDE.md'."
license: MIT
model: opus
effort: high
allowed-tools: Read, Write, Edit, Bash, Glob, Grep
---

# Sincronizar Template

Propaga de forma SEGURA este template (skills, hooks, rules, agents, docs) a cualquier proyecto existente sin romper nada.

## Invocacion y entorno

**Shell requerida:** Git Bash (Windows), bash (Linux/Mac), o zsh. NO cmd.exe ni PowerShell. Si el entorno no es compatible, abortar en Fase 0.

**Argumento obligatorio:** ruta al template fuente.

```
/sincronizar-template <ruta-al-template>

Ejemplo:
/sincronizar-template "c:/Users/ivang/Dropbox/Ai/claude code para trabajar y seguir actualizando con claude"
```

Reglas de parseo del argumento:
- Si `args` (string recibido por el skill) esta vacio o no contiene ruta: PREGUNTAR al usuario "¿Cual es la ruta absoluta del template fuente?" y esperar respuesta.
- El argumento completo se trata como ruta unica (aunque tenga espacios). Strippear comillas envolventes.
- Resolver a ruta absoluta con `readlink -f` o `realpath` antes de usar.
- Nunca asumir cwd como TEMPLATE_SOURCE.

Variables que manejaras durante la ejecucion:
- `TEMPLATE_SOURCE` — ruta absoluta del template (proporcionada por el usuario)
- `PROJECT_DEST` — `pwd` actual (proyecto destino)
- Si `TEMPLATE_SOURCE == PROJECT_DEST` (tras resolver ambos a absolutos) → abortar.

## Garantias de Seguridad (NO NEGOCIABLES)

1. **Backup via git stash + branch** antes de tocar nada. Captura tracked + untracked.
2. **Escritura atomica**: todo se escribe a `.claude.sync-tmp/` y se mueve al final; si falla en medio no hay archivos parciales.
3. **Preview obligatorio** antes del backup y antes de escribir; el usuario ve TODO.
4. **Re-check de `git status` justo antes de escribir** (entre preview y escritura el usuario pudo editar).
5. **Preservar `.claude/memory/` completo** — es del usuario, NUNCA se toca.
6. **Rollback automatico** si falla la validacion JSON de `settings.local.json`.
7. **Preservar por defecto**: en la duda, NO sobrescribir. Preguntar.
8. **Log de auditoria** en `.claude/.sync-log/` (no en `memory/`).
9. **NO pushear nunca**. NO `git reset --hard` nunca. NO `rm -rf` nunca.

## Fases (9 fases + consolidacion opcional)

### Fase 0 — Pre-flight checks (abortar si falla)

Verificar UNO POR UNO. Si cualquiera falla, abortar con mensaje claro.

```
[0.1] PROJECT_DEST es repo git
  → git rev-parse --git-dir (si falla: "Este directorio no es un repo git. Ejecuta 'git init' primero.")

[0.2] No es submodule ni repo solo-lectura
  → git rev-parse --show-superproject-working-tree (si devuelve path: advertir submodule)
  → touch .claude-sync-probe && rm .claude-sync-probe (permisos de escritura)

[0.3] Working tree limpio
  → git status --porcelain (si no vacio: "Hay cambios sin commitear. Commit o stash primero.")

[0.4] TEMPLATE_SOURCE existe y tiene estructura de template
  → existe directorio .claude/ con skills/ y al menos settings.local.json o CLAUDE.md
  → TEMPLATE_SOURCE != PROJECT_DEST (resolver absolutos antes de comparar)

[0.5] .gitignore del destino NO ignora .claude/
  → grep -E "^\.claude/?$|^\.claude/\*" .gitignore (si coincide: advertir y pedir confirmacion — archivos nuevos no entraran al backup)

[0.6] No hay sync reciente en progreso
  → si existe .claude.sync-tmp/ de corrida previa → advertir y ofrecer limpiar
  → si existe .claude/.sync-log/last.json con timestamp < 5 min → advertir posible doble ejecucion

[0.7] Tech stack detectable
  → detectar al menos uno: package.json / pyproject.toml / requirements.txt / Cargo.toml / go.mod / Gemfile / composer.json
  → si ninguno: usar inferencia por extensiones y marcar stack como "unknown" (afecta clasificacion SKIP_IRRELEVANT)

[0.8] Shell compatible
  → verificar $BASH_VERSION o $ZSH_VERSION no vacio
  → si vacio: "Este skill requiere Git Bash, bash o zsh. Abortando."

[0.9] Herramientas requeridas disponibles
  → command -v git (requerido)
  → command -v sha256sum OR command -v shasum (uno de los dos)
  → jq es OPCIONAL — si falta, usar grep/sed para leer JSON (documentado en Fase 8)
```

### Fase 1 — Analisis del destino (solo lectura)

Sin modificar nada, recopilar:

- Listar contenido de `.claude/` (si existe): skills, rules, docs, agents, memory, snippets, settings.
- Leer `.claude/settings.local.json` si existe (preservar texto original).
- Leer `CLAUDE.md` si existe.
- Leer `.claude/INDEX.md` si existe.
- Detectar tech stack(s) presentes (puede haber multiples):
  - `package.json` con `next` en deps → next-stack
  - `package.json` sin next → node-stack
  - `pyproject.toml` / `requirements.txt` → python-stack
  - `Cargo.toml` → rust-stack
  - `go.mod` → go-stack
  - `Gemfile` → ruby-stack
  - `composer.json` → php-stack
  - Si hay varios (ej. monorepo) → todos los stacks detectados
- Detectar si es monorepo (carpetas `packages/` o `apps/` con sub-`.claude/`) → si si, preguntar cual es el target.
- Git branch actual y ultimo commit.

Guardar TODO este diagnostico en memoria para las siguientes fases.

### Fase 2 — Clasificar cada archivo del template

Para cada archivo bajo `TEMPLATE_SOURCE/.claude/` y `TEMPLATE_SOURCE/CLAUDE.md`, asignar UNA categoria:

| Categoria | Criterio | Accion en Fase 5 |
|-----------|----------|-------------------|
| `ADD` | No existe en destino | Copiar |
| `MERGE` | Es settings.local.json, CLAUDE.md o INDEX.md y existe en destino | Merge por reglas Fase 6 |
| `SKIP_CUSTOM` | Existe en destino, difiere del template, y NO es mergeable | Preservar destino, reportar |
| `SKIP_IRRELEVANT` | No aplica al stack detectado | No copiar, reportar |
| `ASK` | Ambiguo (divergencia estructural grande, monorepo sin target claro) | Preguntar al usuario en Fase 4 |

**Como detectar "difiere del template" sin version previa:**
- Normalizar EOL (CRLF→LF) y trim trailing whitespace antes de comparar.
- Comparar hash SHA256 normalizado.
- Si el destino tiene `.claude/.template-manifest.json` de una corrida previa, usarlo como baseline (la primera corrida lo crea).
- Si NO hay manifest previo: cualquier diferencia de contenido cuenta como `SKIP_CUSTOM` salvo whitespace/EOL.
- Al final de Fase 5, ACTUALIZAR `.claude/.template-manifest.json` con los hashes de lo copiado.

**Reglas de stack (omitir si NO aplica):**

Skills universales (siempre ADD): `code-review`, `pre-mortem`, `refactor-smells`, `systematic-debugging`, `verification-before-completion`, `skill-creator`, `brainstorming`, `bucle-agentico`, `explorador`, `security-review`, `auditoria-vps`, `generar-prp`, `ejecutar-prp`, `arreglar-issue-github`, `preparar-paralelo`, `ejecutar-paralelo`, `auto-research`, `optimize-skill`, `sincronizar-template`.

Skills condicionados:
- `nextjs-16-complete-guide`, `frontend-design`, `webapp-testing`, `ui-ux-pro-max-skill` → solo si next-stack o node-stack con frontend
- `supabase-auth-memory`, `supabase-postgres-best-practices` → solo si el proyecto usa Supabase (grep `@supabase` en package.json o `supabase` en requirements)
- `agent-builder-vercel-sdk` → solo si node-stack

Rules (`.claude/rules/*.md`) condicionados:
- `frontend.md` → solo si hay `*.tsx` o `*.jsx` en el proyecto
- `database.md` → solo si hay carpeta `supabase/`, `prisma/`, o servicios en `src/**/services/`
- `testing.md` → solo si hay archivos `*.test.*` o carpeta `tests/`
- `security.md` → solo si hay `src/app/api/` o `middleware.*`

Si stack es `unknown`: clasificar los condicionados como `ASK`, no `SKIP_IRRELEVANT`.

### Fase 2.5 — Deteccion de divergencia estructural (INDEX.md y CLAUDE.md)

Estas reglas son DETERMINISTICAS. Dos modelos deben producir el mismo resultado.

**INDEX.md — clasificar como ADD, MERGE o ASK:**
```
si destino.INDEX.md no existe:
  → ADD

calcular:
  headers_destino = set de lineas del destino que empiezan con "## " (trim)
  headers_template = set de lineas del template que empiezan con "## " (trim)
  interseccion = |headers_destino ∩ headers_template|
  min_total = min(|headers_destino|, |headers_template|)

  si min_total == 0 → ASK
  ratio = interseccion / min_total

  si ratio >= 0.5  → MERGE (compatible, agregar entradas nuevas a listas)
  si ratio <  0.5  → ASK  (estructura demasiado distinta, preguntar al usuario)
```

**CLAUDE.md — detectar headers iguales sin adivinar sinonimos:**
```
NO intentar mapear EN↔ES. No hay tabla de sinonimos.
Comparar headers con esta normalizacion EXACTA:
  1. trim whitespace
  2. lowercase
  3. remover "## " del prefijo
  4. colapsar multiples espacios en uno

Si header del template normalizado coincide con alguno del destino → preservar el del destino, NO insertar.
Si no coincide → agregar al final del archivo destino.

"## Prohibitions" y "## Prohibiciones" NO coinciden — son headers distintos.
Si el usuario quiere unificarlos es decision manual, NO del skill.
```

### Fase 3 — Preview (PRIMERO, sin modificar nada)

El preview va ANTES del backup. Si el usuario cancela, no hay branch huerfano.

Mostrar reporte estructurado:

```
==========================================
SINCRONIZACION DE TEMPLATE — PREVIEW
==========================================

Template origen: <TEMPLATE_SOURCE>
Proyecto destino: <PROJECT_DEST>
Branch actual: <branch>
Stack detectado: <stacks>
Monorepo: <si/no>

AGREGAR (N archivos nuevos):
+ .claude/skills/code-review/SKILL.md
+ .claude/skills/pre-mortem/SKILL.md
+ .claude/rules/security.md
  ...

MERGE (N archivos):
↻ .claude/settings.local.json
    - Permissions: union de <X actuales> + <Y del template> = <Z finales>
    - Hooks: <accion concreta: agregar solo los nuevos / preguntar>
↻ .claude/INDEX.md
    - Agregar <N> referencias a skills nuevos
    - Preservar tus secciones custom
↻ CLAUDE.md
    - Agregar seccion "Prohibitions" (falta en tu archivo)
    - Preservar <N> secciones existentes sin tocar

PRESERVAR sin tocar (N archivos):
✓ .claude/memory/* (TODO — regla inviolable)
✓ .claude/skills/mi-skill-custom/ (custom, no en template)
✓ CLAUDE.md seccion "Convenciones personales" (custom detectado)

OMITIR por stack (N archivos):
⊘ .claude/skills/nextjs-16-complete-guide/ (proyecto python-stack)
⊘ .claude/rules/frontend.md (no hay archivos *.tsx)

REQUIEREN DECISION (N archivos):
? .claude/skills/security-review/SKILL.md — existe con contenido diferente
  Opciones: (diff) ver diferencias / (preservar) mantener tuyo / (sobrescribir) usar template / (omitir) no tocar

==========================================
Responde: aplicar / cancelar / diff <archivo> / decidir <archivo> <opcion>
==========================================
```

**Esperar confirmacion explicita.** Solo `aplicar` continua a Fase 4.

**Loop de interaccion en Fase 3:**
- `aplicar` → continua a Fase 4
- `cancelar` → termina, no modifica nada
- `diff <archivo>` → muestra diff del archivo indicado y RE-MUESTRA el preview. Repite.
- `decidir <archivo> <opcion>` → registra decision (sobrescribir/preservar/omitir/diff), re-muestra preview con la decision aplicada. Repite.

**Parseo de `<archivo>`:**
- Matching por subcadena case-insensitive contra el path completo relativo al template.
- Si hay multiples matches: listarlos todos y pedir al usuario que desambigue con path mas largo.
- Si no hay match: decir "No encontre archivo con '<input>'. Revisa el preview."

Ejemplos validos:
- `diff security-review` → matchea `.claude/skills/security-review/SKILL.md`
- `decidir security-review preservar` → marca ese archivo como preservar
- `decidir .claude/skills/security-review/SKILL.md sobrescribir` → mismo efecto con path completo

### Fase 4 — Backup (capturando tracked + untracked REAL)

Solo tras confirmacion "aplicar". El backup DEBE contener tambien archivos untracked dentro de `.claude/` y `CLAUDE.md`, porque el template va a crear nuevos archivos.

**Comando exacto:**

```bash
TS=$(date +%Y%m%d-%H%M%S)
BRANCH="backup-pre-sync-$TS"

# Evitar colision de branch (doble ejecucion o segundo identico)
while git rev-parse --verify "$BRANCH" >/dev/null 2>&1; do
  BRANCH="backup-pre-sync-$TS-$RANDOM"
done

# git stash create genera un commit con tracked + untracked SIN tocar working tree
STASH_COMMIT=$(git stash create --include-untracked 2>/dev/null || git stash create 2>/dev/null || echo "")

if [ -n "$STASH_COMMIT" ]; then
  # Crear branch apuntando al stash commit (tiene tracked + untracked + ignored)
  git branch "$BRANCH" "$STASH_COMMIT"
else
  # Fallback: no habia cambios stasheable-s; branch al HEAD
  git branch "$BRANCH"
fi

# Verificacion obligatoria
git rev-parse --verify "$BRANCH" >/dev/null || {
  echo "ERROR: no se pudo crear backup branch"
  exit 1
}

echo "Backup creado: $BRANCH (incluye untracked)"
```

**Nota:** `git stash create --include-untracked` crea un commit ternario (HEAD, index, untracked) sin modificar el working tree ni la pila de stashes. `git branch $BRANCH $stash_commit` lo hace accesible por nombre. Asi el usuario puede restaurar via `git checkout $BRANCH -- .claude/ CLAUDE.md` y recupera tambien archivos nuevos.

### Fase 4.5 — Re-check antes de escribir

```bash
if [ -n "$(git status --porcelain)" ]; then
  echo "ERROR: Hay cambios en el working tree despues del preview."
  echo "Posiblemente editaste archivos entre el preview y ahora."
  echo "Commit o stash esos cambios y vuelve a correr el skill."
  # Limpiar el backup branch huerfano creado en Fase 4
  git branch -D "$BRANCH" 2>/dev/null
  exit 1
fi
```

**Regla estricta:** trabajo tree debe estar VACIO. No se acepta "coincide con Fase 0".

### Fase 5 — Escritura atomica

```
[5.1] Crear staging: mkdir -p .claude.sync-tmp/

[5.2] Por cada archivo clasificado ADD o MERGE:
  - Calcular resultado final (merge en memoria si aplica).
  - Si es JSON → validar sintaxis ANTES de escribir:
      try: JSON.parse(contenido_final)
      catch: abortar, reportar error, cleanup de .claude.sync-tmp/
  - Escribir a .claude.sync-tmp/<ruta-relativa>.

[5.3] Validacion integral en staging:
  - Todos los SKILL.md tienen frontmatter valido.
  - settings.local.json parsea como JSON.
  - INDEX.md: por cada [text](path), el path existe en staging o en destino.

[5.4] Mover staging a destino (atomic per file). Comando exacto:
  ```bash
  # Recorrer staging en orden: primero archivos nuevos (ADD), luego merges
  # para que validaciones de links en INDEX.md encuentren los targets.
  cd .claude.sync-tmp

  find . -type f -print0 | while IFS= read -r -d '' rel; do
    # rel es "./<ruta>", ej. "./.claude/skills/foo/SKILL.md" o "./CLAUDE.md"
    src="$PWD/${rel#./}"
    dest="$PROJECT_DEST/${rel#./}"
    mkdir -p "$(dirname "$dest")"
    mv -f "$src" "$dest"  # atomic si mismo filesystem; si cross-filesystem mv hace copy+delete (no atomic pero funcional)
  done

  cd "$PROJECT_DEST"
  ```
  - Comilla toda variable (soporta rutas con espacios).
  - `mv -f` sobrescribe solo si esta autorizado (archivo ya clasificado como ADD o MERGE).
  - Orden de creacion NO importa porque las validaciones de Fase 7 corren al final.

[5.5] Actualizar .claude/.template-manifest.json con hashes nuevos.

[5.6] Escribir log: .claude/.sync-log/<timestamp>.json
  {
    "timestamp": "...",
    "template_source": "...",
    "backup_branch": "backup-pre-sync-...",
    "added": [...],
    "merged": [...],
    "preserved": [...],
    "skipped_irrelevant": [...],
    "asked": [...]
  }
  Tambien actualizar .claude/.sync-log/last.json con el mismo contenido.

[5.7] Limpiar: rm -r .claude.sync-tmp/  (contenido ya movido)
```

Si FALLA la validacion en [5.3]: abortar, cleanup de `.claude.sync-tmp/`, reportar error. Destino no fue tocado.

### Fase 6 — Reglas de merge por tipo

**`settings.local.json`:**
```
resultado = objeto nuevo

# Union conservadora: solo dedup por igualdad exacta de string. NUNCA eliminar
# permisos explicitos del usuario aunque haya wildcards en el template.
resultado.permissions.allow = dedup_exacto(destino.allow ∪ template.allow)
resultado.permissions.deny  = dedup_exacto(destino.deny  ∪ template.deny)

dedup_exacto(lista):
  - preservar orden: primero todos los de destino en orden original,
    luego los del template que NO estan ya presentes por igualdad exacta de string.
  - comparar case-sensitive.
  - NO interpretar wildcards. "Bash(npm run *)" y "Bash(npm run test)" son strings distintos
    y ambos se preservan. Mutar permisos del usuario es peor que tener duplicados logicos.

resultado.autoMemoryEnabled = destino.autoMemoryEnabled ?? template.autoMemoryEnabled
resultado.autoDreamEnabled  = destino.autoDreamEnabled  ?? template.autoDreamEnabled

resultado.hooks:
  - si destino NO tiene .hooks → copiar template.hooks completo
  - si destino SI tiene .hooks:
      Por cada hook del template, identificar por tripleta (event, matcher, command_exact):
        - si el triplete YA existe en destino → no duplicar
        - si no existe → marcar para preguntar en Fase 3. Opciones:
            (a) agregar solo los hooks del template que no tienes → recomendado
            (b) mantener solo tus hooks → no tocar
            (c) ver diff completo
        - NUNCA ofrecer "reemplazar todo" como default.

Validar JSON final antes de escribir (Fase 5.2).
```

**Nota importante:** La deteccion "preguntar en Fase 3" ocurre en Fase 2 (clasificacion), se muestra al usuario en Fase 3 (preview con decisiones pendientes), y la respuesta del usuario modifica el merge ANTES de Fase 5 (escritura). No hay pregunta interactiva durante la escritura.

**`CLAUDE.md`:**
- Si destino NO existe → copiar template.
- Si existe → aplicar normalizacion de headers definida en Fase 2.5 (trim + lowercase + colapso de espacios; NO mapeo EN↔ES).
  - Para cada header `## X` del template cuyo normalizado NO aparece en destino:
    - Mostrar en preview: "Se agregara seccion '## X' al final".
    - Insertar al final del archivo destino (no buscar `## References`, porque puede no existir).
  - Si header del template existe en destino (normalizado): marcar como `SKIP_CUSTOM` para ese bloque, NO sobrescribir.
  - NUNCA reordenar ni borrar nada del destino.

**`INDEX.md`:**
- Clasificacion (ADD / MERGE / ASK) determinada en Fase 2.5 usando ratio de headers.
- Si MERGE:
  - Agregar solo entradas nuevas (links `[text](path)`) que no existan literalmente en el destino.
  - NUNCA reordenar las existentes.
  - Preservar todas las secciones del usuario.
- Si ASK:
  - Preguntar en Fase 3: "Tu INDEX.md tiene estructura muy distinta al template. Opciones: (a) agregar seccion 'Skills nuevos' al final, (b) no tocar INDEX.md, (c) reemplazar completamente (NO recomendado — perderia tu estructura)".
  - Default sugerido: (b) no tocar.

**`.claude/memory/`:**
- NUNCA copiar, NUNCA tocar. Regla inviolable.

**Archivos de skill/rule/doc individuales:**
- Si path no existe en destino → copiar (ADD).
- Si existe con mismo hash normalizado → no tocar.
- Si existe con hash diferente → `SKIP_CUSTOM`, reportar en preview, NO sobrescribir salvo decision explicita del usuario.

### Fase 7 — Validacion post-sync

```
[7.1] Validar JSON de settings.local.json (ya validado en Fase 5, re-confirmar en disco).

[7.2] Para cada skill en .claude/skills/*/: verificar existe SKILL.md con frontmatter.

[7.3] Para cada link [text](path) en .claude/INDEX.md: verificar path existe.

[7.4] Si el proyecto tiene scripts typecheck/lint: ofrecer correrlos (no obligatorio, informativo).
      NO correr tests automaticamente (pueden fallar por razones ajenas al sync).
```

Si falla cualquier check:
- Reportar QUE fallo con detalle.
- Opciones: (a) `rollback automatico` → git reset al backup branch, (b) `arreglar manualmente` → el skill indica que tocar, (c) `aceptar y continuar`.
- **Si el fallo es JSON invalido de settings.local.json**: rollback automatico del archivo desde el backup branch (sin preguntar) — es la unica excepcion auto-revertiva, porque rompe el entorno.

### Fase 8 — Reporte + commit opcional

```
==========================================
SINCRONIZACION COMPLETADA
==========================================

Backup: <branch>
Agregados:     N archivos
Mergeados:     N archivos
Preservados:   N archivos
Omitidos:      N archivos (por stack o decision)

Log: .claude/.sync-log/<timestamp>.json

ROLLBACK (si algo salio mal):
  Arbol de decision:
  ¿Ya hiciste commit de estos cambios?
    NO → git checkout <backup-branch> -- .claude/ CLAUDE.md
          (restaura los archivos desde el backup sin cambiar de branch)
    SI  → git revert <commit-hash>
          (crea commit inverso; no pierde historial)
  ¿Quieres descartar TODO y volver al estado previo (destructivo)?
    → git reset --hard <backup-branch>
      (solo si no has pusheado y estas 100% seguro)
==========================================

¿Crear commit con estos cambios? (si/no)
```

Si "si":
```bash
# Listar archivos desde el log en vez de git add .claude/
# para evitar arrastrar .claude/memory/ u otros archivos del usuario.

LOG=".claude/.sync-log/last.json"

if command -v jq >/dev/null 2>&1; then
  # Soporta paths con espacios via NUL separator + while read
  jq -r '(.added + .merged)[]' "$LOG" | while IFS= read -r f; do
    [ -n "$f" ] && git add -- "$f"
  done
else
  # Fallback sin jq: grep + sed. Funciona si el JSON esta formateado una linea por entry.
  grep -oE '"[^"]+"' "$LOG" | grep -E '\.(md|json)' | sed 's/"//g' | while IFS= read -r f; do
    [ -f "$f" ] && git add -- "$f"
  done
fi

git commit -m "feat: sincronizar template claude code

- Backup branch: $BRANCH
- Detalles: $LOG

Co-Authored-By: Claude Opus 4.6 <noreply@anthropic.com>"
```

NUNCA `git push` automatico.

### Fase 9 — Consolidacion de CLAUDE.md (OPCIONAL — solo si el usuario lo pide)

**Activacion:** Esta fase NO se ejecuta automaticamente. Solo corre si:
- El usuario dice explicitamente "consolidar CLAUDE.md", "limpiar CLAUDE.md", o "fase 9"
- O si en Fase 3 el preview detecta un CLAUDE.md destino con >300 lineas y OFRECE (sin insistir):
  "Tu CLAUDE.md tiene N lineas. ¿Quieres consolidarlo despues del sync? (si/no — default: no)"

**Prerequisitos:**
- Fases 0-8 ya completadas exitosamente (el sync regular ya corrio).
- Backup branch de Fase 4 ya existe (NO crear backup adicional — el de Fase 4 cubre todo).
- Si se invoca FUERA de un sync (standalone): ejecutar Fase 0 (pre-flight) y Fase 4 (backup) primero.
  - **Excepcion standalone:** si el working tree tiene cambios sin commitear (ej. el usuario no commitio tras Fase 8), Fase 0.3 se RELAJA para standalone: en vez de abortar, ofrecer "Tienes cambios pendientes. ¿Quieres (a) que hagamos commit ahora — el skill ejecutara `git add` + `git commit` con mensaje descriptivo, (b) que hagamos stash — el skill ejecutara `git stash`, (c) cancelar — tu haces commit/stash manualmente y vuelves a correr?"

**Excepcion explicita a Fase 6:** Fase 6 dice "NUNCA reordenar ni borrar nada del destino" para CLAUDE.md. Fase 9 es la UNICA excepcion: reordena y redistribuye, pero SOLO cuando el usuario lo pide explicitamente. Esta excepcion NO aplica a Fases 0-8.

**Objetivo:** Tomar un CLAUDE.md extenso (300-1500+ lineas) y redistribuir su contenido a los archivos correctos de `.claude/`, dejando CLAUDE.md como resumen ejecutivo de ~80-150 lineas.

**Principio fundamental:** NUNCA se pierde contenido. Todo se MUEVE, nada se borra. El usuario puede verificar via diff que cada linea del CLAUDE.md original aparece en algun archivo destino.

#### [9.1] Parseo estructural del CLAUDE.md existente

Parsear CLAUDE.md en bloques usando SOLO estructura de headers Markdown:

```
bloques = []
bloque_actual = { header: null, nivel: 0, lineas: [], linea_inicio: N, linea_fin: N }
dentro_code_block = false
fence_pattern = null  # guarda el patron del fence actual para matchear cierre

Para cada linea del archivo:
  si dentro_code_block:
    si linea matchea fence_pattern (mismo o mas backticks que el que abrio):
      dentro_code_block = false
      fence_pattern = null
    agregar linea a bloque_actual.lineas
  sino si linea matchea /^(`{3,}|~{3,})/:  # abre fence (3+ backticks o tildes)
    dentro_code_block = true
    fence_pattern = regex que matchea >= len($1) backticks/tildes al inicio de linea
    agregar linea a bloque_actual.lineas
  sino si linea matchea /^(#{1,4})\s+(.+)/:
    cerrar bloque_actual → push a bloques
    abrir nuevo bloque: header = $2, nivel = len($1)
  sino:
    agregar linea a bloque_actual.lineas

# El contenido ANTES del primer header es bloque especial: "preambulo"
# Las lineas vacias entre bloques pertenecen al bloque anterior
# Code fences: soporta ``` y ~~~, nested (4+ backticks dentro de 3)
# Indented code blocks (4 espacios) NO se detectan — solo fenced.
#   Esto es aceptable: headers indentados 4+ espacios no son headers validos en Markdown.
```

Si el archivo NO tiene ningun header `##` (texto plano sin estructura):
- Clasificar TODO como `UNCATEGORIZED` → va a `.claude/docs/CLAUDE_ORIGINAL.md`
- Generar CLAUDE.md minimal desde template
- Reportar en preview: "Tu CLAUDE.md no tiene headers. Se movio completo a .claude/docs/CLAUDE_ORIGINAL.md"

#### [9.2] Clasificacion deterministica de bloques

Cada bloque se clasifica por su header normalizado (mismo algoritmo que Fase 2.5: trim + lowercase + colapsar espacios). La clasificacion usa LISTAS FIJAS, no inferencia.

**KEEP_ROOT — headers que se quedan en CLAUDE.md:**

```
Lista exacta (normalizado):
  "proyecto", "project"
  "tech stack", "stack"
  "architecture", "arquitectura", "architecture: feature-first"
  "commands", "comandos", "important: commands"
  "code conventions", "convenciones", "important: code conventions"
  "prohibitions", "prohibiciones", "important: prohibitions"
  "workflow", "workflow: plan > diffs > verify"
  "references", "referencias"

Regla: si el header normalizado del bloque EMPIEZA CON alguno de estos strings → KEEP_ROOT
Ejemplo: "## IMPORTANT: Code Conventions" normaliza a "important: code conventions"
  → match con "important: code conventions" → KEEP_ROOT
```

**MOVE_DOCS — headers que van a `.claude/docs/`:**

```
Lista exacta (normalizado):
  "api", "endpoints", "routes", "rutas"
  "deployment", "deploy", "despliegue"
  "environment", "env", "variables de entorno", "environment variables"
  "database", "base de datos", "schema", "migrations", "migraciones"
  "authentication", "auth", "autenticacion"
  "infrastructure", "infraestructura"
  "performance", "rendimiento"
  "monitoring", "monitoreo"
  "ci/cd", "ci", "cd", "pipeline"
  "docker", "containers", "contenedores"
  "third-party", "integraciones", "integrations"
  "changelog", "historial de cambios"

Regla: si el header normalizado del bloque EMPIEZA CON alguno de estos → MOVE_DOCS
Destino: .claude/docs/<HEADER_SLUG>.md
  Generacion de HEADER_SLUG (deterministica):
    1. Tomar header original (sin "## ")
    2. lowercase
    3. Reemplazar acentos con ASCII (á→a, é→e, í→i, ó→o, ú→u, ñ→n)
    4. Reemplazar todo caracter que NO sea [a-z0-9] con "-"
    5. Colapsar multiples "-" en uno
    6. Trim "-" del inicio y final
    7. Truncar a 50 chars (si el char 50 es "-", truncar en el "-" anterior)
  Ejemplo: "## Database Schema & Migrations" → .claude/docs/database-schema-migrations.md
Si el archivo destino YA EXISTE: APPEND al final con separador "---\n\n## [header original]\n"
```

**MOVE_IMPORTED — headers que PARECEN memory pero van a `.claude/docs/imported/` (NUNCA a `.claude/memory/`):**

**IMPORTANTE:** La regla ".claude/memory/ — NUNCA tocar" se mantiene INVIOLABLE incluso en Fase 9.
Contenido tipo "notas, decisiones, TODOs" se mueve a `.claude/docs/imported/` y el usuario decide
manualmente si quiere copiar algo a memory despues.

```
Lista exacta (normalizado):
  "notes", "notas", "session notes"
  "decisions", "decisiones", "technical decisions", "decisiones tecnicas"
  "todo", "tareas", "pendientes", "tasks"
  "blockers", "bloqueos", "issues conocidos", "known issues"
  "learnings", "aprendizajes", "lessons learned"
  "context", "contexto", "session context", "contexto de sesion"
  "history", "historial", "project history"

Regla: si normalizado EMPIEZA CON alguno → MOVE_IMPORTED
Destino: SIEMPRE .claude/docs/imported/<HEADER_SLUG>.md
  Ejemplo: "## Known Issues" → .claude/docs/imported/known-issues.md
  Ejemplo: "## TODO" → .claude/docs/imported/todo.md

NUNCA escribir a .claude/memory/. El preview indicara:
  "→ ## Known Issues → .claude/docs/imported/known-issues.md
     (Tip: revisa y copia manualmente a .claude/memory/BLOCKERS.md si lo deseas)"

Si el archivo destino YA EXISTE: APPEND con separador "---\n\n## [header original]\n"
```

**MOVE_RULES — headers sobre reglas especificas de path:**

```
Lista exacta (normalizado):
  "frontend rules", "reglas frontend", "frontend conventions"
  "backend rules", "reglas backend", "backend conventions"
  "testing rules", "reglas testing", "test conventions"
  "security rules", "reglas seguridad", "security conventions"
  "database rules", "reglas database", "reglas base de datos"
  "api rules", "reglas api"

Regla: si normalizado EMPIEZA CON alguno → MOVE_RULES
Destino: el .claude/rules/<tipo>.md correspondiente (frontend.md, testing.md, etc.)
Si el archivo rules YA EXISTE: APPEND al final
Si NO existe: crear nuevo con frontmatter basico (paths vacios — el usuario debe configurarlos)
```

**UNCATEGORIZED — no matchea ninguna lista:**

```
Destino: .claude/docs/CLAUDE_EXTRAS.md
APPEND con header original preservado.
Reportar CADA bloque uncategorized en el preview de Fase 9.3.
```

**Regla de precedencia:** Si un bloque matchea multiples listas (improbable pero posible), prioridad: KEEP_ROOT > MOVE_RULES > MOVE_IMPORTED > MOVE_DOCS > UNCATEGORIZED.

#### [9.3] Preview de consolidacion (OBLIGATORIO)

Antes de cualquier cambio, mostrar:

```
==========================================
CONSOLIDACION DE CLAUDE.md — PREVIEW
==========================================

CLAUDE.md actual: <N> lineas
CLAUDE.md proyectado: ~<M> lineas

Backup: <branch de Fase 4>

QUEDA EN CLAUDE.md (KEEP_ROOT):
  ✓ ## Proyecto: Mi App (lineas 1-8)
  ✓ ## Tech Stack (lineas 10-22)
  ✓ ## IMPORTANT: Commands (lineas 24-35)
  ✓ ## IMPORTANT: Code Conventions (lineas 37-52)
  ✓ ## IMPORTANT: Prohibitions (lineas 54-65)
  ✓ ## Workflow: PLAN > DIFFS > VERIFY (lineas 67-72)
  ✓ ## References (se regenera con links actualizados)

SE MUEVE A .claude/docs/:
  → ## Database Schema & Migrations (lineas 74-145) → .claude/docs/database-schema-migrations.md
  → ## API Endpoints (lineas 147-230) → .claude/docs/api-endpoints.md
  → ## Deployment Guide (lineas 232-310) → .claude/docs/deployment-guide.md

SE MUEVE A .claude/docs/imported/ (contenido tipo memory — revisa y copia manualmente a .claude/memory/ si deseas):
  → ## Known Issues (lineas 312-340) → .claude/docs/imported/known-issues.md
  → ## Session Notes (lineas 342-380) → .claude/docs/imported/session-notes.md

SE MUEVE A .claude/rules/:
  → ## Frontend Conventions (lineas 382-420) → .claude/rules/frontend.md (APPEND)

NO CLASIFICADO (va a .claude/docs/CLAUDE_EXTRAS.md):
  ⚠ ## Notas de Ivan sobre el diseño (lineas 422-460)
  ⚠ ## Cosas random del sprint pasado (lineas 462-490)

ARCHIVOS QUE SE MODIFICARAN:
  ✎ CLAUDE.md — reescritura completa (~M lineas)
  ✎ .claude/docs/database-schema-migrations.md — NUEVO
  ✎ .claude/docs/api-endpoints.md — NUEVO
  ✎ .claude/docs/imported/known-issues.md — NUEVO
  ✎ .claude/docs/imported/session-notes.md — NUEVO
  ✎ .claude/rules/frontend.md — APPEND (N lineas)
  ✎ .claude/docs/CLAUDE_EXTRAS.md — NUEVO (N lineas)
  ✎ .claude/INDEX.md — agregar links a docs nuevos

==========================================
Responde: consolidar / cancelar / preview <bloque> / mover <bloque> <destino> / keep <bloque> / preview-claude
==========================================
```

**Loop de interaccion (mismo patron que Fase 3):**
- `consolidar` → ejecutar
- `cancelar` → no tocar nada
- `preview <bloque>` → mostrar las primeras 15 lineas del contenido del bloque (para verificar clasificacion)
  - Matching por subcadena case-insensitive contra el header
  - Ejemplo: `preview "known issues"` → muestra contenido del bloque "## Known Issues"
- `mover <bloque> <destino>` → reclasificar un bloque manualmente
  - Ejemplo: `mover "notas de ivan" docs` → mueve a .claude/docs/ en vez de CLAUDE_EXTRAS.md
  - Destinos validos: `docs`, `imported` (docs/imported/), `rules`, `keep` (mantener en root)
  - Re-mostrar preview
- `keep <bloque>` → forzar que un bloque se quede en CLAUDE.md (shortcut de `mover X keep`)
- `preview-claude` → mostrar como quedaria el CLAUDE.md final (texto completo)

#### [9.4] Generacion del CLAUDE.md limpio

El CLAUDE.md final se construye asi (NO desde un template fijo — desde el contenido REAL del usuario):

```
1. Tomar todos los bloques clasificados como KEEP_ROOT
2. Ordenarlos en este orden canonico (si existen):
   a. Preambulo (nombre del proyecto, descripcion)
   b. Tech Stack
   c. Architecture (version RESUMIDA — max 15 lineas)
   d. Commands
   e. Code Conventions
   f. Prohibitions
   g. Workflow
   h. References (se REGENERA — ver paso 3)

3. Regenerar seccion References:
   - Mantener links que ya existian
   - Agregar links a CADA archivo nuevo creado por la consolidacion
   - Formato:
     "- See @.claude/docs/<archivo>.md for <descripcion del header original>"
   - Agregar al final:
     "<!-- Content consolidated from CLAUDE.md (<fecha>). Original backup: <backup-branch> -->"

4. Si un bloque KEEP_ROOT tiene >30 lineas:
   - TRUNCAR a las primeras 15 lineas
   - Agregar al final: "→ Full details: @.claude/docs/<header-slug>-full.md"
   - El contenido completo se copia a .claude/docs/<header-slug>-full.md (sufijo "-full" para
     evitar colision con archivos MOVE_DOCS que usan el slug sin sufijo)
   - Si el archivo -full.md ya existe por una corrida previa: SOBRESCRIBIR (es contenido del template)
   - Reportar en preview: "Bloque '<header>' tiene N lineas, se resumira en root"

5. Verificar que el resultado tiene entre 60 y 200 lineas.
   Si <60: advertir "El CLAUDE.md consolidado es muy corto. ¿Continuar?"
   Si >200: advertir "No se logro reducir a <200 lineas. ¿Quieres truncar mas bloques?"
```

#### [9.5] Escritura atomica (reutiliza mecanismo de Fase 5)

```
[9.5.1] Escribir TODO a .claude.sync-tmp/ (staging):
  - Nuevo CLAUDE.md
  - Cada archivo .claude/docs/ nuevo o modificado (incluye docs/imported/)
  - Cada archivo .claude/docs/<slug>-full.md (contenido completo de bloques KEEP_ROOT truncados)
  - Cada archivo .claude/rules/ modificado (con APPEND)
  - INDEX.md actualizado
  - NUNCA escribir a .claude/memory/ (regla inviolable)

[9.5.2] Para archivos que son APPEND (rules, docs existentes):
  - Leer archivo original del destino REAL (no del staging de Fases 5-8)
  - Concatenar contenido nuevo al final
  - Escribir resultado completo al staging
  - Necesario porque Fases 5-8 ya movieron sus archivos del staging al destino.

[9.5.3] Validar en staging:
  - CLAUDE.md tiene al menos las secciones: project name, commands, references
  - Todos los links en References apuntan a archivos existentes (en staging o destino)
  - INDEX.md parseabe

[9.5.4] Mover staging a destino (mismo mv atomico que Fase 5.4)

[9.5.5] Limpiar .claude.sync-tmp/
```

#### [9.6] Validacion post-consolidacion

```
[9.6.1] Verificar CERO PERDIDA de contenido (doble check):
  Metodo 1 — Por bloques:
  - Para CADA bloque parseado en 9.1, verificar que aparece COMPLETO en exactamente uno de:
    a) El nuevo CLAUDE.md (bloques KEEP_ROOT, posiblemente truncados)
    b) Un archivo .claude/docs/ o .claude/rules/ (bloques movidos)
    c) Un archivo .claude/docs/<slug>-full.md (contenido completo de bloques truncados)
  - "Aparece completo" = todas las lineas no-vacias del bloque original existen en SU archivo destino especifico
    (el que fue asignado en la clasificacion, NO buscar globalmente en todos los archivos)
  - Si algún bloque NO aparece completo en ningun destino: ABORTAR y listar cuales faltan.

  Metodo 2 — Conteo total (sanity check):
  - Contar lineas no-vacias del CLAUDE.md original
  - Sumar lineas no-vacias NUEVAS escritas por la consolidacion (excluyendo separadores/comentarios
    de import y contenido que ya existia en archivos destino)
  - Tolerancia: ±10 lineas (separadores, links de referencia, comentarios HTML)
  - Si delta > 10: ADVERTIR (no abortar — el Metodo 1 es el autoritativo)

[9.6.2] Verificar que CLAUDE.md final tiene <200 lineas (advertir si no)

[9.6.3] Verificar links en References apuntan a archivos existentes
```

Mostrar resumen:

```
==========================================
CONSOLIDACION COMPLETADA
==========================================

CLAUDE.md: <N_original> lineas → <N_final> lineas
Contenido redistribuido a:
  .claude/docs/database-schema-migrations.md (72 lineas)
  .claude/docs/api-endpoints.md (84 lineas)
  .claude/docs/imported/known-issues.md (29 lineas — revisar y copiar a memory/ si deseas)
  .claude/rules/frontend.md (+39 lineas)
  .claude/docs/CLAUDE_EXTRAS.md (69 lineas — revisar manualmente)

Verificacion: 0 lineas perdidas ✓

ROLLBACK (mismo backup que el sync):
  git checkout <backup-branch> -- CLAUDE.md .claude/

¿Crear commit? (si/no)
==========================================
```

Si "si":
```bash
git add CLAUDE.md .claude/docs/*.md .claude/docs/imported/*.md .claude/rules/*.md .claude/INDEX.md

git commit -m "refactor: consolidar CLAUDE.md — redistribuir contenido a .claude/

- CLAUDE.md reducido de N a M lineas
- Contenido movido a .claude/docs/, .claude/docs/imported/, .claude/rules/
- Backup branch: $BRANCH
- Zero content loss verified

Co-Authored-By: Claude Opus 4.6 <noreply@anthropic.com>"
```

NUNCA `git push` automatico.

## Protecciones contra daño (matriz)

| Riesgo | Mitigacion |
|--------|-----------|
| Perder contenido custom | Backup branch + preview + SKIP_CUSTOM + re-check git status |
| Hooks custom pisados | Detectar por tripleta (event, matcher, command), preguntar, nunca reemplazar default |
| settings.local.json corrompido | Validar JSON en memoria antes de escribir; rollback auto si falla |
| Untracked archivos perdidos | `git stash create --include-untracked` + `git branch` captura tracked + untracked sin tocar working tree |
| Rollback sin commit | Backup branch referencia el HEAD pre-sync |
| Doble ejecucion | Manifest + last.json detectan sync reciente |
| Rutas con espacios (Windows) | TODOS los paths citados con `"$var"` en bash |
| Ctrl+C a mitad | Staging `.claude.sync-tmp/` nunca contamina destino |
| Backup branch colision | Sufijo `-$RANDOM` si el nombre ya existe |
| .gitignore ignora .claude/ | Detectado en Fase 0.5, advertir y pedir confirmacion |
| Submodule / solo-lectura | Detectado en Fase 0.2 |
| Monorepo ambiguo | Detectado en Fase 1, preguntar target |
| `src/legacy/` del destino | Skill solo toca `.claude/` y `CLAUDE.md`, nunca `src/` |
| Skill Python en proyecto Rust | Reglas de stack en Fase 2 clasifican como SKIP_IRRELEVANT |
| Header renombrado (Prohibitions vs Prohibiciones) | Normalizacion en merge de CLAUDE.md |
| INDEX.md completamente distinto | Clasificado como ASK, preguntar al usuario |
| Consolidacion pierde contenido | Verificacion por bloques (9.6.1 Metodo 1): aborta si algun bloque no aparece completo en destino. Conteo total (Metodo 2, ±10): advierte |
| Bloque sin header reconocido | Va a CLAUDE_EXTRAS.md, reportado en preview, usuario puede reclasificar |
| CLAUDE.md sin headers (texto plano) | Todo va a CLAUDE_ORIGINAL.md, se genera CLAUDE.md minimal |
| Fase 9 corre sin sync previo | Exige Fase 0 + Fase 4 como prerequisito standalone |
| Consolidacion no deseada | NUNCA automatica, solo con peticion explicita del usuario |
| Archivo destino ya tiene contenido | APPEND con separador, nunca reemplazar |

## Cuando abortar

Aborta con mensaje claro si:
- No es repo git (Fase 0.1)
- Es submodule o solo-lectura (Fase 0.2)
- Hay cambios sin commitear (Fase 0.3 o 4.5)
- TEMPLATE_SOURCE no existe o == PROJECT_DEST (Fase 0.4)
- Usuario no provee TEMPLATE_SOURCE
- Usuario responde algo distinto a `aplicar` en Fase 3
- Validacion JSON post-merge falla (rollback del archivo, no del repo)
- Falla la creacion del backup branch tras 3 intentos
- `.claude.sync-tmp/` no puede crearse (permisos)
- Verificacion por bloques (Fase 9.6.1 Metodo 1) detecta bloque no preservado completo en ningun destino
- Usuario cancela en preview de consolidacion (Fase 9.3)

## Rollback — arbol de decision (para el usuario en panico)

```
¿Corriste el skill y algo salio mal?

Paso 1: ¿Hiciste commit de los cambios del sync?
  NO  → Paso 2
  SI  → Paso 3

Paso 2: (sin commit)
  git checkout <backup-branch> -- .claude/ CLAUDE.md
  → Restaura los archivos desde el backup. No cambia de branch.
  → Verifica con: git status

Paso 3: (con commit)
  git revert HEAD
  → Crea un nuevo commit que deshace el anterior.
  → Seguro, no pierde historial.

Opcion nuclear (destructiva, solo si sabes lo que haces):
  git reset --hard <backup-branch>
  → Solo si NO has pusheado y estas dispuesto a perder cualquier cambio posterior.
```

El skill DEBE mostrar este arbol al final de Fase 8, con el nombre real del backup branch interpolado.

## Rules

1. **Idempotente** — correr 2 veces sobre mismo proyecto no duplica (manifest + hash).
2. **Sin operaciones destructivas** — nada de `rm -rf`, `--force`, `reset --hard` automatico.
3. **Preview antes de aplicar** — el usuario ve TODO.
4. **Preservar por defecto** — en la duda, NO sobrescribir.
5. **Transaccional** — staging + mv atomico, no archivos parciales.
6. **Log de auditoria** — `.claude/.sync-log/` con timestamp por corrida.
7. **Español consistente** — frontmatter puede tener terminos tecnicos en EN (model, effort), cuerpo en español.

## Common Rationalizations

| Excusa | Realidad |
|--------|---------|
| "El usuario seguro quiere esto, lo sobrescribo" | No. Custom code es su trabajo. Pregunta. |
| "Backup branch es redundante si tiene git" | Recuperar commits toma 30 min; crear branch toma 1 seg. |
| "Saltamos el preview, es largo" | El preview es la promesa de seguridad. Nunca skip. |
| "Merge de hooks es complejo, reemplazo" | Reemplazar hooks custom = perder trabajo del usuario. Pregunta. |
| "El stack no importa, copio todo" | Next.js skill en Python = ruido. Detecta. |
| "El diff es solo un espacio, sobrescribo" | Normaliza whitespace. Si sigue distinto, preserva. |
| "No hace falta manifest, re-detecto cada vez" | Sin manifest, idempotencia es imposible. |
| "El CLAUDE.md es largo pero funciona, consolido automaticamente" | No. Consolidacion es OPCIONAL. Un CLAUDE.md largo funciona, solo es lento de parsear. |
| "Este bloque parece de docs, lo muevo sin preguntar" | No. Si no matchea la lista fija, va a EXTRAS y se reporta. |

## Integracion

- Corre ANTES de empezar a trabajar en un proyecto existente que quieres modernizar.
- Compatible con `/code-review` y `/pre-mortem` despues del sync.
- El log `.claude/.sync-log/last.json` permite auditoria y re-runs idempotentes.
- Si falla, el backup branch permite volver en un paso.
- Fase 9 (consolidacion) es OPCIONAL y corre DESPUES de Fases 0-8 (sync regular).
- Puede invocarse standalone con "consolidar CLAUDE.md" (ejecuta Fases 0 y 4 como prerequisito).
- Especialmente util en proyectos legacy donde CLAUDE.md ha crecido organicamente a 500+ lineas.
