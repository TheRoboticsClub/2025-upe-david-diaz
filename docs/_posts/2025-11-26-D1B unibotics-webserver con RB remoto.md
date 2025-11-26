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
\d academy_dockerfarm  # Antiguo
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

### Contenedor para exponer API Docker
Para que desde la sección de la granja de la web se puedan lanzar contenedores Robotics
Backend desde las granjas que se hayan creado es necesario crear un contenedor en el
puerto 23750 que exponga la API en el puerto 2375. Para ello ejecutaremos **en la máquina
remota**:
```shell
# Usaremos 0.0.0.0 para que sea accesible externamente
docker run -d --restart=always -p 0.0.0.0:23750:2375 -v /var/run/docker.sock:/var/run/docker.sock  alpine/socat  tcp-listen:2375,fork,reuseaddr unix-connect:/var/run/docker.sock
```
> [!IMPORTANT]  
> Habrá que incluir el puerto 23750 en el firewall de la nube remota.  
> Para estar seguro de tener los puertos que necesita el RoboticsBackend habrá que
> tener en el firewall estos:
> ```text
> 7164, 1905, 2303, 8765, 6080-6090, 7681, 8080, 2304, 1904, 5900, 7163, 23750
> ```
> Hay que lanzar el RoboticsBackend remoto con:
> ```shell
> # Si da error quitar **--gpus all** y **--device /dev/dri**
> docker run --rm -it --gpus all -v /usr/lib/wsl:/usr/lib/wsl -e LD_LIBRARY_PATH=/usr/lib/wsl/lib --device /dev/dri -p 6080-6090:6080-6090 -p 7163:7163 jderobot/robotics-backend:latest
> ```

Iniciando automáticamente el contenedor cada vez que se abra Docker Desktop, permitiendo
que la web pueda lanzar Robotics Backends a las granjas.  
Para comprobar que se está ejecutando podemos usar **en WSL** desde nuestro ordenador:
```shell
echo "export DOCKER_HOST=tcp://<IP_DOCKER_REMOTO>:2375" >> ~/.bashrc && source ~/.bashrc
sudo nc -zv <IP_DOCKER_REMOTO> 23750
```
y debería de salir ``Conection to <IP_DOCKER_REMOTO> 23750 port [tcp/*] succeed!``.

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
Antes habrá que hacer un cambio en settings.py, en CHANNEL_LAYERS:
```
"CONFIG": {
            "hosts": [("127.0.0.1", <puerto_a_la_izq_del_:>)],
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

> [!NOTE]  
> Si no ves estos puertos o no funciona puede que la versión sea distinta, para
> añadir la máquina entonces hay que ir a "Operator admin" y modificar las bases
> de datos desde ahí, como se explica debajo.

Lo siguiente que habría que hacer es crear una granja (si queremos que la máquina
Docker remota pertenezca a otra) y añadirla a ella, y luego añadir la granja al
usuario que estemos usando.  
Y, una vez hecho todo esto, podemos entrar en cualquier ejercicio para conectarnos
con nuestro RoboticsBackend remoto (hay que seleccionar la granja donde tenemos la
maquina Docker remota para que lo use).

> [!IMPORTANT]  
> Si sale un error en bucle en el terminal de Daphne diciendo ``AttributeError:
> 'NoneType' object has no attribute 'group_add'`` habrá que comentar el siguiente
> *if* en **settings.py**:
> ```python
> DEPLOYMENT_TYPE = os.getenv("DEPLOYMENT_TYPE", "D1")
> 
> # if DEPLOYMENT_TYPE != "D1:
>   # Configuraciones específicas de Channels
> INSTALLED_APPS += ['channels']
> ASGI_APLICATION = 'common.routing.application'
> CHANNEL_LAYERS = {
>   "default": {
>       "BACKEND": "channels_redis.core.RedisChannelLayer",
>       "CONFIG": {
>           "hosts": [("127.0.0.1", <puerto_redis_a_la_izq_del_:>)]
>       },
>   },
> }
> """else:
>   # Configuración estándar sin Channels
>   WSGI_APPLICATION = 'common.wsgi.application'
> """
> ```
> y con esto, todo debería de funcionar.

Si después de conectarse con el RB remoto ves que las secciones de la derecha no
funcionan debes modificar el HTML para que se use http en vez de https en estas
peticiones, darle a F12 para usar las herramientas de desarrollador, elegir
"Seleccionar un elemento para inspeccionarlo", poner el ratón encima de las zonas
que no cargan y buscar:
```html
src="https://<IP_MAQUINA_REMOTA>:6080/vnc.html?..."
src="https://<IP_MAQUINA_REMOTA>:6082/vnc.html?..."
```
Y quitar la 's' de https, con esto las secciones deberían de cargar.