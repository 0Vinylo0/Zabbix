# Configuración de Alertas en Zabbix

Las alertas en Zabbix permiten notificar a los administradores cuando un evento crítico ocurre en la infraestructura monitoreada. Se pueden enviar alertas por email, Telegram, Slack, Discord, SMS, entre otros.

## 1. Configuración de Media Types (Tipos de Notificación)
Antes de configurar alertas, se deben definir los medios por los cuales se enviarán las notificaciones.

### Configurar Notificaciones por Correo Electrónico
1. Ir a `Administration > Media Types`.
2. Hacer clic en `Create Media Type`.
3. Configurar:
   - **Name**: `Correo Electrónico`
   - **Type**: `Email`
   - **SMTP Server**: Servidor SMTP de tu proveedor (Ejemplo: `smtp.gmail.com`).
   - **SMTP Port**: `587` (TLS) o `465` (SSL).
   - **SMTP Username**: Tu dirección de correo electrónico.
   - **SMTP Password**: Contraseña o token de acceso.
4. Guardar y aplicar cambios.

### Configurar Notificaciones por Telegram
1. Crear un bot en Telegram a través de `@BotFather` y obtener el token.
2. Ir a `Administration > Media Types`.
3. Crear un nuevo `Media Type` con:
   - **Name**: `Telegram`
   - **Type**: `Script`
   - **Script name**: `telegram.sh`
   - **Parameters**:
     - `{ALERT.SENDTO}` → ID del chat de Telegram.
     - `{ALERT.MESSAGE}` → Mensaje de alerta.
4. Guardar cambios y probar enviando un mensaje.

## 2. Configuración de Usuarios y Medios de Notificación
Cada usuario en Zabbix debe tener configurado un medio de notificación.

1. Ir a `Administration > Users`.
2. Seleccionar el usuario que recibirá alertas.
3. En la pestaña `Media`, hacer clic en `Add`.
4. Seleccionar el `Media Type` configurado anteriormente (Correo, Telegram, etc.).
5. Ingresar los detalles (Ejemplo: correo electrónico, ID de chat de Telegram).
6. Guardar cambios.

## 3. Creación de Acciones para Alertas
Las acciones definen qué sucede cuando un trigger se activa.

1. Ir a `Configuration > Actions`.
2. Hacer clic en `Create Action`.
3. Configurar:
   - **Name**: `Alerta de CPU Alta`.
   - **Conditions**: `Trigger = CPU Usage > 90%`.
   - **Operations**:
     - **Send Message to Users**: Seleccionar los usuarios y medios de notificación.
   - **Recovery Operations** (Opcional): Enviar una notificación cuando el problema se resuelva.
4. Guardar y activar la acción.

## 4. Prueba y Verificación de Alertas
Para comprobar que las alertas funcionan correctamente:
1. Ir a `Monitoring > Problems`.
2. Simular una condición de alerta (Ejemplo: elevar el uso de CPU manualmente).
3. Verificar si la alerta se dispara y si el usuario recibe la notificación.

Con esta configuración, Zabbix notificará a los administradores de eventos críticos, asegurando una respuesta rápida ante fallos. 🚀

