# Solución de Problemas en Zabbix (Troubleshooting)

Esta guía cubre problemas comunes en Zabbix y sus soluciones.

## 1. Errores de Conexión con la Base de Datos
### Problema: `Cannot connect to database` en la interfaz web
**Causa:** Las credenciales de la base de datos son incorrectas o el servicio no está en ejecución.
**Solución:**
1. Verificar que la base de datos está activa:
   ```bash
   sudo systemctl status mysql  # Para MySQL/MariaDB
   sudo systemctl status postgresql  # Para PostgreSQL
   ```
2. Comprobar las credenciales en `/etc/zabbix/zabbix_server.conf`:
   ```bash
   DBHost=localhost
   DBName=zabbix
   DBUser=zabbix
   DBPassword=tu_contraseña
   ```
3. Reiniciar el servicio de Zabbix Server:
   ```bash
   sudo systemctl restart zabbix-server
   ```

## 2. La Interfaz Web No Carga o Muestra Errores PHP
### Problema: Página en blanco o error `500 Internal Server Error`
**Causa:** Faltan extensiones de PHP o hay un problema con la configuración del servidor web.
**Solución:**
1. Verificar que las extensiones necesarias están instaladas:
   ```bash
   sudo apt install -y php php-mbstring php-gd php-xml php-bcmath php-ldap
   ```
2. Reiniciar Apache o Nginx:
   ```bash
   sudo systemctl restart apache2  # Para Apache
   sudo systemctl restart nginx php-fpm  # Para Nginx
   ```

## 3. Agentes No Se Comunican con el Servidor
### Problema: `Zabbix agent unreachable`
**Causa:** El agente no está corriendo o hay un problema de red.
**Solución:**
1. Verificar si el agente está en ejecución:
   ```bash
   sudo systemctl status zabbix-agent
   ```
2. Comprobar conectividad desde el servidor:
   ```bash
   telnet <IP_DEL_AGENT> 10050  # Debe responder con una conexión establecida
   ```
3. Revisar el firewall y permitir conexiones al puerto 10050:
   ```bash
   sudo ufw allow 10050/tcp  # En sistemas con UFW
   sudo firewall-cmd --add-port=10050/tcp --permanent && sudo firewall-cmd --reload  # En CentOS/RHEL
   ```
4. Reiniciar el agente:
   ```bash
   sudo systemctl restart zabbix-agent
   ```

## 4. No Se Reciben Alertas
### Problema: Zabbix no envía notificaciones
**Causa:** El medio de notificación (email, Telegram, etc.) no está configurado correctamente.
**Solución:**
1. Ir a `Administration > Media Types` y probar el envío de una notificación.
2. Revisar los logs de Zabbix Server en `/var/log/zabbix/zabbix_server.log` para ver errores relacionados con notificaciones.
3. Verificar la configuración SMTP si se usa correo electrónico.

## 5. Alta Carga en la Base de Datos
### Problema: Zabbix consume demasiados recursos
**Causa:** Exceso de datos almacenados o consultas pesadas.
**Solución:**
1. Configurar Housekeeping (`Administration > General > Housekeeping`).
2. Optimizar tablas en MySQL/MariaDB:
   ```sql
   OPTIMIZE TABLE history;
   OPTIMIZE TABLE trends;
   ```
3. Ajustar caché en `zabbix_server.conf`:
   ```
   CacheSize=512M
   TrendCacheSize=256M
   ```

## 6. Errores en Logs
Los logs de Zabbix pueden ayudar a diagnosticar problemas.
- Logs principales:
  ```bash
  sudo tail -f /var/log/zabbix/zabbix_server.log  # Logs del servidor
  sudo tail -f /var/log/zabbix/zabbix_agentd.log  # Logs del agente
  ```
- Buscar errores específicos con:
  ```bash
  grep "error" /var/log/zabbix/zabbix_server.log
  ```

---

Si el problema persiste, revisa la [documentación oficial de Zabbix](https://www.zabbix.com/documentation/current/). 🚀

