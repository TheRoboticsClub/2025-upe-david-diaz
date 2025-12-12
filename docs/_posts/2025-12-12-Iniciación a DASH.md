[Dash](https://dash.plotly.com) es un framework en Python para el desarrollo
de páginas web interactivas, está integrada con [Plotly](https://plotly.com/)
y se puede usar para generar gráficos interactivos.

A continuación se muestra una pequeña aplicación DASH con algunos de los muchos
componentes que nos ofrecen estos paquetes Python.

```python
import dash
import dash_ag_grid as dag  # To display tables
import dash_bootstrap_components as dbc  # Bootstrap components
import pandas as pd  # For loading data
import plotly.express as px  # For graphics

from dash import html  # Main Dash app
from dash import dcc, Output, Input  # For interactivity

external_stylesheets = [
    'url_to_stylesheet',
    dbc.themes.BOOTSTRAP
]  # To use CSS in the app

my_input = dcc.Input(id='input', type='number', value=1, min=-2, max=2, step=0.5)

data = df = pd.DataFrame({
    "Fruit": ["Apples", "Oranges", "Bananas", "Apples", "Oranges", "Bananas"],
    "Amount": [4, 1, 2, 2, 4, 5],
    "City": ["SF", "SF", "SF", "Montreal", "Montreal", "Montreal"]
})
fig = px.bar(data, x="Fruit", y="Amount", color="City", barmode="group")  # Plotly bar chart

app = dash.Dash(__name__, external_stylesheets=external_stylesheets, title='My first DASH app')

app.layout = [
    html.H1(  # HTML h1
        children="Application made with DASH in Python.",  # Content
        style={'textAlign': 'center'}  # Style (CSS)
    ),

    html.Div(children=[  # HTML div
        html.Div(children="Inputs:"),
        dcc.Dropdown(  # HTML dropdown
            id='dropdown',  # For reference
            options=[  # Different options of the dropdown
                {'label': "Number 1", 'value': 1},
                {'label': "Number 2", 'value': 2},
                {'label': "Number 3", 'value': 3},
            ],
            multi=False,  # Only one option can be selected at the same time
            value=1,  # Default value
            style={'width': '30%'}
        ),
        my_input,  # Created before
        html.Div(id='output'),
    ], style={'margin-left': '2%'}),

    dcc.Markdown(  # Markdown format
        children="""
        ## This is a markdown text
        With URL to [DASH page](https://dash.plotly.com/)
        """,
        style={'textAlign': 'center'}
    ),

    html.H3(children='Graphics', style={'font-weight': 'bold', 'textAlign': 'center'}),  # HTML h3
    dcc.Graph(  # Graphic
        id='graph1',
        figure={  # Configuration of the graphs
            'data': [
                {'x': [1, 2, 3], 'y': [1, 2, 2], 'type': 'bar'}  # Bar chart without using plotly
            ]
        }
    ),
    dcc.Graph(
        id='graph2',
        figure={
            'data': [
                {'x': [1, 2, 3], 'y': [1, 2, 2]}  # Simple line chart without using plotly
            ]
        }
    ),

    html.Div(
        id='table',
        children=[
            dag.AgGrid(  # Add table
                rowData=data.to_dict("records"),  # Data
                columnDefs=[  # Name of the columns
                    {'field': 'Fruit'},
                    {'field': 'Amount'},
                    {'field': 'City'},
                ],
                columnSize="sizeToFit",  # To fit the columns in the space (width)
            ),
            html.H3(
                children='Figure with Plotly.express',
                style={'font-weight': 'bold', 'textAlign': 'center', 'margin-top': '2%'}  # Style only for this text
            ),
            dcc.Graph(
                id='example-graph',
                figure=fig  # Plotly figure
            )
        ],
        style={'width': '80%', 'margin': 'auto'}
    ),
]

@app.callback(
    Output('output', 'children'),
    [Input('dropdown', 'value'),
     Input(my_input, 'value')]  # Pass the object instead of id
)
def update_output(dropdown_value, input_value):
    """Update the output depending on the dropdown_value + input_value."""

    if not dropdown_value:
        return 'Dropdown value not selected.'
    return f'The sum of the inputs is: {dropdown_value + input_value}.'

if __name__ == "__main__":
    app.run(debug=True)
```

Todos los componentes de nuestra aplicación web interactiva con DASH están dentro
del *layout* de la aplicación, y la actualización de las interacciones del usuario
en la página están en las funciones exteriores (en este caso solamente 1).  
Dash también nos permite usar hojas de estilo (CSS) para la página, además de la
posibilidad de usar Bootstrap para el diseño de ésta.

Combinando este framework con otros como plotly.express y pandas, tenemos muchísimas
opciones para crear e implementar gráficos con los que el usuario pueda interactuar,
mediante *dropdowns*, listas, radios, texto, tablas, ...