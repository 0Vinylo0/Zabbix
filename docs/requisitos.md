# Requisitos del Sistema

Antes de instalar Zabbix, es importante asegurarse de que el sistema cumple con los siguientes requisitos.

## Requisitos del Servidor Zabbix

- **Sistema Operativo Compatible:**
  - Debian 10/11, Ubuntu 20.04/22.04
  - CentOS 7/8, RHEL 7/8
  - SUSE Linux Enterprise Server
  - Windows Server (para agentes)
- **Base de Datos Compatible:**
  - MySQL / MariaDB
  - PostgreSQL
  - SQLite (para entornos de prueba)
- **Web Server:**
  - Apache 2.4+
  - Nginx 1.18+
- **PHP:**
  - Versión mínima: 7.2+
  - Extensiones requeridas: GD, XML, BCMath, MBString, Sockets
- **Memoria RAM:**
  - Mínimo: 2 GB (recomendado 4 GB o más)
- **Espacio en Disco:**
  - Mínimo: 20 GB (depende de la cantidad de datos almacenados)
- **Otros:**
  - Net-SNMP (para monitoreo por SNMP)
  - Java Gateway (opcional, para monitoreo de Java)

## Requisitos del Agente Zabbix

- **Sistema Operativo:**
  - Linux, Windows, macOS, FreeBSD
- **Requisitos Mínimos de Hardware:**
  - CPU: 1 núcleo
  - RAM: 512 MB
  - Espacio en Disco: 500 MB
