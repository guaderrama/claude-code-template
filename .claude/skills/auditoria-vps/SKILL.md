---
name: auditoria-vps
description: "Auditoria completa de todos los VPS de Hostinger: salud del sistema, seguridad, contenedores Docker, code-servers, procesos zombie, cron jobs, URLs, SSL, firewall, y reporte detallado. Usar cuando Ivan diga 'auditoriar', 'audit', 'checar servidores', 'revisar VPS', 'reporte de servidores', 'como estan los servers'."
allowed-tools: Bash, Read, Grep, Glob, Agent, TodoWrite
model: opus
effort: high
---

# Auditoria VPS Completa

Ejecuta una auditoria exhaustiva de TODOS los VPS de Ivan en Hostinger. Genera un reporte detallado con problemas, recomendaciones y estado de cada servicio.

## Inventario de VPS

Consulta siempre el archivo de memoria para obtener las IPs, llaves SSH y datos actualizados:
- Archivo de memoria: `C:\Users\ivang\.claude\projects\c--Users-ivang-Dropbox-Ai-HOSTINGER-VPS\memory\MEMORY.md`

### Conexiones SSH conocidas:
```
VPS#2 (Principal):  ssh -i ~/.ssh/id_rsa_hostinger -o IdentitiesOnly=yes -o ConnectTimeout=10 root@31.220.59.12
VPS#4 (Grace):      ssh -i ~/.ssh/vps4_new -o ConnectTimeout=10 root@187.124.150.159
VPS#1 (Comprometido): ssh -i ~/.ssh/vps_compromised_new -o ConnectTimeout=10 root@31.220.56.130
VPS#3 (LosCabos):   ssh -i ~/.ssh/vps3_new -o ConnectTimeout=10 root@93.188.165.234
```

> IMPORTANTE: Siempre usar `-o IdentitiesOnly=yes` en VPS#2 para evitar enviar multiples llaves y ser baneado por fail2ban.

## Protocolo de Auditoria

Ejecutar TODAS las fases en paralelo donde sea posible usando el tool Agent o llamadas Bash paralelas. Maximizar eficiencia.

---

### FASE 1: Conectividad
Verificar acceso SSH a cada VPS. Si alguno falla, reportar el error exacto y continuar con los demas.

```bash
# Para cada VPS:
ssh [conexion] "echo 'OK' && uptime" 2>&1
```

**Reportar:** Accesible/No accesible, motivo del fallo si aplica.

---

### FASE 2: Sistema (por cada VPS accesible)

Ejecutar en un solo comando SSH para minimizar conexiones:

```bash
echo "=== SISTEMA ==="
echo "Hostname: $(hostname)"
echo "OS: $(lsb_release -ds 2>/dev/null)"
echo "Kernel: $(uname -r)"
echo "Uptime: $(uptime -p)"
echo "Load: $(cat /proc/loadavg)"
echo ""

echo "=== RECURSOS ==="
free -h | head -3
df -h / | tail -1
echo "Inodes: $(df -i / | tail -1 | awk '{print $5}')"
echo ""

echo "=== REBOOT PENDIENTE ==="
test -f /var/run/reboot-required && cat /var/run/reboot-required || echo "No"
echo ""

echo "=== UPDATES PENDIENTES ==="
apt list --upgradable 2>/dev/null | grep -c -v "^Listing"
echo "paquetes"
```

**Alertas:**
- RAM > 80% = CRITICO
- Disco > 85% = CRITICO
- Load > num_cpus * 2 = ALTO
- Reboot requerido = MEDIO
- Updates > 10 = MEDIO

---

### FASE 3: Seguridad (por cada VPS accesible)

