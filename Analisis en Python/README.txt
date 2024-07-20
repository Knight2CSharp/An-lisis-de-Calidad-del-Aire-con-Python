Análisis de Calidad del Aire con Python

Este proyecto analiza datos de calidad del aire utilizando técnicas de visualización y tratamiento de datos faltantes. El conjunto de datos proviene de un archivo Excel y se realizan diversas operaciones para manejar valores nulos e interpolar datos.

Requisitos

Para ejecutar este notebook, necesitarás las siguientes bibliotecas de Python:
•	pandas
•	matplotlib
•	numpy
•	scikit-learn

Puedes instalar estas dependencias utilizando pip:
pip install pandas matplotlib numpy scikit-learn

Descripción del Notebook

Celda 1: Cargar y mostrar los datos

import pandas as pd
import matplotlib.pyplot as plt
import numpy as np
from sklearn.linear_model import LogisticRegression
from sklearn.metrics import accuracy_score

# Ruta del archivo Excel
file_path = r'C:\Users\aldri\OneDrive\Escritorio\Analisis\bd\AirQualityUCI.xlsx'

# Leer la hoja de cálculo
df = pd.read_excel(file_path)

# Mostrar las primeras filas del DataFrame
df.head()



En esta celda, importamos las bibliotecas necesarias y cargamos los datos de un archivo Excel en un DataFrame de pandas. Luego, mostramos las primeras filas para verificar la carga correcta de los datos.


Celda 2: Graficar datos originales

# Asegurarse de que la columna 'Date' esté en formato datetime
df['Date'] = pd.to_datetime(df['Date'], errors='coerce')

# Lista de columnas para graficar
columns_to_plot = [
    'CO(GT)', 'PT08.S1(CO)', 'NMHC(GT)', 'C6H6(GT)',
    'PT08.S2(NMHC)', 'NOx(GT)', 'PT08.S3(NOx)', 'NO2(GT)',
    'PT08.S4(NO2)', 'PT08.S5(O3)', 'T', 'RH', 'AH'
]

# Graficar las columnas del DataFrame original
for column in columns_to_plot:
    df.plot(x='Date', y=column, kind='line')
    
    # Configurar título y etiquetas de la gráfica
    plt.title(f'{column} con el tiempo (Original)')
    plt.xlabel('Fecha')
    plt.ylabel(column)
    plt.grid(True)
    plt.show()

Convertimos la columna 'Date' a formato datetime y seleccionamos columnas específicas para graficar. Generamos gráficas de línea para cada columna seleccionada para visualizar su comportamiento en el tiempo.


Celda 3: Introducir valores nulos aleatorios

# Guardar una copia del DataFrame original
df_original = df.copy()

# Calcular los promedios de las columnas, excluyendo 'Date' y 'Time'
promedios_original = df_original.drop(columns=['Date', 'Time']).mean()

# Columnas a las que se les pueden agregar valores nulos
columns_to_modify = df.columns.difference(['Date', 'Time'])

# Añadir valores nulos aleatorios a las columnas seleccionadas
np.random.seed(0)  # Para reproducibilidad
for column in columns_to_modify:
    # Determinar el número de valores a reemplazar por nulos
    n_nulls = int(0.3 * len(df))  # Por ejemplo, el 30% de los valores
    null_indices = np.random.choice(df.index, n_nulls, replace=False)
    df.loc[null_indices, column] = np.nan

# Mostrar las primeras filas del DataFrame con valores nulos
df.head()

Guardamos una copia del DataFrame original y calculamos los promedios de las columnas, excluyendo 'Date' y 'Time'. Introducimos valores nulos aleatorios en un 30% de las filas de cada columna seleccionada.


Celda 4: Interpolación de valores nulos

# Convertir columnas de tipo 'object' a tipos más específicos
df_interpolated = df.copy()
df_interpolated = df_interpolated.infer_objects()

# Realizar la interpolación sencilla
df_interpolated = df_interpolated.interpolate()

# Asegurarse de que los NaN se han reemplazado usando 'ffill' y 'bfill'
df_interpolated = df_interpolated.ffill().bfill()

# Mostrar las primeras filas del DataFrame interpolado
df_interpolated.head()
Creamos una copia del DataFrame modificado y convertimos columnas de tipo 'object' a tipos más específicos. Realizamos una interpolación sencilla y utilizamos 'ffill' y 'bfill' para asegurar que no queden valores nulos.


Celda 5: Graficar datos interpolados

# Graficar las columnas interpoladas
for column in columns_to_plot:
    df_interpolated.plot(x='Date', y=column, kind='line')
    
    # Configurar título y etiquetas de la gráfica
    plt.title(f'{column} con el tiempo (Interpolado)')
    plt.xlabel('Fecha')
    plt.ylabel(column)
    plt.grid(True)
    plt.show()
Generamos gráficas de línea para las mismas columnas seleccionadas previamente, mostrando su comportamiento en el tiempo después de la interpolación.


Celda 6: Calcular y mostrar precisión del modelo

# Calcular la suma de los promedios del DataFrame original
suma_promedios_original = promedios_original.sum()

# Calcular la suma de los promedios del DataFrame interpolado
suma_promedios_interpolated = promedios_interpolated.sum() # type: ignore

# Calcular la precisión general como porcentaje
precision_global = 100 * (1 - abs(suma_promedios_original - suma_promedios_interpolated) / suma_promedios_original)

# Mostrar la precisión global
print(f"Precisión del modelo interpolados: {precision_global:.2f}%")

Calculamos la suma de los promedios de las columnas en el DataFrame original e interpolado. Usamos estas sumas para calcular la precisión del modelo interpolado como un porcentaje y la mostramos.


Celda 7: Comparar visualmente promedios originales e interpolados

# Comparar los promedios originales e interpolados visualmente
df_comparison = pd.DataFrame({'Original': promedios_original, 'Interpolado': promedios_interpolated})

# Graficar la comparación
df_comparison

Autores

Aldrin Castro
Uriel Junca
Emanuel Martinez
Eduadro Gutierrez
