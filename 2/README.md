# README: Operaciones Avanzadas con Spark

Este documento abarca ejemplos prácticos de operaciones avanzadas en Apache Spark, cubriendo desde transformaciones y agregaciones en DataFrames hasta la limpieza de datos y el uso de diferentes tipos de datos. A continuación, se presenta un resumen detallado del contenido del notebook, junto con ejemplos representativos.

## Contenidos del Notebook

### 1. Introducción a Operaciones con DataFrames
   Configuración inicial de un DataFrame con datos ficticios de personas para ilustrar cómo realizar diferentes transformaciones sobre los datos.

### 2. Transformaciones y Creación de Nuevas Columnas
   - **Cálculo de Columna `Mayor30`**: Añadir una columna que indique si la persona es mayor de 30 años.
   - **Cálculo de `FaltanJubilacion`**: Añadir una columna que calcule cuántos años faltan para la jubilación (suponiendo la jubilación a los 67 años).
   - **Añadir Columna Constante `Apellidos`**: Crear una columna con un valor constante (`XYZ`) utilizando `lit()`.
   - **Eliminación de Columnas**: Eliminar columnas innecesarias (`Mayor30` y `Apellidos`).
   - **Añadir Año de Nacimiento**: Crear una columna (`AnyoNac`) con el año de nacimiento calculado a partir de la edad.
   - **Añadir Identificador Incremental**: Añadir una columna `Id` incremental utilizando `monotonically_increasing_id()`.

### 3. Limpieza de Datos con `VentasNulos.csv`
   A partir de un archivo CSV con valores nulos:
   - **Eliminar Filas con Múltiples Nulos**: Eliminar filas que tienen al menos 4 valores nulos.
   - **Sustitución de Valores Nulos**:
     - Rellenar nombres nulos con `"Empleado"`.
     - Sustituir las ventas nulas por la media de ventas de los compañeros, redondeado a entero.
     - Rellenar los euros nulos con el valor del compañero que menos ha ganado.

### 4. Lectura y Limpieza de Datos desde Archivo TSV (`movies.tsv`)
   - **Lectura de Datos TSV**: Leer datos de películas desde un archivo TSV y definir un esquema explícito con `StructType`.
   - **Filtrado de Datos**: Mostrar las películas en las que ha trabajado Eddie Murphy.
   - **Operaciones de Intersección**: Mostrar los intérpretes que han trabajado tanto en "Superman" como en "Superman II".

## Ejemplos Sencillos

### Añadir una Columna que Indique si la Persona es Mayor de 30 Años
```python
from pyspark.sql.functions import when

df = df.withColumn("Mayor30", when(df["Edad"] > 30, True).otherwise(False))
df.show()
```
Este ejemplo añade una columna que indica si la persona es mayor de 30 años.

### Calcular Años Restantes para la Jubilación
```python
df = df.withColumn("FaltanJubilacion", 67 - df["Edad"])
df.show()
```
Añade una columna que calcula cuántos años le faltan a cada persona para jubilarse (suponiendo la jubilación a los 67 años).

### Añadir un Identificador Incremental
```python
from pyspark.sql.functions import monotonically_increasing_id

df = df.withColumn("Id", monotonically_increasing_id())
df.show()
```
Añade una columna `Id` con un identificador incremental único para cada fila.

### Eliminar Filas con Más de 3 Valores Nulos
```python
df = df.dropna(thresh=3)
df.show()
```
Elimina las filas que tienen al menos 4 valores nulos, asegurando que se mantengan los registros con suficiente información.

### Sustituir Ventas Nulas por la Media de Ventas de los Compañeros
```python
from pyspark.sql.functions import avg

media_ventas = df.select(avg(df["Ventas"])).collect()[0][0]
df = df.fillna(int(media_ventas), subset=["Ventas"])
df.show()
```
Este ejemplo reemplaza las ventas nulas por la media de las ventas de los compañeros, redondeada a un valor entero.

### Leer Datos de Películas desde un Archivo TSV con Esquema Personalizado
```python
from pyspark.sql.types import StructType, StructField, StringType, IntegerType

schema = StructType([
    StructField("interprete", StringType(), True),
    StructField("pelicula", StringType(), True),
    StructField("anyo", IntegerType(), True)
])

df = (spark.read.format("csv")
      .option("delimiter", "\t")
      .option("header", "false")
      .schema(schema)
      .load("dbfs:/FileStore/shared_uploads/carlosalonsomingo@gmail.com/movies.tsv"))
df.show()
```
Define un esquema explícito para leer datos de un archivo TSV y cargarlos en un DataFrame.

### Filtrar Películas de Eddie Murphy
```python
df_eddie = df.filter(df["interprete"] == "Murphy, Eddie (I)").select("pelicula")
df_eddie.show()
```
Filtra las películas en las que ha trabajado el intérprete Eddie Murphy.

### Mostrar Intérpretes que Aparecen en Ambas Películas: "Superman" y "Superman II"
```python
df_superman = df.filter(df["pelicula"] == "Superman").select("interprete")
df_superman_ii = df.filter(df["pelicula"] == "Superman II").select("interprete")

interpretes_ambas = df_superman.intersect(df_superman_ii)
interpretes_ambas.show()
```
Utiliza `intersect()` para mostrar los intérpretes que aparecen tanto en "Superman" como en "Superman II".

---

Este README proporciona una guía exhaustiva de las operaciones realizadas en el notebook, con ejemplos prácticos que ilustran cómo trabajar con datos utilizando Apache Spark. Si necesitas más detalles o ejemplos adicionales, ¡no dudes en pedírmelo! 😊🚀

