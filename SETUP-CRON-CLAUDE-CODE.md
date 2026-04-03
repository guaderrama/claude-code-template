# Cómo programar tareas automáticas con Claude Code

> Instrucciones para configurar Claude Code en modo headless con cron en un VPS.
> Probado y funcionando en code-server (Docker) con Claude Code 2.1+
> Suscripción: Max ($200/mes, uso ilimitado de tokens)

---

## Requisitos

- Un VPS o servidor que esté encendido 24/7
- Claude Code CLI instalado (`which claude`)
- Cron daemon corriendo (`ps aux | grep cron`)
- Sesión autenticada de Claude (`claude auth status`)

---

## 1. Crear el script bash

```bash
#!/bin/bash
# /ruta/proyecto/automation/scripts/mi-tarea.sh

PROJECT_PATH="/ruta/proyecto"
LOG_DIR="$PROJECT_PATH/automation/logs"
DATE=$(date +%Y-%m-%d)
LOG_FILE="$LOG_DIR/mi-tarea-$DATE.log"

# Environment — ajustar según tu entorno
export PATH="/config/.local/bin:/config/.bun/bin:/usr/local/bin:/usr/bin:/bin"
export HOME="/config"

mkdir -p "$LOG_DIR"
echo "[$(date '+%H:%M:%S')] === Tarea Started ===" >> "$LOG_FILE"

cd "$PROJECT_PATH" || exit 1

# Ejecutar Claude Code en modo headless
claude -p "
Tu prompt aquí. Sé específico con los pasos que debe seguir.

PASO 1: ...
PASO 2: ...
PASO 3: ...
" \
  --allowedTools "Bash,Read,Write,Edit,Glob,Grep" \
  --max-turns 30 \
  --no-session-persistence >> "$LOG_FILE" 2>&1

echo "[$(date '+%H:%M:%S')] === Tarea Complete ===" >> "$LOG_FILE"
```

### Hacer ejecutable:
```bash
chmod +x /ruta/proyecto/automation/scripts/mi-tarea.sh
```

---

## 2. Flags importantes

| Flag | Qué hace | Recomendación |
|------|----------|---------------|
| `-p "prompt"` | Modo headless (sin sesión interactiva) | **Obligatorio** para cron |
| `--allowedTools "..."` | Herramientas que puede usar sin pedir permiso | **Recomendado** — lista solo las necesarias |
| `--max-turns 30` | Máximo de pasos agénticos (evita loops infinitos) | **Recomendado** — ajustar según complejidad |
| `--no-session-persistence` | No guarda la sesión en disco | **Recomendado** para cron (ahorra espacio) |
| `--model sonnet` | Usar un modelo específico | Opcional — con Max no hay diferencia de costo |
| `--max-budget-usd 1.00` | Límite de gasto por ejecución | **No aplica** con suscripción Max (uso ilimitado) |
| `--dangerously-skip-permissions` | Saltar TODOS los permisos | **NO recomendado** — usar `--allowedTools` es más seguro |
| `--append-system-prompt "..."` | Agregar instrucciones al system prompt | Opcional — útil para reglas globales |
| `--output-format text` | Formato de salida (text, json, stream-json) | Opcional |

---

## 3. Configurar el crontab

```bash
# Editar crontab
crontab -e

# Ejemplo: todos los días a las 5:00 AM (hora del servidor)
0 5 * * * /ruta/proyecto/automation/scripts/mi-tarea.sh

# Ejemplo: cada lunes a las 4:00 AM
0 4 * * 1 /ruta/proyecto/automation/scripts/mi-tarea.sh

# Ejemplo: cada 3 días a las 10:00 PM
0 22 */3 * * /ruta/proyecto/automation/scripts/mi-tarea.sh
```

### Verificar que cron está activo:
```bash
crontab -l          # Ver tareas programadas
ps aux | grep cron  # Verificar que el daemon corre
```

---

## 4. Problema común en Docker: cron en sleep infinity

Si tu servidor es un contenedor Docker (como code-server), el servicio de cron
puede no estar corriendo si el crontab estaba vacío cuando el contenedor arrancó.

