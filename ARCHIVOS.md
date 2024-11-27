# Documentación para Examen de PySpark en Databricks

Este documento proporciona una guía sobre cómo realizar las operaciones más comunes en PySpark dentro del entorno de Databricks, centrándose en la lectura, escritura y transformación de diferentes tipos de archivos como CSV, Parquet, JSON y Avro. La idea es proporcionar una referencia clara y útil para los comandos esenciales que podrían ser evaluados en un examen.

## Índice
1. [Lectura de Archivos](#lectura-de-archivos)
2. [Escritura de Archivos](#escritura-de-archivos)
3. [Transformaciones](#transformaciones)

## Lectura de Archivos
### CSV
Para leer archivos CSV, PySpark ofrece el método `read.csv()`, que permite cargar datos estructurados de manera fácil. En Databricks, puedes utilizar el almacenamiento de Databricks directamente:

```python
from pyspark.sql import SparkSession

spark = SparkSession.builder.appName("LecturaCSV").getOrCreate()
df_csv = spark.read.csv("/dbfs/ruta/del/archivo.csv", header=True, inferSchema=True)
df_csv.show()
```
- `header=True`: Indica que el archivo CSV tiene una fila de encabezados.
- `inferSchema=True`: Infere el tipo de datos de cada columna.

### Parquet
Parquet es un formato de almacenamiento columnar eficiente. Para leer archivos Parquet:

```python
df_parquet = spark.read.parquet("/dbfs/ruta/del/archivo.parquet")
df_parquet.show()
```
Este formato es recomendado cuando se trabaja con grandes volúmenes de datos debido a su capacidad de compresión y eficiencia en lectura.

### JSON
Para archivos JSON, el método `read.json()` permite cargar los datos:

```python
df_json = spark.read.json("/dbfs/ruta/del/archivo.json")
df_json.show()
```
Este método soporta tanto JSON de una sola línea como archivos con varias líneas JSON.

### Avro
Para leer archivos en formato Avro, se utiliza el método `read.format("avro")`:

```python
df_avro = spark.read.format("avro").load("/dbfs/ruta/del/archivo.avro")
df_avro.show()
```
El formato Avro es adecuado para el almacenamiento eficiente y la serialización de datos.

## Escritura de Archivos
### CSV
Para escribir un DataFrame a un archivo CSV, usamos el método `write.csv()`:

```python
df_csv.write.csv("/dbfs/ruta/de/salida.csv", mode="overwrite", header=True)
```
- `mode="overwrite"`: Sobrescribe cualquier archivo existente.
- `header=True`: Incluye una fila de encabezado en el archivo de salida.

### Parquet
Para escribir en formato Parquet:

```python
df_parquet.write.parquet("/dbfs/ruta/de/salida.parquet", mode="overwrite")
```
Este formato se recomienda para una salida eficiente y almacenamiento optimizado.

### JSON
Para escribir datos en formato JSON:

```python
df_json.write.json("/dbfs/ruta/de/salida.json", mode="overwrite")
```

### Avro
Para escribir un DataFrame en formato Avro:

```python
df_avro.write.format("avro").save("/dbfs/ruta/de/salida.avro", mode="overwrite")
```

## Transformaciones
### Selección y Filtrado
- **Selección de Columnas**: Para seleccionar columnas específicas del DataFrame:
  
  ```python
  df_seleccion = df_csv.select("columna1", "columna2")
  df_seleccion.show()
  ```

- **Filtrado**: Para filtrar filas basadas en una condición:
  
  ```python
  df_filtrado = df_csv.filter(df_csv["columna1"] > 100)
  df_filtrado.show()
  ```

### Creación de Columnas
Para agregar una nueva columna calculada:

```python
from pyspark.sql.functions import col

df_nueva_columna = df_csv.withColumn("nueva_columna", col("columna1") * 2)
df_nueva_columna.show()
```

### Agrupación y Agregación
Para realizar agrupaciones y cálculos agregados:

```python
df_agrupado = df_csv.groupBy("columna2").sum("columna1")
df_agrupado.show()
```
Este ejemplo agrupa los datos por `columna2` y calcula la suma de `columna1` para cada grupo.

## Conclusión
Esta guía proporciona una base sólida para trabajar con archivos CSV, Parquet, JSON y Avro en PySpark dentro de Databricks, abordando la lectura, escritura y transformaciones básicas. Tener estos conceptos claros es fundamental para afrontar el examen con confianza y manejar cualquier escenario relacionado con el tratamiento de datos en PySpark.

