### Para que el frontend se conecte con el RB local, granja o Google Cloud

Usaremos el **formulario que hay actualmente en la página de Unibotics**,
eligiendo el RoboticsBackend que queremos utilizar.

- Podemos usar una **variable de entorno** (por ejemplo *RB_URL*) para
identificar la URL del RoboticsBackend (sea local, granja o Google Cloud).
- Añadir una columna más a la base de datos de Usuarios para la URL del backend
(pero el tamaño de la base de datos aumentará con el número de usuarios).

Dependiendo del valor de esa URL usaremos HTTP (local) o HTTPS (granja y Google
Cloud).

### Información que debe guardarse para que un usuario use su Google Cloud para el RB
Credenciales del usuario, región de despliegue; asignar la máquina creada al usuario.
Habría que redireccionar al usuario a la página de Google Cloud para que inicie sesión
y, una vez iniciada la sesión, pedir un *access_token* (para poder crear e iniciar la
Máquina Virtual en nombre del usuario) y un *refresh_token* (para renovar el token de
acceso cuando haya caducado).  
Mínimo habrá que almacenar el ID de usuario, y el *access_token*, si queremos más detalles
habrá que almacenar la región de despliegue, el tipo de máquina elegida, ... (si no
podemos establecer unas por defecto).

Almacenar:
```text
ID_PROYECTO
NOMBRE_MV
ZONA
```

Usar la consola de Google Cloud en terminal local:
```shell
# Hay que tener descargada la consola previamente
gcloud auth login  # Abre ventana en el navegador para que el usuario inicie sesión

gcloud projects create <ID_PROYECTO> --name="<NOMBRE_PROYECTO>"  # El ID_PROYECTO debe ser único
# Si el usuario ya tiene un proyecto creado es más fácil porque Google Cloud asigna
# automáticamente un ID_PROYECTO, y desde el terminal podemos directamente acceder a él

# Obtenemos el ID del proyecto
PROYECT_ID=$(gcloud projects list --filter="name='<NOMBRE_PROYECTO>'" --format="value(projectId)")

gcloud config set project $PROYECT_ID

# Creamos la Máquina Virtual
gcloud services enable compute.googleapis.com  # Asegurarse de que el servicio está activo
gcloud config set compute/zone <ZONA>  # Zona por defecto (p.e. europe-southwest1-a, Madrid, zona A)

gcloud compute addresses create <NOMBRE_IP_ESTATICA> --region=<REGION>  # Región (p.e. europe-southwest1)

gcloud compute instances create <NOMBRE_MV> \
    --address=<NOMBRE_IP_ESTATICA> \  # Para que la IP no cambie cuando se detenga la máquina
    --machine-type=<TIPO_DE_MÁQIUINA> \  # Por ejemplo e2-micro
    --image-family=ubuntu-2404-lts \  # Ubuntu 24.04 LTS
    --image-project=ubuntu-os-cloud \
    --tags=robotics-backend \
    --metadata startup-script='  # Script que se ejecutará al crear la máquina virtual
    #!/bin/bash
    sudo apt update
    
    # Instalamos Docker
    sudo apt install docker.io -y
    
    # Descargamos los elementos principales para ejecutar el RoboticsBackend
    sudo docker run -d --restart=always -p 0.0.0.0:23750:2375 \
    -v /var/run/docker.sock:/var/run/docker.sock  alpine/socat \
    tcp-listen:2375,fork,reuseaddr unix-connect:/var/run/docker.sock
    
    sudo docker pull jderobot/robotics-backend:latest
    
    # Ejecutamos el RoboticsBackend
    sudo docker run --rm -it -v /usr/lib/wsl:/usr/lib/wsl \
        -e LD_LIBRARY_PATH=/usr/lib/wsl/lib \
        -p 6080-6090:6080-6090 -p 7163:7163 \
        jderobot/robotics-backend:latest
    
    # Avismos al usuario de que todo ha ido bien
    curl <URL_MI_SERVIDOR/remote-rb-ok/>  # Al acceder a está página devolver HTML
    '

gcloud compute firewall-rules create allow-rb-ports \
    --allow=tcp:6080-6090,tcp:7163,tcp:23750 \
    --target-tags=robotics-backend \
    --description="Allow RoboticsBackend ports."
```

Para detener la MV:
```shell
gcloud compute instances stop <NOMBRE_MV> \
    --zone=<ZONA> \
    --project=<ID_PROYECTO> 
```

Para iniciar la MV:
```shell
gcloud compute instances start <NOMBRE_MV> \
    --zone=<ZONA> \
    --project=<ID_PROYECTO> 
```

### DASH
Iniciación a DASH.
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

Visualización del código DASH de unibotics-webserver. Prueba de los elementos
DASH de unibotics-webserver. Visualización de las URL. Nueva versión de DASH.
Análisis del nuevo código de unibotics_dash, errores reportados de app.py.
Ejecución del DASH de unibotics-webserver (despliegue D1C), apertura de Issue
para reportar *requirements* faltantes.
Cambios en el código de app.py, **Pull Request** de los requirements que faltaban
y de los cambios en app.py.

### Videos
Subidos los vídeos sobre el despliegue de RoboticsBackend en las nubes de AWS,
Azure y Google Cloud; y también sobre la verificación de que se puede usar
Unibotics con el RB en estas nubes (vídeos en oculto, solo se pueden ver con
el enlace); subidos los enlaces a la documentación (en el fichero videotutorials)
con un Pull Request creado.

### Webadmin
Análisis de parte del código de unibotics/common (models.py, admin_site.py, ...),
creación de nueva rama (primeros cambios, ...)

### Espacio de usuario
Creación de un apartado en la página del perfil de usuario donde muestra una barra
de progreso con el espacio que ocupa el usuario, además de los ejercicios vistos por
el usuario y los proyectos que tiene (con el tamaño que ocupa cada uno).
Primer despliegue en D2 para probar que a los usuarios les sale su espacio consumido
en S3.