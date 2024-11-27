# Documentación: `cast()`, Tipos de Datos y Esquemas en PySpark

En esta guía se abordarán los conceptos de conversión de tipos (`cast()`), los tipos de datos utilizados y cómo definir esquemas para trabajar con datos en PySpark. Estos conceptos son fundamentales para procesar, limpiar y transformar datos de manera efectiva en entornos distribuidos.

## Tabla de Contenidos
1. [Conversión de Tipos con `cast()`](#1-conversion-de-tipos-con-cast)
   - 1.1 [Ejemplo de Uso](#11-ejemplo-de-uso)
   - 1.2 [Tipos de Datos Comunes para `cast()`](#12-tipos-de-datos-comunes-para-cast)
2. [Tipos de Datos en PySpark](#2-tipos-de-datos-en-pyspark)
   - 2.1 [Tipos de Datos Comunes](#21-tipos-de-datos-comunes)
3. [Definir Esquemas en PySpark](#3-definir-esquemas-en-pyspark)
   - 3.1 [Definir un Esquema Manualmente](#31-definir-un-esquema-manualmente)
   - 3.2 [Inferencia de Esquema](#32-inferencia-de-esquema)
   - 3.3 [Consideraciones](#33-consideraciones)
4. [Resumen](#4-resumen)

## 1. Conversión de Tipos con `cast()`
La función `cast()` se utiliza en PySpark para convertir el tipo de datos de una columna en un DataFrame a otro tipo específico. Esta conversión es importante cuando los datos están en un tipo que no permite ciertas operaciones, como cuando una columna de precios está almacenada como cadena (`string`) y queremos realizar cálculos matemáticos.

### 1.1 Ejemplo de Uso:
```python
from pyspark.sql import functions as F

# Convertir la columna "price" de string a double
new_df = df.withColumn("price", F.col("price").cast("double"))
```
En este ejemplo, la columna `price` se convierte al tipo `double`, lo cual es necesario para realizar cálculos como multiplicaciones o sumas.

### 1.2 Tipos de Datos Comunes para `cast()`
- **`IntegerType()`**: Se utiliza para convertir una columna al tipo entero.
- **`DoubleType()`**: Se usa para convertir una columna a un número decimal de doble precisión.
- **`StringType()`**: Convierte una columna a cadena de caracteres.
- **`BooleanType()`**: Para convertir una columna a verdadero o falso.

Es importante tener en cuenta que si los valores no son compatibles con el tipo especificado (por ejemplo, intentar convertir un valor no numérico a `double`), el resultado de la conversión será `null`.

## 2. Tipos de Datos en PySpark
PySpark proporciona varios tipos de datos que se pueden utilizar para definir columnas en un DataFrame. Estos tipos de datos son equivalentes a los tipos de datos en otros lenguajes de programación y bases de datos.

### 2.1 Tipos de Datos Comunes:
- **`StringType`**: Representa texto o cadenas de caracteres.
- **`IntegerType`**: Representa números enteros.
- **`LongType`**: Representa números enteros grandes.
- **`FloatType`** y **`DoubleType`**: Representan números con punto decimal. `DoubleType` tiene mayor precisión.
- **`BooleanType`**: Representa valores lógicos (`True` o `False`).
- **`DateType`** y **`TimestampType`**: Representan fechas y fechas con horas, respectivamente.

Estos tipos de datos se utilizan para definir esquemas y garantizar que los datos se procesen de manera correcta y eficiente.

## 3. Definir Esquemas en PySpark
Un esquema define la estructura del DataFrame, especificando los nombres de las columnas y sus tipos de datos. Definir esquemas es importante para garantizar la consistencia y el correcto procesamiento de los datos, especialmente cuando se cargan datos de fuentes como archivos CSV.

### 3.1 Definir un Esquema Manualmente:
Puedes definir un esquema utilizando `StructType` y `StructField`:

```python
from pyspark.sql.types import StructType, StructField, StringType, IntegerType, DoubleType

# Definir el esquema manualmente
schema = StructType([
    StructField("order_id", StringType(), True),
    StructField("product_name", StringType(), True),
    StructField("customer_id", StringType(), True),
    StructField("date", StringType(), True),
    StructField("quantity", IntegerType(), True),
    StructField("price", DoubleType(), True),
    StructField("discount", DoubleType(), True)
])

# Cargar el DataFrame con el esquema definido
df = spark.read.format("csv").option("header", "true").schema(schema).load("path/to/file.csv")
```
En este ejemplo, se define un esquema para un archivo CSV, especificando los tipos de datos de cada columna. Esto garantiza que los datos se interpreten correctamente al cargarlos.

### 3.2 Inferencia de Esquema
Cuando no se especifica un esquema, PySpark puede intentar inferir el esquema automáticamente si se utiliza la opción `.option("inferSchema", "true")`. Esto es útil para evitar definir manualmente el esquema, pero puede ser menos preciso si los datos son inconsistentes o tienen valores atípicos.

### 3.3 Consideraciones:
- Definir esquemas manualmente proporciona mayor control y previene errores que podrían surgir al inferir tipos incorrectos.
- La inferencia de esquemas puede ser más conveniente para cargas rápidas, pero podría no ser ideal si los datos contienen errores o son complejos.

## 4. Resumen
- **`cast()`** se usa para convertir el tipo de una columna a otro tipo, necesario para operaciones matemáticas y manipulación de datos específica.
- **Tipos de Datos**: PySpark ofrece una variedad de tipos de datos (`StringType`, `IntegerType`, `DoubleType`, etc.) para definir y trabajar con columnas de un DataFrame.
- **Definir Esquemas**: Puedes definir esquemas manualmente para garantizar la consistencia de los datos o inferirlos automáticamente para mayor comodidad.

Definir esquemas y usar `cast()` correctamente es crucial para garantizar que los datos se manejen de manera eficiente y precisa en PySpark, evitando problemas relacionados con tipos incompatibles o valores nulos inesperados.

