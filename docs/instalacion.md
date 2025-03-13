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

2. Reiniciar servicios:
   ```bash
   sudo systemctl restart zabbix-server zabbix-agent
   ```

3. Acceder al panel web (`http://IP_SERVER/zabbix`), completar el asistente de instalación.

## Finalización

Una vez completada la instalación y configuración inicial, puedes iniciar sesión en la interfaz web y comenzar a monitorear tu infraestructura con Zabbix.