```bash
echo "=== FIREWALL ==="
ufw status verbose 2>/dev/null || echo "UFW no instalado"
echo ""

echo "=== FAIL2BAN ==="
fail2ban-client status 2>/dev/null || echo "No instalado"
fail2ban-client status sshd 2>/dev/null | tail -5
echo ""

echo "=== SSH CONFIG ==="
grep -E "^(PermitRootLogin|PasswordAuthentication|PubkeyAuthentication|MaxAuthTries)" /etc/ssh/sshd_config 2>/dev/null
echo ""

echo "=== DOCKER-USER IPTABLES ==="
iptables -L DOCKER-USER -n -v 2>/dev/null | head -15
echo ""

echo "=== PUERTOS EXPUESTOS (no localhost) ==="
ss -tlnp | grep -v "127.0.0" | sort
echo ""

echo "=== PROCESOS SOSPECHOSOS ==="
ps aux | grep -iE "xmrig|cryptominer|kdevtmpfsi|kinsing|scanner_linux|\.hidden|/tmp/\." | grep -v grep || echo "LIMPIO - ninguno encontrado"
echo ""

echo "=== ARCHIVOS SOSPECHOSOS EN /tmp ==="
find /tmp -type f -executable 2>/dev/null | head -10 || echo "Ninguno"
echo ""

echo "=== CONEXIONES SOSPECHOSAS ==="
ss -tnp | grep ESTAB | grep -v -E ":(22|80|443|5678|8443|8082) " | head -10
echo ""

echo "=== ULTIMOS LOGINS ==="
last -5 -w
echo ""

echo "=== INTENTOS SSH FALLIDOS (24h) ==="
journalctl -u ssh --since "24 hours ago" 2>/dev/null | grep -c "Failed\|Invalid" || echo "N/A"
```

**Alertas:**
- UFW inactivo = CRITICO
- fail2ban no instalado = CRITICO
- PermitRootLogin yes = CRITICO
- PasswordAuthentication yes = ALTO
- Procesos sospechosos encontrados = CRITICO
- Archivos ejecutables en /tmp = ALTO
- > 100 intentos SSH fallidos en 24h = MEDIO

---

### FASE 4: Docker y Contenedores (por cada VPS con Docker)

```bash
echo "=== DOCKER VERSION ==="
docker --version
docker compose version
echo ""

echo "=== CONTENEDORES ==="
docker ps -a --format 'table {{.Names}}\t{{.Status}}\t{{.Image}}' | sort
echo ""

echo "=== CONTENEDORES CON PROBLEMAS ==="
docker ps -a --filter "status=exited" --format '{{.Names}}: {{.Status}} ({{.Image}})' | grep -v "migrator\|setup\|init"
docker ps -a --filter "status=dead" --format '{{.Names}}: DEAD'
docker ps -a --filter "status=restarting" --format '{{.Names}}: RESTARTING LOOP'
echo ""

echo "=== DOCKER STATS (memoria) ==="
docker stats --no-stream --format 'table {{.Name}}\t{{.CPUPerc}}\t{{.MemUsage}}' | sort -k3 -h -r | head -25
echo ""

echo "=== DOCKER DISK ==="
docker system df
echo ""

echo "=== RESTART POLICIES ==="
docker inspect --format '{{.Name}}: {{.HostConfig.RestartPolicy.Name}}' $(docker ps -aq) 2>/dev/null | grep -v "always\|unless-stopped" | head -10 || echo "Todos tienen restart policy"
```

**Alertas:**
- Contenedor exited (no migrator/init) = MEDIO
- Contenedor dead = CRITICO
- Contenedor restarting = CRITICO
- Contenedor > 80% memory limit = ALTO
- Docker images reclaimable > 20 GB = MEDIO
- Contenedor sin restart policy = MEDIO

---

### FASE 5: Code Servers (solo VPS#2)

Para CADA code-server, ejecutar:

```bash
SERVERS="visual-studio-code-server-bots-code-server-1 code-server-imelda code-server-lab code-server-cabohealth code-server-admon code-server-marketing"

for cs in $SERVERS; do
  echo "=== $cs ==="
  
  # Status basico
  echo "Status: $(docker inspect --format='{{.State.Status}}' $cs)"
  echo "Started: $(docker inspect --format='{{.State.StartedAt}}' $cs)"
  
  # Procesos Claude Code (zombie detection)
  echo "Claude processes:"
  docker exec $cs ps aux 2>/dev/null | grep claude | grep -v grep | awk '{print "  PID:"$2, "CPU:"$3"%", "MEM:"$4"%", "START:"$9, "CMD:"$11}' || echo "  none"
  
  # Dev servers stuck
  echo "Dev servers:"
  docker exec $cs ps aux 2>/dev/null | grep -E "next-server|vite|webpack-dev|nodemon|tsx watch" | grep -v grep | awk '{print "  PID:"$2, "CPU:"$3"%", "MEM:"$4"%", "START:"$9, "CMD:"$11,$12}' || echo "  none"
  
  # Node version
  echo "Node: $(docker exec $cs node --version 2>/dev/null || echo 'NOT INSTALLED')"
  
  # Claude CLI version
  echo "Claude CLI: $(docker exec $cs bash -c 'export PATH="/config/.local/share/npm/bin:$PATH"; claude --version 2>/dev/null || echo "NOT INSTALLED"')"
  
  # Total processes
  total=$(docker exec $cs ps aux 2>/dev/null | wc -l)
  echo "Total processes: $total"
  
  # Disk usage
  echo "Workspace: $(docker exec $cs du -sh /config/workspace 2>/dev/null | awk '{print $1}')"
  
  # Cron jobs
  echo "Cron jobs:"
  docker exec $cs bash -c 'crontab -l 2>/dev/null | grep -v "^#" | grep -v "^$" || echo "  NINGUNO"'
  
  # Init scripts
  echo "Init scripts:"
  docker exec $cs ls /custom-cont-init.d/ 2>/dev/null || echo "  NO DIR"
  
  # Tmux sessions
  echo "Tmux: $(docker exec $cs bash -c 'tmux ls 2>/dev/null || echo "none"')"
  
  echo "---"
done
```

**Alertas:**
- Claude processes > 3 en un contenedor = ALTO (zombie acumulados)
- Dev servers corriendo > 7 dias = MEDIO (posiblemente stuck)
- Total processes > 30 = MEDIO
- Cron jobs = NINGUNO en todos = INFO (verificar si deberian tener)
- Workspace > 10 GB = MEDIO
- Node NOT INSTALLED = CRITICO
- Claude CLI NOT INSTALLED en Ivan = INFO (usa extension)

---

### FASE 6: URLs y SSL (solo VPS#2)

```bash
echo "=== HEALTH CHECK URLs ==="
URLS="
https://code.ariai.cloud
https://imelda.ariai.cloud
https://lab.ariai.cloud
https://cabohealth.ariai.cloud
https://admon.ariai.cloud
https://marketing.ariai.cloud
https://n8n.srv1131496.hstgr.cloud
https://monday.ariai.cloud
"

for url in $URLS; do
  status=$(curl -sk -o /dev/null -w "%{http_code}|%{time_total}s" --max-time 10 "$url" 2>/dev/null)
  echo "$url -> $status"
done

echo ""
echo "=== SSL CERTIFICATES ==="
DOMAINS="code.ariai.cloud imelda.ariai.cloud lab.ariai.cloud cabohealth.ariai.cloud admon.ariai.cloud marketing.ariai.cloud monday.ariai.cloud n8n.srv1131496.hstgr.cloud"

for domain in $DOMAINS; do
  expiry=$(echo | openssl s_client -servername "$domain" -connect "$domain":443 2>/dev/null | openssl x509 -noout -enddate 2>/dev/null | cut -d= -f2)
  if [ -n "$expiry" ]; then
    days_left=$(( ($(date -d "$expiry" +%s) - $(date +%s)) / 86400 ))
    echo "$domain -> Expira: $expiry ($days_left dias)"
  else
    echo "$domain -> ERROR: No se pudo obtener certificado"
  fi
done
```

**Alertas:**
- HTTP 502/503/500 = CRITICO
- HTTP 000 (timeout) = CRITICO
- Response time > 5s = ALTO
- SSL expira en < 14 dias = CRITICO
- SSL expira en < 30 dias = MEDIO

---

### FASE 7: Servicios Especificos (solo VPS#2)