### Síntoma:
```bash
ps aux | grep "sleep infinity"
# Si ves un proceso sleep infinity del servicio cron, está dormido
```

### Solución:
```bash
# Matar el sleep para que s6 reinicie el servicio de cron
docker exec -u root CONTAINER_ID kill $(docker exec CONTAINER_ID pgrep -f "sleep infinity")

# Verificar que cron arrancó
ps aux | grep cron
```

### Prevenir en reinicios:
Crear un script en `/config/custom-cont-init.d/` que restaure el crontab:

```bash
#!/bin/bash
# /config/custom-cont-init.d/50-restore-crontab.sh
CRONTAB_FILE="/ruta/proyecto/automation/crontab.txt"
if [ -f "$CRONTAB_FILE" ]; then
    crontab -u abc "$CRONTAB_FILE"
    echo "[cron] Crontab restored"
fi
```

```bash
chmod +x /config/custom-cont-init.d/50-restore-crontab.sh
```

Y guardar un backup del crontab:
```bash
crontab -l > /ruta/proyecto/automation/crontab.txt
```

---

## 5. Ejemplo completo: tarea de investigación + generación

```bash
#!/bin/bash
# Investiga tendencias y genera contenido basado en lo encontrado

PROJECT_PATH="/ruta/proyecto"
LOG_FILE="$PROJECT_PATH/automation/logs/trending-$(date +%Y-%m-%d).log"

export PATH="/config/.local/bin:/config/.bun/bin:/usr/local/bin:/usr/bin:/bin"
export HOME="/config"
mkdir -p "$(dirname $LOG_FILE)"

echo "[$(date '+%H:%M:%S')] === Started ===" >> "$LOG_FILE"
cd "$PROJECT_PATH" || exit 1

claude -p "
Lee CLAUDE.md para contexto del proyecto.

PASO 1: Investiga tendencias usando Apify MCP.
PASO 2: Analiza los datos y elige el mejor tema.
PASO 3: Genera el contenido basado en ese tema.
PASO 4: Guarda todo en output/ con metadata.json.

NO publiques nada. Solo genera y guarda.
" \
  --allowedTools "Bash,Read,Write,Edit,Glob,Grep,mcp__apify__search-actors,mcp__apify__fetch-actor-details,mcp__apify__call-actor,mcp__apify__get-actor-output" \
  --max-turns 30 \
  --no-session-persistence >> "$LOG_FILE" 2>&1

echo "[$(date '+%H:%M:%S')] === Complete ===" >> "$LOG_FILE"
```

---

## 6. Verificar que funciona

### Test rápido (ejecutar manualmente):
```bash
claude -p "Responde solo: OK FUNCIONO" --max-turns 3 --no-session-persistence --output-format text
```

### Test con herramientas:
```bash
claude -p "Lee el archivo README.md y dime cuántas líneas tiene." \
  --allowedTools "Bash,Read" \
  --max-turns 5 \
  --no-session-persistence \
  --output-format text
```

### Test del script completo:
```bash
# Ejecutar manualmente y revisar el log
bash /ruta/proyecto/automation/scripts/mi-tarea.sh
cat /ruta/proyecto/automation/logs/mi-tarea-$(date +%Y-%m-%d).log
```

---

## 7. Tips

- **CLAUDE.md**: Claude lee automáticamente el CLAUDE.md de tu proyecto. Úsalo para darle contexto permanente.
- **Prompts específicos**: Sé muy detallado en los pasos. Claude en headless no puede preguntarte — tiene que saber exactamente qué hacer.
- **Logs**: Siempre redirige la salida a un log. Sin logs no sabrás qué hizo (o qué falló).
- **allowedTools**: Lista SOLO las herramientas necesarias. No des acceso a todo.
- **max-turns**: 30 es un buen default. Subir a 50 para tareas muy complejas.
- **Timezone**: Los horarios de cron usan la hora del servidor. Verifica con `date`.
- **MCP tools**: Si necesitas Apify, Blotato, u otros MCPs, agrégalos a `--allowedTools` con el prefijo `mcp__servidor__tool-name`.
