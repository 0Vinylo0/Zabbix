# Configuración Avanzada de Zabbix

Esta guía cubre configuraciones avanzadas para optimizar y ampliar las capacidades de Zabbix.

## 1. Descubrimiento Automático de Hosts

El descubrimiento automático permite agregar nuevos dispositivos a la red sin intervención manual.

### Configuración:

1. Ir a `Configuration > Discovery`
2. Crear una nueva regla de descubrimiento.
3. Definir el rango de IPs a escanear.
4. Seleccionar los métodos de detección (ICMP, SNMP, Zabbix Agent, etc.).
5. Configurar las acciones automáticas (agregar a grupos, asignar templates, etc.).

## 2. Configuración de Proxies en Zabbix

Los proxies permiten distribuir la carga de monitoreo en grandes infraestructuras.

### Instalación de Zabbix Proxy en Debian/Ubuntu

```bash
sudo apt install -y zabbix-proxy-mysql
```

### Instalación de Zabbix Proxy en CentOS/RHEL

```bash
sudo yum install -y zabbix-proxy-mysql
```

### Configuración del Proxy

Editar el archivo de configuración:

```bash
sudo nano /etc/zabbix/zabbix_proxy.conf
```

Ajustar los siguientes parámetros:

```
Server=<IP_DEL_SERVIDOR_ZABBIX>
Hostname=ZabbixProxy
DBName=zabbix_proxy
DBUser=zabbix
DBPassword=password
```

Reiniciar el servicio:

```bash
sudo systemctl restart zabbix-proxy
```

## 3. Configuración de Integración con Grafana

Grafana permite visualizar los datos de Zabbix con dashboards avanzados.

### Instalación de Grafana

```bash
sudo apt install -y grafana
```

### Configuración del Data Source

1. Acceder a `http://IP_SERVIDOR:3000` (usuario: `admin`, contraseña: `admin`).
2. Ir a `Configuration > Data Sources`.
3. Agregar un nuevo `Zabbix Data Source`.
4. Configurar la URL: `http://IP_SERVIDOR/zabbix/api_jsonrpc.php`.
5. Guardar y probar la conexión.

## 5. Configuración de Monitoreo de Logs

Zabbix permite la supervisión de archivos de registro en tiempo real, facilitando la detección de errores y anomalías en servidores y aplicaciones. Esto es útil para auditorías de seguridad, diagnósticos de fallos y detección temprana de problemas.

### Configuración en el Agente Zabbix

Para habilitar la supervisión de logs en un servidor con Zabbix Agent:

1. Editar la configuración del agente Zabbix:
   ```bash
   sudo nano /etc/zabbix/zabbix_agentd.conf
   ```
2. Agregar o modificar las siguientes líneas para definir el archivo de log a monitorear:
   ```
   LogFile=/var/log/zabbix_agentd.log
   LogFileSize=10
   ```
3. Para monitorear logs específicos, añadir la directiva `LogFile` en `zabbix_agentd.conf`:
   ```
   LogFile=/var/log/syslog
   ```
4. En el servidor Zabbix, crear un nuevo **ítem de monitoreo**:
   - Tipo: `Zabbix Agent (activo)`
   - Clave: `log[/var/log/syslog,Error]` (Filtra líneas que contengan "Error").
   - Intervalo de actualización: `30s`
   - Tipo de información: `Log`
   - Aplicar y guardar cambios.
5. Reiniciar el agente Zabbix para aplicar los cambios:
   ```bash
   sudo systemctl restart zabbix-agent
   ```

### Visualización y Alertas de Logs en Zabbix

1. Ir a `Monitoring > Latest Data`.
2. Seleccionar el host y el ítem de log.
3. Visualizar registros en tiempo real.
4. Configurar disparadores (`Triggers`) para alertas automáticas:
   - Ir a `Configuration > Triggers`.
   - Crear una nueva regla con la condición:
     ```
     {Host:log[/var/log/syslog,Error].nodata(300)}=0
     ```
   - Definir el nivel de severidad y acción de alerta.

Con esta configuración, Zabbix capturará y alertará sobre eventos críticos en logs del sistema o aplicaciones.

Zabbix puede monitorear logs del sistema y aplicaciones en tiempo real.

### Configuración en el Agente Zabbix

1. Editar la configuración del agente:
   ```bash
   sudo nano /etc/zabbix/zabbix_agentd.conf
   ```
2. Agregar la siguiente línea para monitorear logs:
   ```
   LogFile=/var/log/zabbix_agentd.log
   LogFileSize=10
   ```
3. Reiniciar el agente Zabbix:
   ```bash
   sudo systemctl restart zabbix-agent
   ```

## 6. Optimización de Rendimiento

### Configuración de Housekeeping para eliminar datos antiguos

Housekeeping es el mecanismo de Zabbix que limpia datos antiguos de la base de datos para evitar el crecimiento descontrolado.

1. Ir a `Administration > General > Housekeeping`.
2. Activar la opción `Enable Housekeeping`.
3. Configurar los valores:
   - `Trigger data storage period`: Tiempo en días para mantener los eventos de disparadores.
   - `Trend storage period`: Tiempo en días para mantener tendencias de datos históricos.
   - `Event storage period`: Tiempo en días para guardar eventos completos.
4. Guardar los cambios y monitorear el tamaño de la base de datos.

### Optimización de consultas SQL en zabbix\_server.conf

Para mejorar el rendimiento de la base de datos, es recomendable optimizar las consultas ajustando parámetros en `zabbix_server.conf`.

1. Editar el archivo de configuración:
   ```bash
   sudo nano /etc/zabbix/zabbix_server.conf
   ```
2. Modificar los siguientes parámetros:
   ```
   CacheSize=256M  # Ajustar el tamaño del caché de la base de datos
   TrendCacheSize=128M  # Ajustar el caché de tendencias
   HistoryIndexCacheSize=64M  # Optimizar el índice de historial
   ```
3. Reiniciar el servidor Zabbix para aplicar cambios:
   ```bash
   sudo systemctl restart zabbix-server
   ```

### Configuración de proxies para mejorar la distribución de carga

Los proxies permiten distribuir la carga de monitoreo en redes grandes y reducir el tráfico en el servidor principal.

1. Instalar Zabbix Proxy en un servidor remoto.
2. Configurar el archivo `zabbix_proxy.conf`:
   ```
   Server=<IP_DEL_SERVIDOR_ZABBIX>
   ProxyMode=1  # Modo activo
   DBName=zabbix_proxy
   DBUser=zabbix
   ```
3. Reiniciar el servicio proxy:
   ```bash
   sudo systemctl restart zabbix-proxy
   ```
4. Agregar el proxy en `Configuration > Proxies` dentro de la interfaz web de Zabbix.

### Ajuste de la frecuencia de chequeos para evitar sobrecarga

Reducir la frecuencia de chequeos innecesarios ayuda a disminuir la carga en el servidor.

1. Ir a `Configuration > Hosts`.
2. Seleccionar un host y ajustar la frecuencia de monitoreo (`Update interval` en segundos).
3. Revisar los `Discovery rules` y `Active checks` para optimizar el número de consultas.
4. Aplicar los cambios y verificar la carga en `Monitoring > Performance`.

---

Con estas configuraciones avanzadas, puedes mejorar el rendimiento y la escalabilidad de Zabbix. 🚀

