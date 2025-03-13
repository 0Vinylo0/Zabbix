# Instalación de Zabbix en un Servidor Separado del Monitoreo

Esta guía explica cómo instalar **Zabbix Server** en un servidor central y **Zabbix Agent** en servidores remotos que deseas monitorear. Además, incluye la instalación de Zabbix en un contenedor Docker.

---

## 🖥️ 1. Instalación del Servidor Zabbix (Servidor Central)

Este será el servidor que ejecutará **Zabbix Server** y la interfaz web (Frontend).

### 🔹 Instalación del Servidor en Debian/Ubuntu
```bash
sudo apt update
sudo apt install -y zabbix-server-mysql zabbix-frontend-php zabbix-apache-conf zabbix-agent
```

### 🔹 Configuración de la Base de Datos
1. Iniciar sesión en MySQL:
   ```bash
   mysql -u root -p
   ```
2. Crear la base de datos y el usuario para Zabbix:
   ```sql
   CREATE DATABASE zabbix CHARACTER SET utf8mb4 COLLATE utf8mb4_bin;
   CREATE USER 'zabbix'@'localhost' IDENTIFIED BY 'password';
   GRANT ALL PRIVILEGES ON zabbix.* TO 'zabbix'@'localhost';
   FLUSH PRIVILEGES;
   EXIT;
   ```
3. Importar la estructura de la base de datos:
   ```bash
   zcat /usr/share/zabbix-sql-scripts/mysql/server.sql.gz | mysql -u zabbix -p zabbix
   ```

### 🔹 Configuración de Zabbix Server
Editar el archivo de configuración de Zabbix:
```bash
sudo nano /etc/zabbix/zabbix_server.conf
```
Modificar las siguientes líneas:
```
DBHost=localhost
DBName=zabbix
DBUser=zabbix
DBPassword=password
```

### 🔹 Configuración de PHP para el Frontend
```bash
sudo nano /etc/zabbix/apache.conf
```
Modificar la zona horaria:
```
php_value date.timezone Europe/Madrid
```

Reiniciar servicios:
```bash
sudo systemctl restart zabbix-server apache2
sudo systemctl enable zabbix-server apache2
```

### 🔹 Acceder al Panel Web
Abrir un navegador y entrar en:
```
http://IP_DEL_SERVIDOR/zabbix
```
Seguir el asistente de instalación.

---

## 🖥️ 2. Instalación del Agente Zabbix en el Servidor a Monitorear

Este será el **servidor remoto** que queremos monitorear.

### 🔹 Instalación del Agente en Debian/Ubuntu
```bash
sudo apt update
sudo apt install -y zabbix-agent
```

### 🔹 Configuración del Agente
Editar el archivo de configuración:
```bash
sudo nano /etc/zabbix/zabbix_agentd.conf
```
Modificar las siguientes líneas:
```
Server=IP_DEL_SERVIDOR_ZABBIX
ServerActive=IP_DEL_SERVIDOR_ZABBIX
Hostname=Servidor-Monitoreado
```

Guardar y cerrar.

### 🔹 Reiniciar y Habilitar el Agente
```bash
sudo systemctl restart zabbix-agent
sudo systemctl enable zabbix-agent
```

### 🔹 Verificar la Comunicación con el Servidor
Desde el servidor Zabbix, ejecutar:
```bash
zabbix_get -s IP_DEL_SERVIDOR_MONITOREADO -k system.cpu.load
```
Si responde con un valor, la comunicación es correcta.

---

## 🐳 3. Instalación de Zabbix en un Contenedor Docker

Si deseas ejecutar Zabbix Server en un contenedor, puedes usar Docker y Docker Compose. Para garantizar que los agentes Zabbix en servidores remotos puedan comunicarse con el servidor Zabbix en contenedor, sigue estos pasos.

Si deseas ejecutar Zabbix Server en un contenedor, puedes usar Docker y Docker Compose.

### 🔹 Instalación de Docker y Docker Compose
```bash
sudo apt update
sudo apt install -y docker.io docker-compose
```

