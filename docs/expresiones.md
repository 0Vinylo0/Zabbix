# Expresiones en Zabbix

Las expresiones en Zabbix permiten definir condiciones lógicas para activar triggers y alertas. Son fundamentales para automatizar el monitoreo y responder a eventos en tiempo real.

## 1. Estructura Básica de una Expresión

Una expresión en Zabbix sigue esta sintaxis:

```plaintext
{HOST:ITEM.FUNCTION(PARAMETROS)}OPERADORVALOR
```
- **HOST**: Nombre del host al que pertenece el ítem.
- **ITEM**: Clave del ítem que se está evaluando.
- **FUNCTION**: Función que procesa los datos.
- **PARAMETROS**: Argumentos de la función (si los requiere).
- **OPERADOR**: Operador lógico de comparación.
- **VALOR**: Umbral de referencia para la comparación.

## 2. Ejemplos de Expresiones y su Significado

### Ejemplo 1: Alta Carga de CPU
```plaintext
{Servidor-Web:system.cpu.load.avg(5m)}>2
```
- **`Servidor-Web`**: Nombre del host.
- **`system.cpu.load.avg(5m)`**: Promedio de carga de CPU en 5 minutos.
- **`>`**: Operador de comparación (mayor que).
- **`2`**: Umbral de activación del trigger.

_**Significado:** Si la carga de CPU en los últimos 5 minutos supera 2, se activará el trigger._

### Ejemplo 2: Espacio en Disco Bajo
```plaintext
{Servidor-DB:vfs.fs.size[/,free].last()}<10737418240
```
- **`vfs.fs.size[/,free].last()`**: Último valor registrado del espacio libre en `/`.
- **`<10737418240`**: Se activa si el espacio libre es menor a 10GB (10×1024×1024×1024 bytes).

_**Significado:** Si el espacio libre en `/` es menor a 10GB, se activa la alerta._

### Ejemplo 3: Servidor No Responde
```plaintext
{Servidor-Web:agent.ping.nodata(300)}=1
```
- **`agent.ping.nodata(300)`**: No hay datos del ping en los últimos 300 segundos.
- **`=1`**: Se activa si no hay respuesta.

_**Significado:** Si Zabbix no recibe datos del agente en 5 minutos, se activa la alerta._

### Ejemplo 4: Uso de Memoria Excesivo
```plaintext
{Servidor-DB:vm.memory.size[available].last()}<104857600
```
- **`vm.memory.size[available].last()`**: Última medición de memoria disponible.
- **`<104857600`**: Se activa si hay menos de 100MB libres (100×1024×1024 bytes).

_**Significado:** Si la memoria disponible baja de 100MB, se activa la alerta._

## 3. Operadores en Expresiones

| Operador  | Significado |
|-----------|------------|
| `>`       | Mayor que  |
| `<`       | Menor que  |
| `=`       | Igual a    |
| `<>`      | Diferente de |
| `and`     | Y lógico   |
| `or`      | O lógico   |

## 4. Funciones Útiles en Expresiones

| Función | Descripción |
|---------|------------|
| `last()` | Último valor recibido del ítem |
| `avg(nm)` | Promedio de los últimos `nm` minutos |
| `min(nm)` | Valor mínimo en los últimos `nm` minutos |
| `max(nm)` | Valor máximo en los últimos `nm` minutos |
| `nodata(nm)` | No hay datos en los últimos `nm` segundos |

## 5. Ejemplo Complejo: Combinando Condiciones

```plaintext
({Servidor-Web:system.cpu.load.avg(5m)}>3 and {Servidor-Web:agent.ping.nodata(300)}=0)
```

_**Significado:** Si la carga de CPU en los últimos 5 minutos es mayor a 3 y el agente está respondiendo, se activa el trigger._

---

Con esta guía, cualquier usuario puede crear y entender expresiones en Zabbix para configurar triggers personalizados. 🚀

