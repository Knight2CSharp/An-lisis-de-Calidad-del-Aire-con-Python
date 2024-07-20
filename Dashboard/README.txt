Dashboard de Calidad del Aire
Este proyecto crea un dashboard interactivo para visualizar datos de calidad del aire usando Dash y Plotly. Los datos se cargan desde un archivo CSV, se limpian y se visualizan a través de varias gráficas interactivas.

Requisitos
Para ejecutar esta aplicación, necesitarás las siguientes bibliotecas de Python:

dash
dash-bootstrap-components
plotly
pandas

Puedes instalar estas dependencias usando pip:
pip install dash dash-bootstrap-components plotly pandas

Descripción del Proyecto

Archivos
app.py: Contiene el código principal para ejecutar el dashboard.

Estructura del Código

Cargar y Limpiar Datos
El código carga un conjunto de datos desde un archivo CSV, reemplaza valores faltantes (-200) con NaN y convierte la columna Date a un formato datetime.

import dash
import dash_bootstrap_components as dbc
from dash import dcc, html
from dash.dependencies import Input, Output
import plotly.express as px
import pandas as pd

# Cargar el conjunto de datos
csv_file_path = '/home/uriel/app/AirQualityUCI.csv' # Reemplaza con la ruta a tu archivo CSV

data = pd.read_csv(csv_file_path, delimiter=';')

# Limpiar datos reemplazando -200 con NaN
data.replace(-200, pd.NA, inplace=True)

# Convertir la columna 'Date' a datetime
data['Date'] = pd.to_datetime(data['Date'], format='%d/%m/%Y')


Crear la Aplicación Dash

Se crea una aplicación Dash con un layout que incluye un título, un dropdown para seleccionar sensores, varios gráficos y un slider para seleccionar el año.

# Crear la aplicación Dash
app = dash.Dash(__name__, external_stylesheets=[dbc.themes.BOOTSTRAP])

# Definir el layout de la aplicación
app.layout = dbc.Container([
    dbc.Row([
        dbc.Col([
            html.H1("Dashboard de Calidad del Aire", className="text-center"),
        ], width=12)
    ]),
    dbc.Row([
        dbc.Col([
            dcc.Dropdown(
                id='sensor-dropdown',
                options=[
                    {'label': 'CO(GT)', 'value': 'CO(GT)'},
                    {'label': 'NMHC(GT)', 'value': 'NMHC(GT)'},
                    {'label': 'C6H6(GT)', 'value': 'C6H6(GT)'},
                    {'label': 'NOx(GT)', 'value': 'NOx(GT)'},
                    {'label': 'NO2(GT)', 'value': 'NO2(GT)'},
                    {'label': 'PT08.S1(CO)', 'value': 'PT08.S1(CO)'},
                    {'label': 'PT08.S2(NMHC)', 'value': 'PT08.S2(NMHC)'},
                    {'label': 'PT08.S3(NOx)', 'value': 'PT08.S3(NOx)'},
                    {'label': 'PT08.S4(NO2)', 'value': 'PT08.S4(NO2)'},
                    {'label': 'PT08.S5(O3)', 'value': 'PT08.S5(O3)'},
                    {'label': 'T', 'value': 'T'},
                    {'label': 'RH', 'value': 'RH'},
                    {'label': 'AH', 'value': 'AH'}
                ],
                value='CO(GT)',
                clearable=False,
                className='mb-3'
            ),
            dcc.Graph(id='time-series-graph'),
        ], width=12)
    ]),
    dbc.Row([
        dbc.Col([
            dcc.Graph(id='histogram-graph')
        ], width=6),
        dbc.Col([
            dcc.Graph(id='box-plot-graph')
        ], width=6),
    ]),
    dbc.Row([
        dbc.Col([
            dcc.Graph(id='scatter-graph')
        ], width=12),
    ]),
    dbc.Row([
        dbc.Col([
            dcc.Slider(
                id='year-slider',
                min=2004,
                max=2005,
                marks={i: str(i) for i in range(2004, 2006)},
                value=2004,
                step=1
            ),
        ], width=12)
    ]),
], fluid=True)


Callback para Actualizar Gráficas

La función de callback actualiza las gráficas basadas en el sensor seleccionado y el año.

# Callback para actualizar las gráficas en función del sensor seleccionado y el año
@app.callback(
    [Output('time-series-graph', 'figure'),
     Output('histogram-graph', 'figure'),
     Output('box-plot-graph', 'figure'),
     Output('scatter-graph', 'figure')],
    [Input('sensor-dropdown', 'value'),
     Input('year-slider', 'value')]
)
def update_graphs(selected_sensor, selected_year):
    # Filtrar datos por año
    filtered_data = data[data['Date'].dt.year == selected_year]

    # Crear gráfica de series de tiempo
    fig_time_series = px.line(filtered_data, x='Date', y=selected_sensor, title=f'Series de Tiempo para {selected_sensor} en {selected_year}')

    # Crear histograma
    fig_histogram = px.histogram(filtered_data, x=selected_sensor, title=f'Histograma de {selected_sensor} en {selected_year}')

    # Crear box plot
    fig_box_plot = px.box(filtered_data, y=selected_sensor, title=f'Box Plot de {selected_sensor} en {selected_year}')

    # Crear gráfico de dispersión (scatter plot) para mostrar la relación entre dos variables
    fig_scatter = px.scatter(filtered_data, x=selected_sensor, y='T', title=f'Scatter Plot de {selected_sensor} vs T en {selected_year}')

    return fig_time_series, fig_histogram, fig_box_plot, fig_scatter

Ejecutar la Aplicación

La aplicación se ejecuta en el host y puerto especificados.

# Ejecutar la aplicación
if __name__ == '__main__':
    app.run_server(host='0.0.0.0', port=8050)

Uso
Para ejecutar la aplicación:

Asegúrate de tener las dependencias instaladas.
Coloca el archivo app.py en tu directorio de trabajo.
Ejecuta el script:

python app.py


La aplicación estará disponible en http://0.0.0.0:8050.

Autores

Aldrin Castro
Uriel Junca
Emanuel Martinez
Eduadro Gutierrez