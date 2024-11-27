# README: Transformaciones en Apache Spark
[- Docs pyspark Databricks](https://api-docs.databricks.com/python/pyspark/latest/index.html)

[- Docs pyspark oficial](https://spark.apache.org/docs/latest/api/python/index.html)

Este documento detalla las principales transformaciones que se pueden realizar en Apache Spark, incluyendo transformaciones básicas y avanzadas tanto en RDDs como en DataFrames. Esta guía te ayudará a entender cómo manipular y transformar los datos de manera eficiente utilizando Spark.

## Tabla de Contenidos
1. [Introducción a Transformaciones](#1-introducción-a-transformaciones)
2. [Transformaciones Básicas en DataFrames](#2-transformaciones-básicas-en-dataframes)
   - 2.1 [`select()`](#21-select)
   - 2.2 [`filter()`](#22-filter)
   - 2.3 [`withColumn()`](#23-withcolumn)
   - 2.4 [`drop()`](#24-drop)
3. [Transformaciones Avanzadas en DataFrames](#3-transformaciones-avanzadas-en-dataframes)
   - 3.1 [`groupBy()` y `agg()`](#31-groupby-y-agg)
   - 3.2 [`join()`](#32-join)
   - 3.3 [`orderBy()`](#33-orderby)
   - 3.4 [`dropDuplicates()`](#34-dropduplicates)
   - 3.5 [`pivot()`](#35-pivot)
   - 3.6 [`explode()`](#36-explode)
   - 3.7 [`union()`](#37-union)
   - 3.8 [`crossJoin()`](#38-crossjoin)
   - 3.9 [`fillna()` / `replace()`](#39-fillna--replace)
   - 3.10 [`distinct()`](#310-distinct)
   - 3.11 [`sample()`](#311-sample)
   - 3.12 [`rollup()` y `cube()`](#312-rollup-y-cube)
   - 3.13 [Funciones de Ventana (`window()`)](#313-funciones-de-ventana-window)
   - 3.14 [Transformaciones Condicionales (`when()` y `otherwise()`)](#314-transformaciones-condicionales-when-y-otherwise)
   - 3.15 [Funciones Definidas por el Usuario (UDFs)](#315-funciones-definidas-por-el-usuario-udfs)
   - 3.16 [Expresiones Regulares (`regexp_extract()`, `regexp_replace()`)](#316-expresiones-regulares-regexp_extract-y-regexp_replace)
4. [Transformaciones en RDDs](#4-transformaciones-en-rdds)
   - 4.1 [`map()`](#41-map)
   - 4.2 [`flatMap()`](#42-flatmap)
   - 4.3 [`filter()`](#43-filter)
   - 4.4 [`reduceByKey()`](#44-reducebykey)
   - 4.5 [`sortBy()`](#45-sortby)
5. [Optimización de Transformaciones](#5-optimización-de-transformaciones)
   - 5.1 [`repartition()` vs `coalesce()`](#51-repartition-vs-coalesce)
   - 5.2 [Persistencia con `cache()` y `persist()`](#52-persistencia-con-cache-y-persist)
6. [Conclusión](#6-conclusión)

## 1. Introducción a Transformaciones

Las transformaciones en Apache Spark permiten manipular datos y generar nuevos DataFrames o RDDs. Estas transformaciones son "perezosas" (lazy), lo cual significa que no se ejecutan hasta que se llama a una acción sobre el DataFrame o RDD. En esta guía, veremos tanto transformaciones básicas como avanzadas, y discutiremos cómo optimizar su uso para un rendimiento eficiente.

## 2. Transformaciones Básicas en DataFrames

### 2.1 `select()`
Selecciona una o varias columnas de un DataFrame.
```python
ventas_df = ventas_df.select("Product", "Quantity Ordered")
```

### 2.2 `filter()`
Filtra filas según una condición específica.
```python
ventas_filtradas = ventas_df.filter(ventas_df["Quantity Ordered"] > 50)
```

### 2.3 `withColumn()`
Añade una nueva columna o modifica una columna existente.
```python
from pyspark.sql.functions import col
ventas_df = ventas_df.withColumn("Total Price", col("Quantity Ordered") * col("Price Each"))
```

### 2.4 `drop()`
Elimina una o varias columnas del DataFrame.
```python
ventas_df = ventas_df.drop("ColumnToDrop")
```

## 3. Transformaciones Avanzadas en DataFrames

### 3.1 `groupBy()` y `agg()`
Permiten agrupar datos por una columna y realizar cálculos agregados como `sum()`, `avg()`, etc.
```python
ventas_agrupadas = ventas_df.groupBy("Product").agg(F.sum("Quantity Ordered").alias("Total Ventas"))
```

### 3.2 `join()`
Une dos DataFrames basándose en una clave común.
```python
joined_df = df1.join(df2, df1["ProductID"] == df2["ProductID"], "inner")
```

### 3.3 `orderBy()`
Ordena los datos por una o varias columnas.
```python
ventas_ordenadas = ventas_df.orderBy("Total Price", ascending=False)
```

### 3.4 `dropDuplicates()`
Elimina las filas duplicadas basándose en una o varias columnas.
```python
df_sin_duplicados = ventas_df.dropDuplicates(["Product"])
```

### 3.5 `pivot()`
Reorganiza los datos convirtiendo los valores únicos de una columna en nuevas columnas.
```python
ventas_pivot = ventas_df.groupBy("Region").pivot("Product").sum("Quantity Ordered")
```

### 3.6 `explode()`
Aplana una columna de tipo lista o array, creando una fila para cada elemento.
```python
from pyspark.sql.functions import explode
df_exploded = df.withColumn("exploded_column", explode(df["array_column"]))
```

### 3.7 `union()`
Une dos DataFrames con las mismas columnas.
```python
df_union = df1.union(df2)
```

### 3.8 `crossJoin()`
Realiza un producto cartesiano entre dos DataFrames.
```python
df_cross = df1.crossJoin(df2)
```

### 3.9 `fillna()` / `replace()`
Reemplaza valores nulos o específicos en el DataFrame.
```python
df_filled = df.fillna(0)
df_replaced = df.replace("old_value", "new_value", "column_name")
```

### 3.10 `distinct()`
Devuelve un DataFrame sin filas duplicadas.
```python
df_distinct = df.distinct()
```

### 3.11 `sample()`
Muestra aleatoria de datos.
```python
df_sample = df.sample(withReplacement=False, fraction=0.1)
```

### 3.12 `rollup()` y `cube()`
Realizan agregaciones jerárquicas o multidimensionales.
```python
df_rollup = df.rollup("column1", "column2").sum("value_column")
df_cube = df.cube("column1", "column2").sum("value_column")
```

### 3.13 Funciones de Ventana (`window()`)
Utiliza ventanas para cálculos como sumas acumuladas, medias móviles, etc.
```python
from pyspark.sql.window import Window
from pyspark.sql.functions import avg
window_spec = Window.partitionBy("column1").orderBy("column2")
df_window = df.withColumn("avg_value", avg("value").over(window_spec))
```

### 3.14 Transformaciones Condicionales (`when()` y `otherwise()`)
Permiten realizar transformaciones basadas en condiciones específicas.
```python
from pyspark.sql.functions import when
df_condicional = df.withColumn("Discount", when(df["Category"] == "Electronics", 0.1).otherwise(0.05))
```

### 3.15 Funciones Definidas por el Usuario (UDFs)
Las UDFs permiten aplicar funciones personalizadas a las columnas de un DataFrame cuando las funciones estándar no son suficientes.
```python
from pyspark.sql.functions import udf
from pyspark.sql.types import StringType

def convertir_mayusculas(cadena):
    return cadena.upper()

udf_mayusculas = udf(convertir_mayusculas, StringType())
df_udf = df.withColumn("Nombre_Mayusculas", udf_mayusculas(df["Nombre"]))
```

### 3.16 Expresiones Regulares (`regexp_extract()` y `regexp_replace()`)
Permiten extraer o reemplazar patrones de texto usando expresiones regulares.
```python
from pyspark.sql.functions import regexp_extract, regexp_replace

# Extraer un patrón específico
df_extract = df.withColumn("CodigoPostal", regexp_extract(df["Direccion"], r"\d{5}", 0))

# Reemplazar un patrón específico
df_replace = df.withColumn("Direccion_Limpia", regexp_replace(df["Direccion"], r"\d{5}", "XXXXX"))
```

## 4. Transformaciones en RDDs

### 4.1 `map()`
Aplica una función a cada elemento del RDD, transformándolo.
```python
rdd_transformado = rdd.map(lambda x: x * 2)
```

### 4.2 `flatMap()`
Similar a `map()`, pero aplana el resultado para evitar listas anidadas.
```python
rdd_flat = rdd.flatMap(lambda x: x.split(" "))
```

### 4.3 `filter()`
Filtra los elementos del RDD según una condición.
```python
rdd_filtrado = rdd.filter(lambda x: x > 10)
```

### 4.4 `reduceByKey()`
Agrupa los pares clave-valor y aplica una función de reducción a los valores que tienen la misma clave.
```python
rdd_kv = rdd.map(lambda x: (x, 1))
rdd_reducido = rdd_kv.reduceByKey(lambda a, b: a + b)
```

### 4.5 `sortBy()`
Ordena los elementos del RDD según una clave especificada.
```python
rdd_ordenado = rdd.sortBy(lambda x: x[1], ascending=False)
```

## 5. Optimización de Transformaciones

### 5.1 `repartition()` vs `coalesce()`
- **`repartition(n)`**: Incrementa o redistribuye el número de particiones. Ideal para distribuir el procesamiento de manera más uniforme.
- **`coalesce(n)`**: Reduce el número de particiones, útil para reducir el costo de operaciones de escritura.

```python
df_repartido = ventas_df.repartition(4)
df_coalescido = ventas_df.coalesce(2)
```

### 5.2 Persistencia con `cache()` y `persist()`
- **`cache()`**: Almacena el DataFrame/RDD en memoria para un uso posterior más rápido.
- **`persist(storage_level)`**: Permite definir el nivel de almacenamiento, como `MEMORY_ONLY` o `MEMORY_AND_DISK`.

```python
df_persistido = ventas_df.cache()
df_persistido.show()
```

## 6. Conclusión

Apache Spark ofrece una variedad de transformaciones para manipular y transformar datos de manera eficiente. Desde transformaciones básicas como `select()` y `filter()`, hasta técnicas más avanzadas como `groupBy()` y `pivot()`, comprender y aplicar correctamente estas herramientas es esencial para el procesamiento de datos a gran escala. Además, la optimización mediante `repartition()`, `coalesce()`, y el uso adecuado de la persistencia permite mejorar significativamente el rendimiento de tus tareas Spark.

Este documento sirve como una referencia práctica para aplicar estas transformaciones, ayudándote a sacar el máximo provecho de Spark en tus proyectos de análisis y procesamiento de datos.

