---
name: security-review
description: "Comprehensive security audit of code changes against OWASP Top 10, authentication flaws, data exposure, and injection vulnerabilities. Use when the user says 'security review', 'audit security', 'check vulnerabilities', 'review seguridad', before deploying to production, or after implementing auth/payment/data-handling features."
allowed-tools: Read, Bash, Glob, Grep
model: opus
effort: high
---

# Security Review

Audita cambios de codigo contra vulnerabilidades comunes. Ejecuta ANTES de deploy a produccion.

## Paso 1: Identificar Alcance

Determina que archivos cambiaron recientemente:
```bash
git diff --name-only HEAD~5
git diff --staged --name-only
```

Lee cada archivo modificado. Prioriza:
- API routes (`src/app/api/`)
- Middleware (`middleware.ts`)
- Services (`src/**/services/`)
- Auth-related files
- Database queries

## Paso 2: Checklist OWASP Top 10

Para CADA archivo, verifica:

### A01: Broken Access Control
- [ ] Auth verificado en cada API route (`supabase.auth.getUser()`)
- [ ] RLS habilitado en tablas accedidas
- [ ] Permisos verificados (no solo autenticacion)
- [ ] No hay IDOR (acceso a recursos de otros usuarios via ID)

### A02: Cryptographic Failures
- [ ] Secrets en `.env.local`, nunca hardcoded
- [ ] `NEXT_PUBLIC_` solo para valores verdaderamente publicos
- [ ] No se loggean tokens, passwords, o PII

### A03: Injection
- [ ] Queries parametrizadas (Supabase lo maneja, pero verificar raw SQL)
- [ ] No hay `dangerouslySetInnerHTML` con input de usuario
- [ ] No hay `eval()`, `new Function()`, o template literals en queries

### A04: Insecure Design
- [ ] Rate limiting en endpoints de auth
- [ ] Validacion server-side con Zod (no confiar en client)
- [ ] Mensajes de error genericos al cliente (no stack traces)

### A05: Security Misconfiguration
- [ ] Headers de seguridad en `next.config.js` (CSP, HSTS)
- [ ] CORS configurado correctamente
- [ ] No hay debug mode expuesto

### A07: XSS
- [ ] React escapa por defecto — buscar bypass (`dangerouslySetInnerHTML`, `href="javascript:"`)
- [ ] Sanitizar URLs de usuario antes de `<a href={}>`
- [ ] CSP header configurado

### A08: Software & Data Integrity
- [ ] `package-lock.json` committado (integridad de deps)
- [ ] No hay `npm install` de paquetes desconocidos sin verificar

### A09: Logging & Monitoring
- [ ] Acciones sensibles se loggean (login, cambios de permisos)
- [ ] No se loggean datos sensibles (passwords, tokens, credit cards)

## Paso 3: Busqueda Automatizada

Ejecuta estas busquedas en el codebase:

```bash
# Secrets hardcoded
grep -rn "password\|secret\|api_key\|token" src/ --include="*.ts" --include="*.tsx" | grep -v "node_modules\|.test.\|type\|interface"

# Funciones peligrosas
grep -rn "eval(\|dangerouslySetInnerHTML\|innerHTML\|document.write" src/

# SQL crudo
grep -rn "raw\|sql\`\|query(" src/ --include="*.ts"

# Console.log con datos potencialmente sensibles
grep -rn "console.log.*user\|console.log.*token\|console.log.*password" src/
```

## Paso 4: Reporte

Genera un reporte estructurado:

```markdown
## Security Review Report

**Fecha**: [fecha]
**Archivos revisados**: [N]
**Hallazgos**: [criticos/altos/medios/bajos]

### Criticos (bloquean deploy)
- [Descripcion + archivo:linea + fix recomendado]

### Altos (resolver antes de deploy)
- [Descripcion + archivo:linea + fix recomendado]

### Medios (resolver esta semana)
- [Descripcion + archivo:linea + fix recomendado]

### Bajos (backlog)
- [Descripcion + archivo:linea + fix recomendado]

### Aprobado: [SI/NO]
```

Si hay hallazgos criticos o altos, NO aprobar el deploy. Ofrece implementar los fixes.
