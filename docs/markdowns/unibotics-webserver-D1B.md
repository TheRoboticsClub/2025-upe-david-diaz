# Conectarse con el RoboticsBackend corriendo en la nube con unibotics-webserver D1B

Una vez ya hemos conseguido desplegar y ejecutar el RoboticsBackend en la nube
de Google Cloud, con Google Compute Engine, el último paso es conectarse a ese
RoboticsBackend corriendo en remoto, para ello habrá que hacer:

### Revisar base de datos del webserver
Para comprobar si la creación de la base de datos del contenedor Docker ha sido exitosa
habrá que abrir el terminal del contenedor de la base de datos de Docker desktop y hacer:
```shell
psql -U user-dev -d academy_db
```
Una vez hemos accedido a la base de datos miraremos si las migraciones de Django se han
completado bien, para ello ejecutaremos:
```shell
\d academy_dockerfarm
```
En caso de que la base de datos no esté comppleta habrá que borrar las migraciones de 
Django y volver a hacer el despliegue D1.

### Configurar Docker
Habrá que configurarlo para poder exponer la API por el puerto 2375. Para ello habrá que 
ir a *Settings* de Docker Desktop y seleccionar ``Expose daemon on tcp://localhost:2375
without TLS``.

### Configurar .env
Habrá que añadir el puerto a la dirección donde se ejecuta la conexión con el servidor,
para ello editaremos el fichero .env con el puerto, en este caso el puerto 8001.
```dotenv
SERVER=127.0.0.1:8001
```

### Contenedor para exponer API Docker (en el Docker remoto)
Para que desde la sección de la granja de la web se puedan lanzar contenedores Robotics
Backend desde las granjas que se hayan creado es necesario crear un contenedor en el
puerto 23750 que exponga la API en el puerto 2375. Para ello ejecutaremos:
```shell
# Usaremos 0.0.0.0 para que sea accesible externamente
docker run -d --restart=always -p 0.0.0.0:23750:2375 -v /var/run/docker.sock:/var/run/docker.sock  alpine/socat  tcp-listen:2375,fork,reuseaddr unix-connect:/var/run/docker.sock
```
> [!IMPORTANT]  
> Habrá que incluir el puerto 23750 en el firewall de la nube remota.

Iniciando automáticamente el contenedor cada vez que se abra Docker Desktop, permitiendo
que la web pueda lanzar Robotics Backends a las granjas.  
Para comprobar que se está ejecutando podemos usar **en WSL**:
```shell
echo "export DOCKER_HOST=tcp://<IP_DOCKER_REMOTO>:2375" >> ~/.bashrc && source ~/.bashrc
sudo nc -zv 127.0.0.1 23750
```
y debería de salir ``Conection to 127.0.0.1 23750 port [tcp/*] succeed!``.  
Para comprobar si el servicio se está ejecutando en otro ordenador habrá que usar su
dirección IP en el comando ``sudo nc -zc <IP_DIR> 23750``.

### Crear contenedor redis
Para que el contenedor RB de una granja se conecte con un ejercicio del webserver,
habrá que crear un contenedor con una imagen redis que haga de puente para esa conexión,
para ello ejecutaremos:
```shell
docker run -d -p 6379:6379 redis
```
Si el puerto ya está en uso podemos usar otro, por ejemplo:
```shell
docker run -d -p 6380:6379 redis
```
Antes habrá que hacer un cambio en settings.py, en CHANNEL_LAYERS
```
"CONFIG": {
            "hosts": [("127.0.0.1", 6380)],  # El puerto a la izquierda del :
        },
```

### Ejecutando el webserver
Ahora abriremos 2 terminales y ejecutaremos:
```shell
# En 1 terminal (iniciar webserver)
python manage.py runserver 7164
```
```shell
# En otro terminal (daphne para que los ejercicios se conecten con el RB de la granja)
# En el directorio unibotics-webserver/unibotics
python -m daphne -b 0.0.0.0 -p 8001 common.asgi:application
```
Ahora accederemos a la web en http://127.0.0.1:7164/ (entrando con admin admin) y
en el menú desplegable acceder a *Farm Info*, **teniendo el RB remoto activado**
y le daremos a *Farm admin* y pondremos los puertos.  
Donde:
- **IP máquina:** puerto de la máquina remota donde ejecuta el RoboticsBackend.
- **Puerto webserver:** 8000
- **Puerto code:** 1905
- **Puerto GUI:** 2303
- **Puerto manager:** 8765
- **Puerto VNC:** 6080
- **Puerto ejercicio 1:** 7681
- **Puerto ejercicio 2:** 1108
- **El segundo puerto 8080:** 5900

Y le damos a *Añadir máquina*.