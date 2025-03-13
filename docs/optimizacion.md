# Optimización de Zabbix

Esta guía explica cómo mejorar el rendimiento y la eficiencia de Zabbix, optimizando la base de datos, la recolección de datos y la configuración del servidor.

## 1. Configuración de Housekeeping para eliminar datos antiguos
Housekeeping es el proceso automático de limpieza de datos antiguos en Zabbix.

### Configuración:
1. Ir a `Administration > General > Housekeeping`.
2. Activar la opción `Enable Housekeeping`.
3. Configurar:
   - **Trigger data storage period**: Días de retención de eventos de triggers.
   - **Trend storage period**: Días de retención de tendencias de datos históricos.
   - **Event storage period**: Días de retención de eventos detallados.
4. Guardar cambios y monitorear el tamaño de la base de datos.

## 2. Optimización de la Base de Datos
Zabbix almacena grandes volúmenes de datos, por lo que optimizar la base de datos mejora el rendimiento.

### Ajuste de Caché en `zabbix_server.conf`
1. Editar la configuración:
   ```bash
   sudo nano /etc/zabbix/zabbix_server.conf
   ```
2. Ajustar los siguientes parámetros:
   ```
   CacheSize=512M  # Tamaño del caché de la base de datos
   TrendCacheSize=256M  # Tamaño del caché de tendencias
   HistoryIndexCacheSize=128M  # Caché del índice de historial
   ```
3. Reiniciar el servidor Zabbix:
   ```bash
   sudo systemctl restart zabbix-server
   ```

### Optimización en MySQL/MariaDB
1. Ejecutar la siguiente consulta para optimizar tablas:
   ```sql
   OPTIMIZE TABLE trends;
   OPTIMIZE TABLE history;
   OPTIMIZE TABLE events;
   ```
2. Ajustar los buffers en `my.cnf`:
   ```ini
   innodb_buffer_pool_size=1G
   innodb_log_file_size=256M
   ```
3. Reiniciar MySQL:
   ```bash
   sudo systemctl restart mysql
   ```

## 3. Configuración de Proxies para Mejorar la Distribución de Carga
Los proxies permiten reducir la carga en el servidor principal distribuyendo la recolección de datos.

### Instalación de Zabbix Proxy en Debian/Ubuntu
```bash
sudo apt install -y zabbix-proxy-mysql
```

### Configuración del Proxy
1. Editar el archivo de configuración:
   ```bash
   sudo nano /etc/zabbix/zabbix_proxy.conf
   ```
2. Configurar los siguientes parámetros:
   ```
   Server=<IP_SERVIDOR_ZABBIX>
   Hostname=ZabbixProxy
   DBName=zabbix_proxy
   DBUser=zabbix
   ```
3. Reiniciar el proxy:
   ```bash
   sudo systemctl restart zabbix-proxy
   ```

## 4. Ajuste de la Frecuencia de Chequeos para Evitar Sobrecarga
Reducir la frecuencia de chequeos innecesarios ayuda a optimizar el rendimiento.

### Pasos para optimizar los chequeos:
1. Ir a `Configuration > Hosts`.
2. Seleccionar un host y editar los ítems de monitoreo.
3. Ajustar el valor de `Update interval` (Ejemplo: cambiar de `30s` a `60s`).
4. Revisar los `Discovery rules` y `Active checks` y reducir la cantidad de chequeos si es posible.
5. Aplicar los cambios y monitorear la carga en `Monitoring > Performance`.

Con estas optimizaciones, Zabbix funcionará de manera más eficiente y escalará mejor en entornos con muchos dispositivos. 🚀

