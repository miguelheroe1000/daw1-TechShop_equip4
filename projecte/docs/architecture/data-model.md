Aquí tienes el contenido completo de tu archivo `data-model.md`, listo para copiar y guardar dentro de `proyecto/docs/architecture/`.

Escriptura

# Diseño del Modelo de Datos

## 1\. Introducción

El programa de gestión de la tienda online utiliza **vectores paralelos** para almacenar la información de clientes, productos y ventas.

Los vectores paralelos son varios arrays que guardan diferentes características de un mismo registro. Todos utilizan la misma posición o índice para relacionar sus datos.

En este proyecto no se utilizan clases propias ni objetos para guardar los registros. Cada característica se almacena en un vector diferente.

Cada grupo de vectores tiene una capacidad máxima de 50 elementos.

## 2\. ¿Cómo funcionan los vectores paralelos?

Por ejemplo, para guardar los clientes tenemos diferentes vectores:

- `clientIds`: guarda los identificadores.
- `clientNoms`: guarda los nombres.
- `clientEmails`: guarda los correos electrónicos.
- `clientTelefons`: guarda los teléfonos.
- `clientActius`: indica si los clientes están activos.

Todos los vectores utilizan el mismo índice para relacionar la información.

### Ejemplo de correspondencia por índice

| Índice | clientIds | clientNoms | clientEmails | clientTelefons | clientActius |
| --- | --- | --- | --- | --- | --- |
| 0 | 101 | Anna Garcia | anna@correu.cat | 611223344 | true |
| 1 | 102 | Bernat Lopez | bernat@correu.cat | 622334455 | true |
| 2 | 103 | Carla Vidal | carla@correu.cat | 633445566 | true |

Si queremos obtener la información de Anna, consultamos el índice 0 de todos los vectores.

java

```java
clientIds[0]       // 101
clientNoms[0]      // Anna Garcia
clientEmails[0]    // anna@correu.cat
clientTelefons[0]  // 611223344
clientActius[0]    // true
```

Todos estos valores pertenecen a Anna Garcia porque tienen la misma posición.

Los registros ocupan las posiciones desde el índice 0 hasta `count - 1`. La variable `count` indica cuántos registros hay almacenados.

## 3\. Modelo de datos: CLIENT (Clientes)

La entidad CLIENT almacena la información de las personas registradas en la tienda.

### Vectores utilizados

java

```java
int maxClients = 50;
int[] clientIds = new int[maxClients];
String[] clientNoms = new String[maxClients];
String[] clientEmails = new String[maxClients];
String[] clientTelefons = new String[maxClients];
boolean[] clientActius = new boolean[maxClients];
int clientCount = 0;
```

### Atributos y validaciones

| Atributo | Tipo | Descripción y validación |
| --- | --- | --- |
| clientIds | int (PK) | Identificador único. Empieza en 101 y sigue el criterio 101 + clientCount. |
| clientNoms | String | Nombre del cliente. Mínimo 3 caracteres. |
| clientEmails | String | Correo electrónico. Debe contener @ y un punto (.). |
| clientTelefons | String | Número de teléfono del cliente. |
| clientActius | boolean | Indica si el cliente está activo (true) o dado de baja (false). |

**Capacidad máxima:** 50 clientes (`maxClients = 50`).

### Baja lógica de clientes

Cuando se elimina un cliente, sus datos no desaparecen de los vectores. Simplemente se cambia su estado a `false`.

java

```java
clientActius[idx] = false;
```

Esto se llama **baja lógica**.

Se utiliza para conservar los datos del cliente y mantener el historial de sus ventas, aunque ya no esté activo.

## 4\. Modelo de datos: PRODUCTE (Productos)

La entidad PRODUCTE almacena los productos de la tienda y su información de inventario.

### Vectores utilizados

java

```java
int maxProductes = 50;
int[] producteIds = new int[maxProductes];
String[] producteNoms = new String[maxProductes];
String[] producteCategories = new String[maxProductes];
double[] productePreus = new double[maxProductes];
int[] producteEstoc = new int[maxProductes];
int producteCount = 0;
```

### Atributos y validaciones

| Atributo | Tipo | Descripción y validación |
| --- | --- | --- |
| producteIds | int (PK) | Identificador único. Empieza en 1. Para nuevos productos se utiliza Math.max(...) + 1. |
| producteNoms | String | Nombre del producto. Mínimo 2 caracteres. |
| producteCategories | String | Categoría a la que pertenece el producto. |
| productePreus | double | Precio del producto. Mayor que 0. |
| producteEstoc | int | Unidades disponibles. Mayor o igual que 0. |

