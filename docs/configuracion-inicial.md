# Configuración Inicial de Zabbix

Esta guía explica los pasos iniciales para configurar Zabbix después de la instalación.

## 1. Configurar la Base de Datos
Dependiendo de la base de datos elegida, asegúrate de que Zabbix está correctamente configurado.

### MySQL/MariaDB
```bash
mysql -u root -p
CREATE DATABASE zabbix CHARACTER SET utf8 COLLATE utf8_bin;
CREATE USER 'zabbix'@'localhost' IDENTIFIED BY 'password';
GRANT ALL PRIVILEGES ON zabbix.* TO 'zabbix'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

### PostgreSQL
```bash
sudo -u postgres createuser --pwprompt zabbix
sudo -u postgres createdb -O zabbix zabbix
```

## 2. Configuración del Servidor Zabbix
Editar el archivo de configuración de Zabbix Server:
```bash
sudo nano /etc/zabbix/zabbix_server.conf
```
- Configurar la conexión a la base de datos:
  **Para MySQL/MariaDB:**
  ```
  DBHost=localhost
  DBName=zabbix
  DBUser=zabbix
  DBPassword=password
  ```
  **Para PostgreSQL:**
  ```
  DBHost=localhost
  DBName=zabbix
  DBUser=zabbix
  DBPassword=password
  ```

## 3. Configuración del Frontend Web
Para acceder a la interfaz web, asegúrate de que el servidor web está configurado correctamente.

### Configuración con Apache
```bash
sudo nano /etc/zabbix/apache.conf
```
Verifica que la configuración de PHP tenga los valores correctos:
```bash
php_value max_execution_time 300
php_value memory_limit 128M
php_value post_max_size 16M
php_value upload_max_filesize 2M
php_value max_input_time 300
php_value date.timezone "Europe/Madrid"
```
Reinicia Apache:
```bash
sudo systemctl restart apache2
```

### Configuración con Nginx
```bash
sudo nano /etc/nginx/sites-available/zabbix
```
Añadir la siguiente configuración:
```
server {
    listen 80;
    server_name zabbix.tudominio.com;
    root /usr/share/zabbix;

    index index.php;
    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/run/php/php7.4-fpm.sock;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }
}
```
Reiniciar Nginx:
```bash
sudo systemctl restart nginx
```

## 4. Iniciar los Servicios de Zabbix
```bash
sudo systemctl enable zabbix-server zabbix-agent
sudo systemctl restart zabbix-server zabbix-agent
```

## 5. Acceso a la Interfaz Web
Abre un navegador y accede a `http://IP_SERVIDOR/zabbix`. Sigue el asistente de instalación y usa los siguientes valores por defecto:
- **Usuario:** Admin
- **Contraseña:** zabbix

## 6. Configurar Idioma y Zona Horaria
Una vez dentro de la interfaz, ve a `Administration > General` y cambia el idioma a español y la zona horaria adecuada.

---

Después de completar estos pasos, Zabbix estará listo para monitorear dispositivos y servidores. 🚀

