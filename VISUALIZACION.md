# Documentación: Visualización con Seaborn y Matplotlib

En esta guía se presentarán los conceptos clave de visualización de datos utilizando dos de las bibliotecas más populares en Python: **Seaborn** y **Matplotlib**. Ambas bibliotecas permiten crear gráficos personalizados y atractivos para analizar datos de manera visual.

## Tabla de Contenidos
1. [Introducción a Matplotlib](#1-introduccion-a-matplotlib)
   - 1.1 [Ejemplo de Uso Básico](#11-ejemplo-de-uso-basico)
   - 1.2 [Personalización de Gráficos](#12-personalizacion-de-graficos)
2. [Introducción a Seaborn](#2-introduccion-a-seaborn)
   - 2.1 [Ejemplo de Uso Básico](#21-ejemplo-de-uso-basico)
   - 2.2 [Gráficos Comunes en Seaborn](#22-graficos-comunes-en-seaborn)
3. [Comparación: Matplotlib vs. Seaborn](#3-comparacion-matplotlib-vs-seaborn)
4. [Ejemplos Prácticos](#4-ejemplos-practicos)
   - 4.1 [Gráficos de Líneas](#41-graficos-de-lineas)
   - 4.2 [Gráficos de Barras](#42-graficos-de-barras)
   - 4.3 [Diagramas de Caja](#43-diagramas-de-caja)
5. [Funciones para Facilitar la Graficación](#5-funciones-para-facilitar-la-graficacion)
6. [Resumen](#6-resumen)

## 1. Introducción a Matplotlib
**Matplotlib** es una biblioteca de visualización muy versátil que permite crear gráficos desde simples hasta complejos. Es ideal para el control preciso sobre todos los elementos de la visualización y es compatible con una variedad de tipos de gráficos.

### 1.1 Ejemplo de Uso Básico
```python
import matplotlib.pyplot as plt

# Datos de ejemplo
days = ['Lunes', 'Martes', 'Miércoles', 'Jueves', 'Viernes']
values = [5, 7, 6, 8, 7]

# Crear un gráfico de líneas
plt.plot(days, values)
plt.xlabel('Días de la Semana')
plt.ylabel('Cantidad de Actividad')
plt.title('Actividad a lo Largo de la Semana')
plt.show()
```
En este ejemplo, se utiliza `plt.plot()` para crear un gráfico de líneas. `plt.show()` se utiliza para mostrar el gráfico.

### 1.2 Personalización de Gráficos
**Matplotlib** permite personalizar cada aspecto del gráfico, incluyendo colores, líneas, leyendas y mucho más:

```python
# Gráfico de líneas personalizado
plt.plot(days, values, color='red', linestyle='--', marker='o')
plt.xlabel('Días de la Semana')
plt.ylabel('Cantidad de Actividad')
plt.title('Actividad a lo Largo de la Semana (Personalizado)')
plt.grid(True)
plt.legend(['Actividad'])
plt.show()
```

## 2. Introducción a Seaborn
**Seaborn** está construido sobre **Matplotlib** y proporciona una interfaz de alto nivel para crear gráficos estadísticos de una manera más fácil y atractiva. Incluye funciones para visualizar distribuciones, relaciones y categorías.

### 2.1 Ejemplo de Uso Básico
```python
import seaborn as sns
import matplotlib.pyplot as plt

# Datos de ejemplo
iris = sns.load_dataset('iris')

# Crear un gráfico de dispersión
sns.scatterplot(data=iris, x='sepal_length', y='sepal_width', hue='species')
plt.title('Gráfico de Dispersión de Iris')
plt.show()
```
En este ejemplo, **Seaborn** se utiliza para crear un gráfico de dispersión donde las especies se distinguen por colores usando el argumento `hue`.

### 2.2 Gráficos Comunes en Seaborn
Algunos de los gráficos más comunes que se pueden crear con **Seaborn** incluyen:
- **`sns.barplot()`**: Gráfico de barras.
- **`sns.boxplot()`**: Diagrama de caja para mostrar distribuciones.
- **`sns.heatmap()`**: Mapa de calor para visualizaciones de correlación.
- **`sns.pairplot()`**: Gráfico que muestra relaciones entre todas las combinaciones de variables en un dataset.

## 3. Comparación: Matplotlib vs. Seaborn
- **Matplotlib** ofrece un control muy detallado sobre los elementos del gráfico, lo cual es ideal para personalizaciones avanzadas.
- **Seaborn** facilita la creación de gráficos estadísticos y es más rápido para crear visualizaciones complejas con poco código.
- **Seaborn** es mejor para visualizaciones que requieren una representación clara y visualmente atractiva de datos estadísticos, mientras que **Matplotlib** es útil cuando se necesita precisión y personalización completa.

## 4. Ejemplos Prácticos
### 4.1 Gráficos de Líneas
```python
plt.plot([1, 2, 3, 4], [10, 20, 25, 30], label='Serie A')
plt.plot([1, 2, 3, 4], [15, 18, 22, 28], label='Serie B')
plt.xlabel('X')
plt.ylabel('Y')
plt.title('Gráfico de Líneas')
plt.legend()
plt.show()
```

### 4.2 Gráficos de Barras
```python
sns.barplot(x=['A', 'B', 'C', 'D'], y=[5, 7, 8, 6])
plt.title('Gráfico de Barras con Seaborn')
plt.show()
```

### 4.3 Diagramas de Caja
```python
sns.boxplot(data=iris, x='species', y='sepal_length')
plt.title('Diagrama de Caja de la Longitud del Sépalo por Especie')
plt.show()
```
# Funciones para Facilitar la Graficación

Para facilitar el proceso de creación de gráficos, aquí se presentan algunas funciones comunes que reutilizan configuraciones y simplifican el código. Estas funciones permiten crear visualizaciones de manera más rápida y con menos líneas de código utilizando **Matplotlib** y **Seaborn**.

## 5 Funciones para Facilitar la Graficación
## 5.1 Función para Gráfico de Líneas
```python
import matplotlib.pyplot as plt

def plot_line(df, x_col, y_col, xlabel='X', ylabel='Yd', title='Gráfico de Línea', color='blue', linestyle='-', marker='o'):
    x = df.select(x_col).rdd.flatMap(lambda x: x).collect()
    y = df.select(y_col).rdd.flatMap(lambda x: x).collect()
    plt.plot(x, y, color=color, linestyle=linestyle, marker=marker)
    plt.xlabel(xlabel)
    plt.ylabel(ylabel)
    plt.title(title)
    plt.grid(True)
    plt.show()

# Uso de la función (suponiendo que df_mas_vendidos es un DataFrame de PySpark)
plot_line(df_mas_vendidos, "StockCode", "Ventas", xlabel='Producto', ylabel='Ventas', title='Más Vendidos')
```

## 5.2 Función para Gráfico de Barras con Seaborn
```python
import seaborn as sns
import matplotlib.pyplot as plt

def plot_bar_seaborn(df, x_col, y_col, title="", x_label="", y_label="", figsize=(10, 6), palette="viridis"):
    x = df.select(x_col).rdd.flatMap(lambda x: x).collect()
    y = df.select(y_col).rdd.flatMap(lambda x: x).collect()
    plt.figure(figsize=figsize)
    sns.barplot(x=x, y=y, palette=palette)
    plt.title(title, fontsize=14)
    plt.xlabel(x_label, fontsize=12)
    plt.ylabel(y_label, fontsize=12)
    plt.xticks(rotation=45)
    plt.tight_layout()
    plt.show()

# Uso de la función (suponiendo que df_mas_vendidos es un DataFrame de PySpark)
plot_bar_seaborn(df_mas_vendidos, "StockCode", "Ventas", title='Más Vendidos', x_label='Producto', y_label='Ventas')
```

## 5.3 Función para Gráfico de Barras con Matplotlib
```python
import matplotlib.pyplot as plt

def plot_bar_matplotlib(df, x_col, y_col, title="", x_label="", y_label="", figsize=(10, 6), color="skyblue"):
    x = df.select(x_col).rdd.flatMap(lambda x: x).collect()
    y = df.select(y_col).rdd.flatMap(lambda x: x).collect()
    plt.figure(figsize=figsize)
    plt.bar(x, y, color=color)
    plt.title(title, fontsize=14)
    plt.xlabel(x_label, fontsize=12)
    plt.ylabel(y_label, fontsize=12)
    plt.xticks(rotation=45)
    plt.tight_layout()
    plt.show()

# Uso de la función (suponiendo que df_mas_vendidos es un DataFrame de PySpark)
plot_bar_matplotlib(df_mas_vendidos, "StockCode", "Ventas", title='Más Vendidos', x_label='Producto', y_label='Ventas')
```

## 5.4 Función para Gráfico de Dispersión con Seaborn
```python
import seaborn as sns
import matplotlib.pyplot as plt

def plot_scatter(df, x_col, y_col, hue=None, title='Gráfico de Dispersión', figsize=(10, 6)):
    data = df.toPandas()
    plt.figure(figsize=figsize)
    sns.scatterplot(data=data, x=x_col, y=y_col, hue=hue)
    plt.title(title, fontsize=14)
    plt.tight_layout()
    plt.show()

# Uso de la función (suponiendo que iris_df es un DataFrame de PySpark)
plot_scatter(iris_df, x_col='sepal_length', y_col='sepal_width', hue='species', title='Relación entre Largo y Ancho de Sépalo')
```

## 5.5 Función para Gráfico de Histograma
```python
import matplotlib.pyplot as plt

def plot_histogram(df, col, bins=10, title='Histograma', x_label='Valores', y_label='Frecuencia', color='blue', figsize=(10, 6)):
    data = df.select(col).rdd.flatMap(lambda x: x).collect()
    plt.figure(figsize=figsize)
    plt.hist(data, bins=bins, color=color, edgecolor='black')
    plt.title(title, fontsize=14)
    plt.xlabel(x_label, fontsize=12)
    plt.ylabel(y_label, fontsize=12)
    plt.tight_layout()
    plt.show()

# Uso de la función (suponiendo que df es un DataFrame de PySpark)
plot_histogram(df, 'Ventas', bins=15, title='Distribución de Ventas', x_label='Ventas')
```

## 5.6 Función para Gráfico de Heatmap con Seaborn
```python
import seaborn as sns
import matplotlib.pyplot as plt

def plot_heatmap(df, title='Mapa de Calor', figsize=(10, 8), cmap='viridis'):
    data = df.toPandas()
    plt.figure(figsize=figsize)
    sns.heatmap(data.corr(), annot=True, cmap=cmap)
    plt.title(title, fontsize=14)
    plt.tight_layout()
    plt.show()

# Uso de la función (suponiendo que df es un DataFrame de PySpark)
plot_heatmap(df, title='Mapa de Calor de Correlaciones')
```

## 5.7 Función para Gráfico de Pie con Matplotlib
```python
import matplotlib.pyplot as plt

def plot_pie(df, labels_col, values_col, title='Gráfico de Pie', figsize=(8, 8), autopct='%1.1f%%', startangle=140):
    labels = df.select(labels_col).rdd.flatMap(lambda x: x).collect()
    values = df.select(values_col).rdd.flatMap(lambda x: x).collect()
    plt.figure(figsize=figsize)
    plt.pie(values, labels=labels, autopct=autopct, startangle=startangle)
    plt.title(title, fontsize=14)
    plt.tight_layout()
    plt.show()

# Uso de la función (suponiendo que df es un DataFrame de PySpark)
plot_pie(df, labels_col='Producto', values_col='Ventas', title='Distribución de Ventas por Producto')
```

Estas funciones te ayudarán a graficar diferentes tipos de datos de manera más rápida y eficiente, utilizando tanto **Matplotlib** como **Seaborn** para aprovechar sus puntos fuertes en visualización.

