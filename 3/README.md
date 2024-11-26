# README: Procesamiento de Datos con PySpark

Este documento presenta una guía detallada sobre cómo procesar y analizar datos utilizando PySpark. A continuación se presenta un resumen del contenido del notebook, que incluye operaciones de lectura de datos, agregaciones, limpieza de datos y transformaciones comunes.

## Contenidos del Notebook

### 1. Lectura de Archivos CSV y Combinación de Datos
   - **Lectura de múltiples archivos CSV**: Cómo cargar varios archivos de ventas correspondientes a distintos meses del año 2019.
   - **Unión de DataFrames**: Combinar todos los archivos leídos en un único DataFrame para un análisis conjunto.

### 2. Estadísticas Descriptivas sobre Columnas Específicas
   - **Función `describe_columns()`**: Cómo calcular estadísticas descriptivas (mínimo, máximo, media, etc.) de columnas específicas del DataFrame.

### 3. Agrupación y Suma de Datos
   - **Función `group_and_sum()`**: Ejemplo de cómo agrupar los datos por una columna y sumar otra columna.

### 4. Filtrado de Datos
   - **Función `filter_data()`**: Filtrar datos basados en una condición específica utilizando expresiones SQL.

### 5. Añadir Nuevas Columnas Calculadas
   - **Función `add_calculated_column()`**: Cómo agregar nuevas columnas calculadas basadas en operaciones matemáticas entre columnas existentes.

## Ejemplos Sencillos

### Lectura de Múltiples Archivos CSV y Combinación
```python
from pyspark.sql import SparkSession

spark = SparkSession.builder.getOrCreate()

# Lista de nombres de archivos
file_paths = [
    "dbfs:/FileStore/shared_uploads/carlosalonsomingo@gmail.com/Sales_June_2019.csv",
    "dbfs:/FileStore/shared_uploads/carlosalonsomingo@gmail.com/Sales_July_2019.csv",
    "dbfs:/FileStore/shared_uploads/carlosalonsomingo@gmail.com/Sales_March_2019.csv",
    # Otros archivos...
]

# Leer y unir todos los archivos
combined_df = None
for path in file_paths:
    df = spark.read.format("csv").option("header", "true").option("inferSchema", "true").load(path)
    combined_df = df if combined_df is None else combined_df.union(df)

combined_df.show()
```
Este ejemplo muestra cómo cargar y combinar varios archivos CSV en un único DataFrame para su análisis.

### Calcular Estadísticas Descriptivas sobre Columnas Específicas
```python
from pyspark.sql.functions import col

def describe_columns(df, columns):
    return df.select(columns).describe()

# Ejemplo de uso
stats = describe_columns(combined_df, ["Ventas", "Unidades"])
stats.show()
```
Este ejemplo utiliza la función `describe_columns()` para calcular estadísticas sobre las columnas seleccionadas del DataFrame.

### Agrupar y Sumar Datos
```python
from pyspark.sql import functions as F

def group_and_sum(df, group_column, sum_column):
    return df.groupBy(group_column).agg(F.sum(sum_column).alias(f"sum_{sum_column}"))

# Ejemplo de uso
grouped_data = group_and_sum(combined_df, "Producto", "Ventas")
grouped_data.show()
```
Agrupa los datos por la columna `Producto` y calcula la suma de las ventas correspondientes.

### Filtrar Datos Basados en una Condición
```python
def filter_data(df, condition):
    return df.filter(condition)

# Ejemplo de uso
filtered_df = filter_data(combined_df, "Ventas > 1000")
filtered_df.show()
```
Filtra las filas del DataFrame donde la columna `Ventas` tiene un valor mayor a 1000.

### Añadir una Columna Calculada
```python
from pyspark.sql import functions as F

def add_calculated_column(df, new_column_name, calculation):
    return df.withColumn(new_column_name, calculation)

# Ejemplo de uso
combined_df = add_calculated_column(combined_df, "Revenue", F.col("Unidades") * F.col("Precio"))
combined_df.show()
```
Este ejemplo añade una nueva columna `Revenue` calculada multiplicando las columnas `Unidades` y `Precio`.

---

Este README proporciona una guía completa sobre cómo realizar operaciones comunes en PySpark, como la lectura y combinación de archivos, el filtrado y la agregación de datos, así como la creación de nuevas columnas. Si necesitas más ejemplos o aclaraciones, ¡no dudes en pedírmelo! 😊🚀

