# Guía de Instalación de Zabbix

Esta guía detalla cómo instalar Zabbix en diferentes entornos, incluyendo bases de datos compatibles y servidores web.

## Instalación en Debian/Ubuntu

### Instalación del Servidor Zabbix con MySQL/MariaDB
```bash
sudo apt update
sudo apt install -y zabbix-server-mysql zabbix-frontend-php zabbix-agent
```

### Instalación del Servidor Zabbix con PostgreSQL
```bash
sudo apt update
sudo apt install -y zabbix-server-pgsql zabbix-frontend-php zabbix-agent
```

### Instalación del Frontend con Apache
```bash
sudo apt install -y apache2 libapache2-mod-php
```

### Instalación del Frontend con Nginx
```bash
sudo apt install -y nginx php-fpm
```

## Instalación en CentOS/RHEL

### Instalación del Servidor Zabbix con MySQL/MariaDB
```bash
sudo yum install -y zabbix-server-mysql zabbix-web-mysql zabbix-agent
```

### Instalación del Servidor Zabbix con PostgreSQL
```bash
sudo yum install -y zabbix-server-pgsql zabbix-web-pgsql zabbix-agent
```

### Instalación del Frontend con Apache
```bash
sudo yum install -y httpd php
```

### Instalación del Frontend con Nginx
```bash
sudo yum install -y nginx php-fpm
```

## Configuración de la Base de Datos

### Configuración en MySQL/MariaDB
```bash
mysql -u root -p
CREATE DATABASE zabbix CHARACTER SET utf8 COLLATE utf8_bin;
CREATE USER 'zabbix'@'localhost' IDENTIFIED BY 'password';
GRANT ALL PRIVILEGES ON zabbix.* TO 'zabbix'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

### Configuración en PostgreSQL
```bash
sudo -u postgres createuser --pwprompt zabbix
sudo -u postgres createdb -O zabbix zabbix
```

## Configuración del Servidor Zabbix

1. Editar el archivo de configuración de Zabbix Server:
   ```bash
   sudo nano /etc/zabbix/zabbix_server.conf
   ```
   - Configurar la base de datos según corresponda:
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

2. Reiniciar servicios:
   ```bash
   sudo systemctl restart zabbix-server zabbix-agent
   ```

3. Acceder al panel web (`http://IP_SERVER/zabbix`), completar el asistente de instalación.

## Finalización

Una vez completada la instalación y configuración inicial, puedes iniciar sesión en la interfaz web y comenzar a monitorear tu infraestructura con Zabbix.