**Capacidad máxima:** 50 productos (`maxProductes = 50`).

### Baja física de productos

Cuando se elimina un producto, sus datos desaparecen de los vectores.

Para hacerlo, se desplazan los elementos posteriores una posición hacia la izquierda.

java

```java
for (int i = idx; i < producteCount - 1; i++) {
    producteIds[i] = producteIds[i + 1];
    producteNoms[i] = producteNoms[i + 1];
    producteCategories[i] = producteCategories[i + 1];
    productePreus[i] = productePreus[i + 1];
    producteEstoc[i] = producteEstoc[i + 1];
}

producteCount--;
```

Esto se llama **baja física**, porque el registro se elimina realmente de los vectores.

Es importante desplazar todos los vectores para que los datos sigan correspondiéndose mediante el mismo índice.

## 5\. Modelo de datos: VENDA (Ventas)

La entidad VENDA almacena las operaciones de compra realizadas en la tienda.

Cada venta está relacionada con un cliente y un producto mediante sus identificadores.

### Vectores utilizados

java

```java
int maxVendes = 50;
int[] vendaIds = new int[maxVendes];
int[] vendaClientIds = new int[maxVendes];
int[] vendaProducteIds = new int[maxVendes];
int[] vendaQuantitats = new int[maxVendes];
double[] vendaTotals = new double[maxVendes];
String[] vendaDates = new String[maxVendes];
int vendaCount = 0;
```

### Atributos y validaciones

| Atributo | Tipo | Descripción y validación |
| --- | --- | --- |
| vendaIds | int (PK) | Identificador único de la venta. Empieza en 1001 y sigue el criterio 1001 + vendaCount. |
| vendaClientIds | int (FK) | Identificador del cliente que realiza la compra. |
| vendaProducteIds | int (FK) | Identificador del producto vendido. |
| vendaQuantitats | int | Cantidad de unidades vendidas. Debe ser positiva. |
| vendaTotals | double | Importe total de la venta. Precio por cantidad. |
| vendaDates | String | Fecha de la venta en formato AAAA-MM-DD. |

**Capacidad máxima:** 50 ventas (`maxVendes = 50`).

### Cálculo del importe total

El importe total de una venta se calcula multiplicando el precio del producto por la cantidad comprada.

java

```java
double total = productePreus[p] * quantitat;
```

No se aplican descuentos.

### Ventas inmutables

Las ventas son registros históricos que no deben modificarse ni eliminarse.

Esto permite conservar la información de las operaciones realizadas en la tienda.

## 6\. Relaciones entre las entidades

Las tres entidades están relacionadas mediante los identificadores.

- **CLIENT - VENDA:** un cliente puede realizar cero o muchas ventas. Cada venta pertenece a un único cliente. Se relacionan mediante `vendaClientIds`.
- **PRODUCTE - VENDA:** un producto puede aparecer en cero o muchas ventas. Cada venta corresponde a un único producto. Se relacionan mediante `vendaProducteIds`.

Los atributos `vendaClientIds` y `vendaProducteIds` funcionan como claves foráneas (FK).

## 7\. Diferencia entre baja lógica y baja física

| Característica | Baja lógica | Baja física |
| --- | --- | --- |
| Se utiliza en | Clientes | Productos |
| Se eliminan los datos | No | Sí |
| Se conserva el registro | Sí | No |
| Se modifica el estado | Sí | No |
| Se desplazan los elementos | No | Sí |
| Se reduce el contador | No | Sí |

- **Baja lógica:** el cliente sigue guardado, pero se marca como inactivo mediante `clientActius[idx] = false`.
- **Baja física:** el producto desaparece de los vectores y los elementos posteriores se desplazan una posición hacia la izquierda.

Las ventas se conservan como registros históricos inmutables.

## 8\. Resumen de las capacidades

| Entidad | Capacidad máxima | ID inicial | Contador |
| --- | --- | --- | --- |
| CLIENT | 50 | 101 | clientCount |
| PRODUCTE | 50 | 1 | producteCount |
| VENDA | 50 | 1001 | vendaCount |

## 9\. Conclusión

El programa utiliza vectores paralelos para organizar la información de clientes, productos y ventas.

Cada grupo de vectores comparte los mismos índices para relacionar los diferentes atributos de cada registro.

Los clientes utilizan baja lógica para conservar sus datos y el historial de ventas, mientras que los productos utilizan baja física para eliminar sus registros.

Por último, las ventas se conservan como registros históricos inmutables.

Para guardarlo: crea un archivo llamado exactamente `data-model.md` dentro de `proyecto/docs/architecture/` y pega el contenido.
