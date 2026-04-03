# Ejemplos Detallados del Bucle Agentico

## Ejemplo 1: Ingenieria Inversa

**Problema:** "Implementar autenticacion OAuth"

```
↓ Ingenieria Inversa:
- ¿Provider? (Google/GitHub/Custom)
- ¿Flujo? (Authorization Code/Implicit)
- ¿Storage? (Cookie/LocalStorage/Session)
- ¿Backend framework? (FastAPI/Express)
- ¿Frontend framework? (React/Vue)
- ¿Manejo de refresh tokens?
- ¿Logout seguro?
```

## Ejemplo 2: Plan Completo

**Problema:** "Sistema de notificaciones en tiempo real"

**Ingenieria Inversa:**
```
- WebSocket server (Socket.io/Pusher/custom)
- Backend: endpoints para enviar notificaciones
- Frontend: componente NotificationBell
- Base de datos: tabla notifications
- Sistema de permisos (¿quien puede notificar a quien?)
- Persistencia (¿guardar notificaciones?)
- Estado leido/no leido
```

**TodoTask Generado:**
```
✅ Setup infraestructura WebSocket
   ✅ Instalar dependencias (socket.io)
   ✅ Configurar servidor WebSocket
   ✅ Configurar CORS y autenticacion
🔄 Backend: API de notificaciones
   ✅ Modelo Notification (SQLModel)
   ✅ Endpoint POST /notifications/send
   🔄 Endpoint GET /notifications (listar)
   ⏳ Endpoint PATCH /notifications/:id/read
⏳ Frontend: UI de notificaciones
   ⏳ Componente NotificationBell
   ⏳ Hook useNotifications
   ⏳ Integracion con WebSocket client
⏳ Testing & Validacion
   ⏳ Tests unitarios backend
   ⏳ Tests E2E (enviar → recibir)
   ⏳ Validar casos edge (offline, reconexion)
```

**Progreso:** 40% (4/10 subtareas completadas)

## Ventajas del Bucle Agentico

1. **Visibilidad**: User ve progreso en tiempo real
2. **Recuperabilidad**: Si falla, sabes exactamente donde
3. **Calidad**: Validacion en cada paso previene cascadas de errores
4. **Aprendizaje**: El plan es documentacion viva del proceso
