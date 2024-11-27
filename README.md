# PySpark Cheatsheet para Examen: DataFrames y Datasets
[- Docs pyspark Databricks](https://api-docs.databricks.com/python/pyspark/latest/index.html)

[- Docs pyspark oficial](https://spark.apache.org/docs/latest/api/python/index.html)

Esta cheatsheet te ayudará a dominar los conceptos esenciales de PySpark, específicamente para trabajar con DataFrames (DF) y Datasets (DS). Aquí encontrarás una explicación detallada sobre cómo crear, manipular y trabajar con DataFrames y Datasets para que puedas prepararte de manera óptima y lograr un 10 en tu examen. Además de conceptos básicos, también cubriremos operaciones avanzadas, mejores prácticas, y ejemplos de preguntas del examen que te ayudarán a poner en práctica todos los conocimientos adquiridos. Esta guía es completa y te permitirá tener todos los recursos necesarios para enfrentarte al examen sin problemas.

## Creación y Manipulación de DataFrames

### 1. Crear un DataFrame

- **Desde una lista de tuplas:**

  ```python
  from pyspark.sql import SparkSession
  spark = SparkSession.builder.appName("Examen").getOrCreate()

  data = [(1, "Pikachu", 300), (2, "Bulbasaur", 400)]
  columns = ["id", "Pokemon", "Power"]
  df = spark.createDataFrame(data, columns)
  ```

  Puedes crear DataFrames a partir de estructuras de datos de Python como listas de tuplas. Esto es particularmente útil para pequeños conjuntos de datos de prueba y demostración.

- **Desde un archivo CSV:**

  ```python
  df = spark.read.csv("ruta/del/archivo.csv", header=True, inferSchema=True)
  ```

  También puedes leer datos directamente desde archivos CSV. Usar `header=True` te permite mantener los encabezados del archivo CSV, y `inferSchema=True` intenta deducir el tipo de cada columna automáticamente.

- **Desde un archivo JSON:**

  ```python
  df = spark.read.json("ruta/del/archivo.json")
  ```

  Puedes leer datos desde archivos JSON de manera similar. Esto es útil cuando trabajas con datos semiestructurados.

- **Desde una tabla SQL:**

  ```python
  df = spark.read.format("jdbc").option("url", "jdbc:mysql://localhost:3306/database")
      .option("dbtable", "tabla").option("user", "usuario").option("password", "contraseña").load()
  ```

  Si tienes acceso a bases de datos SQL, puedes crear DataFrames directamente desde tablas SQL usando JDBC.

### 2. Inspección de DataFrames

- **Ver primeras filas:** `df.show(n=5)`
  Muestra las primeras `n` filas del DataFrame. Esto es muy útil para revisar rápidamente los datos que has cargado.

- **Ver la estructura del DataFrame:** `df.printSchema()`
  Imprime la estructura del DataFrame, mostrando los nombres de las columnas y sus tipos de datos.

- **Obtener columnas:** `df.columns`
  Devuelve una lista de los nombres de las columnas presentes en el DataFrame.

- **Ver tipo de datos:** `df.dtypes`
  Devuelve una lista de tuplas donde cada tupla contiene el nombre de una columna y su tipo de dato.

- **Número de filas y columnas:**

  ```python
  num_filas = df.count()
  num_columnas = len(df.columns)
  print(f"Filas: {num_filas}, Columnas: {num_columnas}")
  ```

  Esto te permite obtener el tamaño del DataFrame.

- **Describir estadísticas:** `df.describe().show()`
  Muestra estadísticas descriptivas básicas como el promedio, el mínimo y el máximo de las columnas numéricas.

- **Schema detallado:** `df.schema.json()`
  Devuelve el esquema del DataFrame en formato JSON, útil para inspeccionar la estructura completa del DataFrame.

### 3. Selección y Filtrado

- **Seleccionar columnas:** `df.select("Pokemon", "Power").show()`
  Puedes seleccionar columnas específicas para visualizar, lo cual es muy útil para enfocarse en partes particulares de los datos.

- **Seleccionar con alias:**

  ```python
  df.select(col("Pokemon").alias("Nombre_Pokemon"), "Power").show()
  ```

  Puedes renombrar las columnas mientras las seleccionas usando `alias()`. Esto es útil cuando quieres trabajar con nombres más comprensibles.

- **Filtrar filas:** `df.filter(df.Power > 300).show()`
  Filtra las filas según una condición. En este ejemplo, selecciona solo las filas donde la columna `Power` es mayor a 300.

- **Condiciones múltiples:**

  ```python
  from pyspark.sql.functions import col
  df.filter((col("Power") > 300) & (col("Pokemon") == "Pikachu")).show()
  ```

  Puedes aplicar múltiples condiciones utilizando operadores como `&` (AND) o `|` (OR). La función `col()` permite acceder a las columnas de forma más flexible.

- **Filtrar valores nulos:** `df.filter(df["Power"].isNotNull()).show()`
  Filtra las filas que no tienen valores nulos en una columna específica.

### 4. Ordenación y Agrupación

- **Ordenar filas:** `df.orderBy("Power", ascending=False).show()`
  Ordena las filas del DataFrame según una columna en orden ascendente o descendente.

- **Ordenar usando varias columnas:**

  ```python
  df.orderBy(["Power", "Pokemon"], ascending=[False, True]).show()
  ```

  Puedes ordenar por varias columnas especificando el orden para cada una.

- **Agrupar y agregar:**

  ```python
  df.groupBy("Pokemon").agg({"Power": "avg"}).show()
  ```

  Agrupa los datos según una columna y aplica una función de agregación. En este caso, se está calculando el promedio (`avg`) de la columna `Power`.

- **Agrupar usando funciones agregadas:**

  ```python
  from pyspark.sql.functions import avg, sum, count
  df.groupBy("Pokemon").agg(avg("Power").alias("Promedio_Power"), sum("Power"), count("Power")).show()
  ```

  Puedes utilizar varias funciones agregadas simultáneamente para tener una visión más completa de tus datos.

### 5. Agregar y Modificar Columnas

- **Crear una nueva columna:**

  ```python
  from pyspark.sql.functions import lit
  df = df.withColumn("New_Column", lit(100))
  ```

  Puedes agregar nuevas columnas con valores constantes usando `lit()`. Esto es útil cuando necesitas añadir información adicional a cada fila.

- **Modificar columna existente:**

  ```python
  df = df.withColumn("Power", df["Power"] * 2)
  ```

  Modifica una columna existente aplicando una operación sobre ella. Aquí, se duplica el valor de `Power`.

- **Agregar columnas calculadas:**

  ```python
  from pyspark.sql.functions import col
  df = df.withColumn("Power_Multiplied", col("Power") * 3)
  ```

  Puedes agregar columnas calculadas utilizando expresiones sobre otras columnas del DataFrame.

- **Renombrar columna:** `df = df.withColumnRenamed("Old_Column", "New_Column")`
  Renombra una columna del DataFrame para hacerla más clara o para ajustarse a un estándar específico.

- **Eliminar columnas:**

  ```python
  df = df.drop("columna1")
  df = df.drop("columna1", "columna2")
  ```

  Utiliza `drop()` para eliminar una o varias columnas del DataFrame.

### 6. Eliminar Valores Nulos

- **Mostrar filas con valores nulos**
```python
from pyspark.sql.functions import col

# Crear una condición que verifica si cualquier columna tiene valores nulos
condition = [col(c).isNull() for c in df.columns]

# Filtrar filas que tengan al menos un valor nulo
df_con_nulos = df.filter(any(condition))

df_con_nulos.show()


```

- **Eliminar filas con nulos:** `df.na.drop()`
  Elimina las filas que contienen valores `NULL`. Puedes usar parámetros adicionales para eliminar filas solo si ciertas columnas tienen valores nulos.

- **Eliminar filas con nulos en columnas específicas:**

  ```python
  df = df.na.drop(subset=["Power", "Pokemon"])

  #con al menos n columnas con nulos
  df.dropna(thresh=n)
  ```

  Solo elimina las filas si las columnas especificadas tienen valores nulos.

- **Rellenar nulos:** `df.fillna(0, subset=["Power"])`
  Rellena los valores nulos de columnas específicas con un valor dado, en este caso `0`.

- **Rellenar valores nulos con diferentes valores por columna:**

  ```python
  df = df.fillna({"Power": 0, "Pokemon": "Desconocido"})
  ```

  Puedes especificar diferentes valores para diferentes columnas al rellenar valores nulos.

### 7. Uniones (Joins)

- **Inner Join:**

  ```python
  df_joined = df.join(df_attacks, df["Pokemon"] == df_attacks["Pokemon"])
  ```

  Realiza una unión interna entre dos DataFrames, manteniendo solo las filas que tienen coincidencias en ambas tablas.

- **Left Join:**

  ```python
  df_joined = df.join(df_attacks, df["Pokemon"] == df_attacks["Pokemon"], "left")
  ```

  Realiza una unión donde se conservan todas las filas del DataFrame de la izquierda, incluso si no hay coincidencias en el de la derecha.

- **Right Join:**

  ```python
  df_joined = df.join(df_attacks, df["Pokemon"] == df_attacks["Pokemon"], "right")
  ```

  Mantiene todas las filas del DataFrame de la derecha, incluyendo valores `NULL` si no hay coincidencias en el DataFrame de la izquierda.

- **Full Outer Join:**

  ```python
  df_joined = df.join(df_attacks, df["Pokemon"] == df_attacks["Pokemon"], "outer")
  ```

  Mantiene todas las filas de ambos DataFrames, llenando con `NULL` donde no hay coincidencias.

- **Cross Join (Producto Cartesiano):**

  ```python
  df_cross = df.crossJoin(df_attacks)
  ```

  Realiza un producto cartesiano, devolviendo todas las combinaciones posibles de las filas de ambos DataFrames.

- **Union de DataFrames:**

  ```python
  df_union = df.union(df_attacks)
  ```

  Combina dos DataFrames con las mismas columnas en un único DataFrame.

## Operaciones Avanzadas

### 8. Funciones de PySpark SQL

- **Funciones SQL comunes:**

  ```python
  from pyspark.sql.functions import avg, max, min
  df.select(avg("Power"), max("Power"), min("Power")).show()
  ```

  Utiliza funciones agregadas como `avg`, `max` y `min` para realizar cálculos comunes sobre las columnas del DataFrame.

- **Funciones para cadenas:**

  ```python
  from pyspark.sql.functions import lower, upper, length, concat
  df = df.withColumn("Pokemon", lower(col("Pokemon")))
  df = df.withColumn("Nombre_Completo", concat(col("Pokemon"), lit(" - Poder: "), col("Power")))
  ```

  Convierte todos los valores de una columna a minúsculas (`lower()`) o mayúsculas (`upper()`). Usa `concat()` para concatenar valores de diferentes columnas, útil para generar textos descriptivos.

### 9. Transformaciones usando `selectExpr`

- **Transformar columnas con SQL:**

  ```python
  df = df.selectExpr("Power * 2 as Power_Doubled", "Pokemon")
  ```

  Puedes usar expresiones SQL para transformar directamente las columnas y crear nuevas. Este enfoque es útil cuando deseas combinar múltiples operaciones en una sola línea.

- **Operaciones condicionales con ****`selectExpr`****:**

  ```python
  df = df.selectExpr("Power", "Pokemon", "case when Power > 300 then 'Fuerte' else 'Débil' end as Categoria")
  ```

  Puedes aplicar lógica condicional directamente en `selectExpr` para crear nuevas columnas basadas en condiciones.

### 10. Explosiones y Pivotaje

- **Explode:**

  ```python
  from pyspark.sql.functions import explode
  df_exploded = df.withColumn("Exploded_Column", explode(df["Array_Column"]))
  ```

  `explode()` convierte una columna de tipo lista en varias filas, una para cada elemento de la lista. Esto es útil cuando trabajas con estructuras de datos anidadas.

- **Explode Map:**

  ```python
  df_exploded = df.withColumn("Key", explode(df["Map_Column"].keys())).withColumn("Value", explode(df["Map_Column"].values()))
  ```

  Puedes explotar columnas de tipo `Map` para dividir las llaves y los valores en distintas columnas.

- **Pivot:**

  ```python
  df_pivoted = df.groupBy("Pokemon").pivot("Type").sum("Power")
  ```

  `pivot()` reorganiza los datos para convertir valores únicos de una columna en nuevas columnas, con funciones de agregación aplicadas.

### 11. Repartición y Coalescencia

- **Repartition:**

  ```python
  df = df.repartition(4)
  ```

  Cambia el número de particiones del DataFrame, útil para mejorar la paralelización durante el procesamiento.

- **Coalesce:**

  ```python
  df = df.coalesce(1)
  ```

  Reduce el número de particiones, útil cuando se quiere reducir el sobrecoste de escritura a disco.

### 12. Distinct y Drop Duplicates

- **Distinct:**

  ```python
  df_distinct = df.distinct()
  ```

  Devuelve solo las filas únicas en el DataFrame, eliminando duplicados.

- **Drop Duplicates:**

  ```python
  df_deduplicated = df.dropDuplicates(["column1", "column2"])
  ```

  Elimina duplicados basados en una o varias columnas específicas.

### 13. Funciones de Fecha y Hora

- **Funciones para manipular fechas y horas:**
  ```python
  from pyspark.sql.functions import current_date, date_add, date_sub, datediff, to_date
  df = df.withColumn("Fecha_Actual", current_date())
  df = df.withColumn("Fecha_Agregada", date_add(df["Fecha_Actual"], 10))
  df = df.withColumn("Diferencia_Dias", datediff(df["Fecha_Agregada"], df["Fecha_Actual"]))
  ```
  Estas funciones permiten agregar o restar días a fechas, calcular diferencias entre fechas, y convertir cadenas en formato de fecha.

### 14. Operaciones con `map` y `flatMap`

- **Map y FlatMap:**
  ```python
  rdd = df.rdd
  rdd_mapped = rdd.map(lambda x: (x[0], x[1].upper()))
  rdd_flat_mapped = rdd.flatMap(lambda x: x)
  ```
  `map()` aplica una función a cada fila del RDD, mientras que `flatMap()` aplica una función y aplana el resultado. Aunque esto es más común con RDDs, es útil conocerlo para transformaciones personalizadas.

### 15. Acciones `first()`, `take()`, y `head()`

- **Acciones para inspeccionar los datos:**
  ```python
  primera_fila = df.first()
  primeras_filas = df.take(5)
  encabezado = df.head(3)
  ```
  Estas funciones permiten inspeccionar rápidamente los primeros elementos del DataFrame.

### 16. Uso de `collect_list()` y `collect_set()`

- **Agrupar elementos en listas o sets:**
  ```python
  from pyspark.sql.functions import collect_list, collect_set
  df_agrupado = df.groupBy("Pokemon").agg(collect_list("Power").alias("Poderes"), collect_set("Type").alias("Tipos_Unicos"))
  ```
  `collect_list()` agrupa los valores de una columna en una lista, mientras que `collect_set()` agrupa los valores únicos en un set.

### 17. Operaciones de Aggregate (`agg()`) avanzadas

- **Agregaciones avanzadas:**
  ```python
  from pyspark.sql.functions import stddev, variance
  df_agg = df.groupBy("Pokemon").agg(avg("Power"), sum("Power"), stddev("Power"), variance("Power"))
  ```
  Puedes usar `agg()` con múltiples funciones de agregación, como desviación estándar (`stddev`) o varianza (`variance`), para obtener un análisis más profundo.

### 18. Operaciones con `zipWithIndex()`

- **Agregar un índice a las filas:**
  ```python
  from pyspark.sql import Row
  indexed_rdd = df.rdd.zipWithIndex().map(lambda row_index: Row(index=row_index[1], **row_index[0].asDict()))
  df_indexed = spark.createDataFrame(indexed_rdd)
  ```
  `zipWithIndex()` agrega un índice a cada fila del RDD, útil cuando necesitas una numeración única para cada registro.

### 19. Columnas Complejas: `ArrayType` y `StructType`

- **Trabajar con columnas de tipo complejo:**
  ```python
  from pyspark.sql.types import ArrayType, StructType, StructField, StringType, IntegerType
  data = [(1, ["fire", "water"], ("Pikachu", 300))]
  schema = StructType([
      StructField("id", IntegerType(), True),
      StructField("types", ArrayType(StringType()), True),
      StructField("pokemon_info", StructType([
          StructField("name", StringType(), True),
          StructField("power", IntegerType(), True)
      ]), True)
  ])
  df_complex = spark.createDataFrame(data, schema)
  df_complex.printSchema()
  ```
  `ArrayType` permite definir listas dentro de una columna, y `StructType` permite crear una estructura anidada, similar a un objeto JSON. Esto es útil para trabajar con datos jerárquicos o anidados.

## Sección Extra: Operaciones Adicionales

### 20. Manejo de Columnas Complejas (`map`, `array`)

- **Trabajar con columnas de tipo `map` y `array`:**
  ```python
  from pyspark.sql.functions import lit, map_from_arrays, array

  # Crear una columna de tipo `array`
  df = df.withColumn("types", array(lit("fire"), lit("water")))
  df.show()

  # Crear una columna de tipo `map`
  df = df.withColumn("attributes_map", map_from_arrays(array(lit("key1"), lit("key2")), array(lit("value1"), lit("value2"))))
  df.show()
  ```
  Puedes trabajar con columnas de tipo `map` y `array` para representar datos anidados o múltiples valores por fila.

### 21. Funciones de Ranking (`rank()`, `dense_rank()`, `row_number()`)

- **Ranking de datos:**
  ```python
  from pyspark.sql.window import Window
  from pyspark.sql.functions import rank, dense_rank, row_number

  window_spec = Window.partitionBy("Pokemon").orderBy("Power")
  df = df.withColumn("rank", rank().over(window_spec))
  df = df.withColumn("dense_rank", dense_rank().over(window_spec))
  df = df.withColumn("row_number", row_number().over(window_spec))
  df.show()
  ```
  Estas funciones de ranking te permiten clasificar los datos dentro de particiones específicas, lo cual es útil para análisis jerárquicos o para seleccionar los mejores registros.

### 22. Lectura y Escritura en Hadoop/HDFS

- **Leer y escribir en HDFS:**
  ```python
  # Leer desde HDFS
  df = spark.read.csv("hdfs://localhost:9000/ruta/del/archivo.csv", header=True, inferSchema=True)

  # Escribir en HDFS
  df.write.csv("hdfs://localhost:9000/ruta/de/salida", mode="overwrite", header=True)
  ```
  Puedes interactuar directamente con HDFS para almacenar y procesar grandes volúmenes de datos distribuidos.

### 23. Operaciones con `cube()` y `rollup()`

- **Operaciones jerárquicas y análisis multidimensional:**
  ```python
  # Usar `cube` para agregar datos a diferentes niveles
  df_cubed = df.cube("Pokemon", "Type").sum("Power")
  df_cubed.show()

  # Usar `rollup` para agregar jerárquicamente
  df_rolled_up = df.rollup("Pokemon", "Type").sum("Power")
  df_rolled_up.show()
  ```
  `cube()` y `rollup()` son útiles para realizar análisis multidimensionales y generar agregados jerárquicos en diferentes niveles de detalle.

## Tips para el Examen

### 24. Diferencias entre DataFrames y Datasets

- **DataFrame:** Estructura distribuida con columnas, similar a una tabla SQL. Es flexible y puede contener datos no tipados. Se utiliza comúnmente para procesamiento de datos y tiene una API más sencilla.
- **Dataset:** API más fuertemente tipada (en PySpark, DataFrame y Dataset son equivalentes). Ofrecen más garantías de tipo, pero en PySpark suelen tratarse de la misma manera que un DataFrame. Los Datasets están más presentes en la versión Scala de Spark.

### 25. Acciones vs Transformaciones

- **Acciones:** `show()`, `collect()`, `count()` — Devuelven resultados y desencadenan el cálculo de todas las transformaciones pendientes. Usar una acción obliga a Spark a ejecutar el plan lógico y devolver el resultado.

- **Transformaciones:** `filter()`, `select()`, `join()` — Son operaciones perezosas. Spark no las ejecuta hasta que se llama a una acción, lo cual optimiza el rendimiento combinando múltiples transformaciones en un solo paso.

- **Ejemplo de Transformación y Acción juntas:**

  ```python
  df_filtered = df.filter(df["Power"] > 300)
  df_filtered.show()  # Aquí es cuando se realiza el cálculo
  ```

### 26. Buenas Prácticas

- **Uso de ****`cache()`**** para reuso de DataFrames:** Acelera las operaciones posteriores si el DF se utiliza varias veces. Esto es especialmente útil si el DataFrame requiere muchas transformaciones complejas.

  ```python
  df_cached = df.cache()
  ```

  Al almacenar en caché, los datos se guardan en la memoria, evitando recalcular cada transformación en futuras operaciones.

- **Uso de ****`persist()`****:**

  ```python
  from pyspark import StorageLevel
  df_persisted = df.persist(StorageLevel.MEMORY_AND_DISK)
  ```

  `persist()` te permite definir el nivel de almacenamiento. Por ejemplo, `MEMORY_AND_DISK` almacena en memoria, pero si no hay suficiente espacio, guarda en disco.

- **Usar ****`explain()`**** para depurar el plan de ejecución:**

  ```python
  df.explain()
  ```

  `explain()` imprime el plan de ejecución físico del DataFrame, lo cual te ayuda a entender cómo Spark planea ejecutar las operaciones. Esto es esencial para optimizar tu trabajo y detectar cuellos de botella.

- **Repartir Datos:**

  ```python
  df = df.repartition(4)
  ```

  - **Pruebas de formateo automatico**
  ```python
  from pyspark.sql import SparkSession
from pyspark.sql.functions import col

# Crear la sesión de Spark
spark = SparkSession.builder.appName('ManejoCSV').getOrCreate()

# Cargar el archivo CSV mal formateado con opciones para manejar los errores
df = spark.read.format("csv") \
    .option("header", "true") \
    .option("delimiter", ",") \
    .option("multiLine", "true") \
    .option("escape", "\"") \
    .option("mode", "PERMISSIVE") \
    .load("dbfs:/FileStore/shared_uploads/carlosalonsomingo@gmail.com/ventsa.csv")

# Mostrar los datos cargados
df.show(truncate=False)
```

  Cambia el número de particiones del DataFrame para mejorar la paralelización y el rendimiento, especialmente si tienes operaciones que requieren una mayor distribución

