### 1) Mostrar las tablas de la base de datos Northwind

**Objetivo:** Verificar la conectividad y listar las tablas de la base de datos `northwind` para el trabajo de ingesta.

* **Comando:**
    ```bash
    # Para listar las tablas
    psql -h 172.17.0.3 -U postgres -d northwind
    \dt
    \q
    ```
* **Problema Común:** Intentar ejecutar el comando `\dt` directamente en la terminal sin estar dentro del cliente de `psql`.
* **Solución:** Primero se debe acceder al cliente de PostgreSQL (`psql`) y luego ejecutar los comandos internos.

![Mi imagen](../sqoop-ingestion/imagenes/image%201.png)
---

### 2) Mostrar los clientes de Argentina

**Objetivo:** Filtrar y visualizar los clientes de la tabla `Customers` que se encuentran en Argentina.

* **Comando:**
    ```bash
    # Para acceder a la base de datos
    psql -h 172.17.0.3 -U postgres -d northwind

    # Para consultar los clientes de Argentina
    SELECT * FROM Customers WHERE country = 'Argentina';

    # Para salir
    \q
    ```
* **Problemas Comunes:**
    * **Error de Shell:** Ejecutar `SELECT` directamente en la línea de comandos de Bash.
    * **Error de Case Sensitivity:** Utilizar `WHERE "Country" = 'Argentina'` en lugar de `WHERE country = 'Argentina'`. PostgreSQL maneja los nombres de columnas sin comillas en minúscula por defecto.
* **Solución:** Se corrigió la consulta para usar el nombre de columna en minúscula (`country`) para que coincidiera con la convención de PostgreSQL.

![Mi imagen](../sqoop-ingestion/imagenes/image%202.png)

---

### 3) Importar la tabla `orders` completa a HDFS

**Objetivo:** Ingestar todos los registros de la tabla `orders` y almacenarlos en HDFS en formato Parquet.

* **Comando:**
    ```bash
    sqoop import \
    --connect jdbc:postgresql://172.17.0.3:5432/northwind \
    --username postgres \
    --P \
    --table orders \
    --as-parquetfile \
    --target-dir /sqoop/ingest/orders \
    --delete-target-dir
    ```
* **Problema Común:** Los errores de sintaxis en el comando de Sqoop, como olvidar los `\`, lo que causa que los argumentos se interpreten incorrectamente.
* **Solución:** Se verificó que cada argumento estuviera en una nueva línea y terminara con un `\` para una correcta ejecución.

![Mi imagen](../sqoop-ingestion/imagenes/image%203%201.png)
![Mi imagen](../sqoop-ingestion/imagenes/image%203%202.png)

---

### 4) Importar productos con más de 20 unidades en stock

**Objetivo:** Importar una porción de la tabla `products`, filtrando solo los registros donde `units_in_stock` sea mayor que 20.

* **Comando:**
    ```bash
    sqoop import \
    --connect jdbc:postgresql://172.17.0.3:5432/northwind \
    --username postgres \
    --P \
    --table products \
    --as-parquetfile \
    --where "units_in_stock > 20" \
    --target-dir /sqoop/ingest/products_in_stock \
    --delete-target-dir
    ```
* **Problemas Comunes:**
    * **Case Sensitivity en Nombre de Tabla:** El primer intento falló porque se usó `--table Products` en lugar del nombre correcto en minúsculas, `products`.
    * **Case Sensitivity en Nombre de Columna:** El siguiente intento falló porque la columna se referenció como `UnitsInStock` en lugar del nombre correcto en minúsculas y con guion bajo, `units_in_stock`.
* **Solución:** Se corrigieron tanto el nombre de la tabla como el de la columna a sus respectivas versiones en minúscula para que coincidieran con el esquema de la base de datos de PostgreSQL.


![Mi imagen](../sqoop-ingestion/imagenes/image%204%201.png)
![Mi imagen](../sqoop-ingestion/imagenes/image%204%202.png)
