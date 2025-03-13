# Monitoreo en Zabbix

Esta guía explica detalladamente cómo agregar y configurar monitoreo en Zabbix, incluyendo la creación de ítems, triggers y alertas.

## 1. Agregar un Nuevo Host
Para comenzar a monitorear un servidor o dispositivo, primero debes agregarlo a Zabbix.

### Pasos para agregar un host:
1. Ir a `Configuration > Hosts`.
2. Hacer clic en `Create Host`.
3. Configurar los siguientes campos:
   - **Host name**: Nombre del dispositivo (Ejemplo: `Servidor-Web`).
   - **Groups**: Seleccionar un grupo (Ejemplo: `Linux servers`).
   - **Interfaces**: Añadir la dirección IP o el dominio del host.
4. Guardar los cambios.

## 2. Creación de Ítems de Monitoreo
Los ítems son las métricas que queremos medir, como CPU, memoria, disco o logs.

### Pasos para crear un ítem:
1. Ir a `Configuration > Hosts`.
2. Seleccionar el host y hacer clic en `Items`.
3. Hacer clic en `Create Item`.
4. Configurar:
   - **Name**: Nombre del ítem (Ejemplo: `Uso de CPU`).
   - **Type**: `Zabbix agent` (para monitoreo con agente).
   - **Key**: Parámetro específico (Ejemplo: `system.cpu.load`).
   - **Update interval**: Intervalo de actualización en segundos.
   - **History storage period**: Cuánto tiempo se guardarán los datos.
5. Guardar los cambios y habilitar el ítem.

## 3. Creación de Triggers
Los triggers son reglas que determinan cuándo se debe generar una alerta.

### Pasos para crear un trigger:
1. Ir a `Configuration > Hosts > Triggers`.
2. Hacer clic en `Create Trigger`.
3. Configurar:
   - **Name**: Nombre del trigger (Ejemplo: `Alta carga de CPU`).
   - **Expression**: Condición que activa la alerta. Ejemplo:
     ```
     {Servidor-Web:system.cpu.load.avg(5m)}>2
     ```
     Esto significa que si el uso de CPU promedio en 5 minutos es mayor a 2, se activará la alerta.
   - **Severity**: Nivel de severidad (Ejemplo: Warning, High, Disaster).
4. Guardar y aplicar cambios.

## 4. Configuración de Alertas
Las alertas notifican a los administradores cuando un trigger se activa.

### Pasos para configurar alertas por correo electrónico:
1. Ir a `Administration > Media Types`.
2. Crear un nuevo `Email` y configurar los detalles SMTP.
3. Ir a `Administration > Users` y asignar un email a los administradores.
4. Ir a `Configuration > Actions` y crear una nueva acción:
   - **Action Name**: `Alerta CPU Alta`.
   - **Trigger condition**: Seleccionar el trigger creado.
   - **Operations**: Seleccionar `Send message to users`.
5. Guardar y habilitar la alerta.

## 5. Visualización de Datos
Para ver los datos en tiempo real:
1. Ir a `Monitoring > Latest Data`.
2. Seleccionar el host y el ítem deseado.
3. Ver los valores recolectados y graficarlos.

Con esta configuración, cualquier usuario podrá agregar hosts, crear triggers y configurar alertas en Zabbix. 🚀