### 🔹 Crear un archivo `docker-compose.yml`
```yaml
version: '3.5'
services:
  zabbix-db:
    image: mysql:5.7
    container_name: zabbix-db
    restart: always
    environment:
      MYSQL_DATABASE: zabbix
      MYSQL_USER: zabbix
      MYSQL_PASSWORD: password
      MYSQL_ROOT_PASSWORD: rootpassword
    volumes:
      - zabbix-db-data:/var/lib/mysql
    networks:
      - zabbix-net

  zabbix-server:
    image: zabbix/zabbix-server-mysql
    container_name: zabbix-server
    restart: always
    environment:
      DB_SERVER_HOST: zabbix-db
      MYSQL_DATABASE: zabbix
      MYSQL_USER: zabbix
      MYSQL_PASSWORD: password
    depends_on:
      - zabbix-db
    ports:
      - "10051:10051"
    networks:
      - zabbix-net

  zabbix-web:
    image: zabbix/zabbix-web-apache-mysql
    container_name: zabbix-web
    restart: always
    environment:
      DB_SERVER_HOST: zabbix-db
      MYSQL_DATABASE: zabbix
      MYSQL_USER: zabbix
      MYSQL_PASSWORD: password
      ZBX_SERVER_HOST: zabbix-server
    depends_on:
      - zabbix-server
    ports:
      - "8080:8080"
    networks:
      - zabbix-net

volumes:
  zabbix-db-data:

networks:
  zabbix-net:
    driver: bridge
```

### 🔹 Levantar los Contenedores
Ejecutar:
```bash
sudo docker-compose up -d
```

### 🔹 Acceder a la Interfaz Web
Abrir en un navegador:
```
http://IP_DEL_SERVIDOR:8080
```
Usar credenciales por defecto:
- **Usuario:** Admin
- **Contraseña:** zabbix

---

## 🔹 Configurar la Conexión de los Agentes con el Servidor Zabbix en Docker

Si ejecutas Zabbix Server en un contenedor, los agentes en los servidores monitoreados deben apuntar a la **IP del contenedor Zabbix Server** o a la IP del host que ejecuta Docker.

### 🔹 Obtener la IP del Contenedor Zabbix Server
Ejecutar el siguiente comando en el servidor donde se ejecuta Docker:
```bash
docker inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' zabbix-server
```
Si usas Docker Compose:
```bash
docker network inspect zabbix-net
```

### 🔹 Configurar el Agente para Apuntar a la IP del Contenedor
En cada **servidor monitoreado**, edita el archivo de configuración del agente:
```bash
sudo nano /etc/zabbix/zabbix_agentd.conf
```
Modificar estas líneas:
```
Server=IP_DEL_CONTENEDOR_ZABBIX
ServerActive=IP_DEL_CONTENEDOR_ZABBIX
```

Guardar los cambios y reiniciar el agente:
```bash
sudo systemctl restart zabbix-agent
```

### 🔹 Verificar la Comunicación
Desde el servidor monitoreado, probar si el servidor Zabbix responde:
```bash
zabbix_get -s IP_DEL_CONTENEDOR_ZABBIX -k system.cpu.load
```
Si devuelve un valor, la conexión es exitosa.

### 🔹 Exponer el Servidor Zabbix a la Red Local
Si prefieres que los agentes se conecten a la **IP del host** en lugar de la del contenedor, edita `docker-compose.yml` y expón el puerto 10051:
```yaml
ports:
  - "10051:10051"
```
Luego, reinicia los contenedores:
```bash
sudo docker-compose down
sudo docker-compose up -d
```
Configura los agentes para apuntar a la **IP del host** en lugar de la del contenedor.

## 🎯 4. Agregar el Servidor Monitoreado en la Interfaz Web

1. Ingresar a `http://IP_DEL_SERVIDOR/zabbix`.
2. Ir a `Configuration > Hosts`.
3. Hacer clic en `Create Host`.
4. Configurar:
   - **Host name**: `Servidor-Monitoreado`.
   - **IP Address**: `IP_DEL_SERVIDOR_MONITOREADO`.
   - **Templates**: Seleccionar `Template OS Linux` o el que corresponda.
5. Guardar y verificar en `Monitoring > Latest Data`.

---

## ✅ Resultado Final
✔️ Zabbix Server estará instalado en un **servidor separado** o en un **contenedor Docker**.
✔️ Los servidores monitoreados solo necesitan **Zabbix Agent**.
✔️ Se podrán ver métricas en la interfaz web y configurar alertas.

🚀 **Con esta configuración, puedes monitorear múltiples servidores desde un solo servidor Zabbix o en contenedores Docker!**