```bash
echo "=== TRAEFIK ==="
echo "Status: $(docker inspect --format='{{.State.Status}}' root-traefik-1)"
echo "Errores recientes:"
docker logs root-traefik-1 --since 1h 2>&1 | grep -i "err\|warn\|fatal" | tail -5 || echo "Sin errores"
echo ""

echo "=== N8N ==="
echo "Status: $(docker inspect --format='{{.State.Status}}' root-n8n-1)"
echo "Errores recientes:"
docker logs root-n8n-1 --since 1h 2>&1 | grep -iE "error|fatal|crash" | tail -5 || echo "Sin errores"
echo ""

echo "=== PLANE ==="
echo "Containers:"
docker ps --filter 'name=plane' --format '{{.Names}}: {{.Status}}' | sort
echo "API errors:"
docker logs plane-api-1 --since 1h 2>&1 | grep -iE "error|exception" | tail -3 || echo "Sin errores"
echo ""

echo "=== TRIGGER.DEV ==="
docker ps --filter 'name=trigger' --format '{{.Names}}: {{.Status}}' | sort
echo ""

echo "=== FIRECRAWL ==="
docker ps --filter 'name=firecrawl' --format '{{.Names}}: {{.Status}}' | sort
echo ""

echo "=== MIXPOST ==="
docker ps --filter 'name=mixpost' --format '{{.Names}}: {{.Status}}' | sort
echo ""

echo "=== DOCKER-USER FIREWALL SERVICE ==="
systemctl is-active docker-user-firewall.service 2>/dev/null
```

---

### FASE 8: Traefik Dynamic Config

```bash
echo "=== RUTAS DINAMICAS ==="
ls /root/traefik/dynamic/
echo ""
for f in /root/traefik/dynamic/*.yml; do
  echo "--- $(basename $f) ---"
  cat "$f"
  echo ""
done
```

**Alertas:**
- Rutas apuntando a dominios sin DNS = ALTO
- Rutas apuntando a contenedores que no existen = CRITICO

---

## Formato del Reporte

Al finalizar TODAS las fases, generar un reporte con esta estructura:

```markdown
# Auditoria VPS - [FECHA]

## Resumen Ejecutivo
- VPS auditados: X/4
- Problemas criticos: X
- Problemas altos: X
- Problemas medios: X

## Estado por VPS

### VPS#2 (31.220.59.12) - Principal
| Metrica | Valor | Estado |
|---------|-------|--------|
| OS/Kernel | ... | OK/ALERTA |
| RAM | X / Y (Z%) | OK/ALERTA |
| Disco | X / Y (Z%) | OK/ALERTA |
| Load | X | OK/ALERTA |
| Contenedores | X running / Y total | OK/ALERTA |
| UFW | Activo/Inactivo | OK/CRITICO |
| fail2ban | Activo/Inactivo | OK/CRITICO |
| SSL | Expira en X dias | OK/ALERTA |

### Code Servers
| Server | URL | HTTP | SSL | RAM | Claude | Zombies |
|--------|-----|------|-----|-----|--------|---------|
| Ivan | ... | ... | ... | ... | ... | ... |
| ... | | | | | | |

### Servicios
| Servicio | Estado | RAM | Errores |
|----------|--------|-----|---------|
| Traefik | ... | ... | ... |
| n8n | ... | ... | ... |
| Plane | ... | ... | ... |
| ... | | | |

### VPS#4 (187.124.150.159) - Grace
(misma estructura)

### VPS#1 / VPS#3
(estado de conexion, motivo si no accesible)

## Problemas Encontrados (ordenados por severidad)

### CRITICO
1. [Descripcion] - [VPS] - [Accion recomendada]

### ALTO
1. ...

### MEDIO
1. ...

## Acciones Recomendadas (ordenadas por prioridad)
1. [Accion] - [Impacto] - [Riesgo] - [Downtime estimado]

## Comparacion con Auditoria Anterior
(Si hay notas en memoria, comparar y reportar mejoras o regresiones)
```

## Reglas Importantes

1. **NUNCA modificar nada** durante la auditoria. Solo lectura y diagnostico.
2. **Ejecutar en paralelo** donde sea posible (VPS diferentes, fases independientes).
3. **Minimizar conexiones SSH** - agrupar comandos en un solo SSH por fase.
4. **Si fail2ban banea**, reportarlo como problema y continuar con otros VPS.
5. **Comparar con auditorias anteriores** si hay datos en memoria.
6. **Reportar en espanol** siempre.
7. **No proponer fixes automaticamente** - solo reportar y recomendar. Ivan decide que arreglar.
8. **Guardar resumen en memoria** despues de cada auditoria para comparaciones futuras.
