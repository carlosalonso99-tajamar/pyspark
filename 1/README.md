# README: Primeros Pasos con Spark

Este documento cubre los conceptos fundamentales de Apache Spark, específicamente enfocados en las diferencias y el uso de `SparkContext` y `SparkSession`, así como la manipulación y transformación de RDDs. A continuación se presenta un resumen detallado del contenido del notebook, junto con algunos ejemplos sencillos.

## Contenidos del Notebook

### 1. Primeros Pasos con Spark
   Introducción a la configuración inicial de Apache Spark, incluyendo la creación de un contexto y una sesión de Spark.

### 2. SparkContext vs SparkSession
   Explicación sobre `SparkContext` y `SparkSession`:
   - **SparkContext**: Punto de entrada a Spark desde versiones 1.x para la creación de RDD, acumuladores y variables broadcast.
   - **SparkSession**: Introducido en la versión 2.0, proporciona un punto de entrada unificado para trabajar con RDD, DataFrames y Datasets.

### 3. Creación de SparkContext y SparkSession
   Ejemplos prácticos de cómo crear programáticamente un `SparkContext` y una `SparkSession`.

### 4. Trabajando con RDDs
   Introducción al concepto de RDDs (Resilient Distributed Datasets) y cómo crearlos a partir de listas o archivos.
   - **Creación de RDD a partir de listas**
   - **Acciones y transformaciones básicas en RDD**

### 5. Filtrado y Transformación de RDDs
   Ejercicios prácticos sobre cómo aplicar transformaciones y filtrados en RDDs:
   - Filtrar ciudades que contienen una letra específica.
   - Contar la aparición de caracteres en los elementos de un RDD.

### 6. Reorganización de Datos en RDDs
   Ejercicios para trabajar con datos desordenados y listas anidadas.
   - Reorganizar listas anidadas y contar elementos.

### 7. Muestra de Elementos y Ordenación
   Ejercicio para obtener una muestra de elementos sin repetición y ordenados de forma descendente.

### 8. Operaciones de Transformación Avanzadas en RDDs
   - **Map y flatMap**: Ejemplos sobre cómo transformar elementos de un RDD.
   - **Operaciones de mapeo con múltiples índices**.

### 9. Operaciones de Agrupación y Reducción en RDDs
   - **groupByKey()** y **reduceByKey()**: Ejemplos para agrupar y reducir datos.
   - **sortByKey()**: Cómo ordenar los datos por una clave específica.

### 10. Operaciones de Particionamiento
   - **Repartición y Coalescencia**: Cómo modificar el número de particiones para optimizar el procesamiento de los RDDs.
   - **mapPartitions** y **mapPartitionsWithIndex**: Ejemplos para trabajar con particiones específicas y optimizar el rendimiento.

## Ejemplos Sencillos

### Crear un SparkContext
```python
from pyspark import SparkContext
sc = SparkContext.getOrCreate()
```
Este ejemplo muestra cómo crear o recuperar un `SparkContext` existente.

### Crear un SparkSession
```python
from pyspark.sql import SparkSession
spark = SparkSession.builder.getOrCreate()
```
Este ejemplo muestra cómo crear o recuperar una `SparkSession` utilizando el patrón builder.

### Crear un RDD a partir de una lista
```python
rdd = sc.parallelize(['Alicante', 'Elche', 'Valencia', 'Madrid', 'Barcelona'])
```
Crea un RDD a partir de una lista de ciudades.

### Filtrar elementos en un RDD
```python
ciudades_con_e = rdd.filter(lambda x: 'e' in x)
ciudades_con_e.collect()  # ['Elche', 'Valencia', 'Barcelona']
```
Filtra las ciudades que contienen la letra 'e'.

### Contar la aparición de un carácter en los elementos del RDD
```python
ciudades_y_contador = rdd.map(lambda x: (x, x.count('e')))
ciudades_y_contador.collect()  # [('Alicante', 1), ('Elche', 2), ('Valencia', 1), ('Madrid', 0), ('Barcelona', 1)]
```
Devuelve cada ciudad junto con el número de veces que aparece la letra 'e'.

### Reorganizar listas anidadas
```python
ciudades_mal = [['Alicante.Elche', 'Valencia', 'Madrid.Barcelona', 'Bilbao.Sevilla'], ['Murcia', 'San Sebastián', 'Melilla.Aspe']]
rdd_ciudades_mal = sc.parallelize(ciudades_mal)
rdd_ciudades_bien = rdd_ciudades_mal.flatMap(lambda x: [ciudad for sublista in x for ciudad in sublista.split('.')])
rdd_ciudades_bien.collect()  # ['Alicante', 'Elche', 'Valencia', 'Madrid', 'Barcelona', 'Bilbao', 'Sevilla', 'Murcia', 'San Sebastián', 'Melilla', 'Aspe']
```
Reorganiza y separa las ciudades que estaban mal formateadas.

### Obtener una muestra de elementos sin repetir
```python
lista = [4, 6, 34, 7, 9, 2, 3, 4, 4, 21, 4, 6, 8, 9, 7, 8, 5, 4, 3, 22, 34, 56, 98]
rdd_lista = sc.parallelize(lista)
muestra_unica = rdd_lista.distinct().takeSample(False, 5)
muestra_unica  # Ejemplo: [4, 7, 34, 9, 56]
```
Obtiene una muestra de 5 elementos únicos de la lista.

### Operaciones de particionamiento
```python
rdd = sc.parallelize([1, 1, 2, 2, 3, 3, 4, 5], 3)
print(rdd.getNumPartitions())  # 3

rdd_coalesced = rdd.coalesce(1)
print(rdd_coalesced.getNumPartitions())  # 1
```
Modifica el número de particiones de un RDD para mejorar el rendimiento.

### Operación con `mapPartitionsWithIndex`
```python
def mpwi(indice, iterador):
    return [(indice, list(iterador))]

resultado_rdd = rdd.mapPartitionsWithIndex(mpwi)
resultado_rdd.collect()  # [(0, [1, 1, 2]), (1, [2, 3, 3]), (2, [4, 5])]
```
Devuelve cada partición del RDD junto con su índice.

