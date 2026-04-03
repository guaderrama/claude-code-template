# MCP (Model Context Protocol) Guide

> Como configurar y usar servidores MCP con Claude Code.

---

## Que es MCP

MCP permite que Claude Code se conecte a herramientas externas (bases de datos, APIs, servicios) a traves de un protocolo estandarizado. Los servidores MCP extienden las capacidades de Claude sin necesidad de skills custom.

---

## Configuracion

Los servidores MCP se configuran en `.claude/settings.local.json`:

```json
{
  "mcpServers": {
    "server-name": {
      "command": "npx",
      "args": ["-y", "@package/mcp-server"],
      "env": {
        "API_KEY": "your-key"
      }
    }
  }
}
```

---

## Servidores MCP Recomendados

### shadcn/ui (componentes)
```json
{
  "mcpServers": {
    "shadcn": {
      "command": "npx",
      "args": ["-y", "@anthropic-ai/shadcn-mcp-server"]
    }
  }
}
```
**Uso**: Claude puede instalar y configurar componentes shadcn/ui automaticamente.

### Playwright (testing visual)
```json
{
  "mcpServers": {
    "playwright": {
      "command": "npx",
      "args": ["-y", "@anthropic-ai/playwright-mcp-server"]
    }
  }
}
```
**Uso**: Captura screenshots, navega paginas, inspecciona DOM, debug visual.

### Supabase (base de datos)
```json
{
  "mcpServers": {
    "supabase": {
      "command": "npx",
      "args": ["-y", "@supabase/mcp-server"],
      "env": {
        "SUPABASE_URL": "your-url",
        "SUPABASE_SERVICE_KEY": "your-key"
      }
    }
  }
}
```
**Uso**: Queries directas, schema inspection, migrations desde Claude.

### Filesystem (acceso a archivos fuera del proyecto)
```json
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": ["-y", "@anthropic-ai/filesystem-mcp-server", "/allowed/path"]
    }
  }
}
```

---

## Verificar MCP Servers Activos

En Claude Code:
- Los servidores configurados se conectan automaticamente al iniciar
- Si un servidor falla, Claude lo reporta en la respuesta
- Usa `/config` para verificar configuracion actual

---

## Crear un MCP Server Custom

Para servicios internos o APIs propias:

1. Implementar el protocolo MCP (JSON-RPC sobre stdio)
2. Exponer tools con schemas JSON
3. Registrar en settings.local.json
4. Claude descubre las tools automaticamente

Referencia: https://modelcontextprotocol.io/

---

## Tips

- **Seguridad**: Nunca committear `settings.local.json` con API keys — esta en `.gitignore`
- **Performance**: Cada MCP server es un proceso — solo habilitar los que uses
- **Debugging**: Si un MCP no responde, verificar que el paquete npm existe y el comando es correcto
- **Scope**: MCP servers son por proyecto (settings.local.json) o globales (~/.claude/settings.json)
