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
from dash import Dash, html  # Main Dash app
from dash import dcc, callback, Output, Input  # For interactivity
import dash_ag_grid as dag  # To display tables
import dash_bootstrap_components as dbc  # Bootstrap components

external_stylesheets = [
    'url_to_stylesheet',
    dbc.themes.CERULEAN
]  # To use CSS in the app

app = Dash.app(external_stylesheets=external_stylesheets)

app.layout = [
    html.Div(children="Hello world.")
]

if __name__ == "__main__":
    app.run(debug=True)
```

### Video