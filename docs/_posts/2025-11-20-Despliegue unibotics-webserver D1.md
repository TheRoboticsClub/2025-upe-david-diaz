# Conectarse con el RoboticsBackend corriendo en local con unibotics-webserver D1

Un último paso que tomaremos antes del objetivo de conseguir conectarnos al
contenedor de RoboticsBackend ejecutando en remoto es ejecutar todo el servicio
de unibotics-webserver en local (D1) y usar el RB local. Para ello:

> [!IMPORTANT]  
> Antes habrá que tener instalado Python, Git y Pycharm, además de WSL2 y PostgreSQL.

En la Git Bash ejecutaremos:

### Creación de un entorno virtual
```shell
# Crear un directorio donde crear el entorno virtual y meter el código
mkdir academy-venv
cd academy-venv/

# Crear el entorno virtual
python3 -m venv .

# Activar el entorno virtual
Scripts/activate
```

### Clonar el repositorio e instalar dependencias
Previamente habrá que haber creado una clave SSH, como se explica [aquí](https://www.hatica.io/blog/how-to-configure-github-ssh-keys/).
Una vez la tenemos, desde PyCharm ejecutamos:
```shell
# Clonar repositorio con los submodulos
git clone --recurse-submodules https://github.com/JdeRobot/unibotics-webserver.git

# Si no se clonan bien ejecutar:
cd unibotics-webserver
git submodule update --init --recursive

# Si sigue sin estar clonado algún submodulo usar:
git submodule update --force unibotics/static/<submodulo>

# Instalar dependencias
cd unibotics-webserver
pip install -r utils/requirements.txt
```

### Creación de zip de common
```shell
# Estando en el directorio unibotics (donde el manage.py)
cd static/RoboticsAcademy/common  # Si no está, clonar los submódulos a mano
mkdir RA_links  # Crear directorio 'RA_links'

# Desde el directorio unibotics/static/RoboticsAcademy/common
# IMPORTANTE: hay que usar WSL para estos comandos
cd console_interfaces
zip -r ../common.zip console_interfaces/
cd ..
cd gui_interfaces
zip -r -u ../common.zip gui_interfaces/
cd ..
cd hal_interfaces
zip -r -u ../common.zip hal_interfaces/
cd ../..
mv common/common.zip react_frontend/src/common.zip
cd ../..
```

### Instalar y activar webpack
```shell
npm install webpack --legacy-peers-deps
curl https://raw.githubusercontent.com/creationix/nvm/master/install.sh | bash
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"  # This loads nvm
[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"  # This loads nvm bash_completion
source ~/.bashrc  # Activar nvm
nvm install 17  # Instalar nodo 17
nvm use 17  # Activar nodo 17
cd unibotics-webserver/unibotics/react_frontend  # Moverse de carpeta unibotics/react_frontend
npm run dev  # Lanzar webpack

# Si salen errores de *Module not found: Error: Can't resolve ...* relacionado con
# BtStudio, ejecutar desde el directorio unibotics/static/BtStudio
git checkout v0.10.0  # Coger la rama v0.10.0
```

### Base de datos
```shell
docker pull postgres:13.11  # Descargar la imagen Docker
docker run --name academy_db -d\
    -e POSTGRES_DB=academy_db \
    -e POSTGRES_USER=user-dev \
    -e POSTGRES_PASSWORD=unibotics-dev \
    -e POSTGRES_PORT=5432 \
    -d -p 5432:5432 \
    postgres
```

Creamos el fichero .env en unibotics/common
```dotenv
SERVER=127.0.0.1
SERVER_PORT=7000

DATABASE_NAME=academy_db
DATABASE_USER=user-dev
DATABASE_PASSWORD=unibotics-dev
DATABASE_HOST=127.0.0.1
DATABASE_PORT=5432

EMAIL_PORT=587
```

### Dumps de ejercicios y universos
```shell
# En el directorio unibotics-webserver/unibotics (usando WSL)
docker exec -i academy_db psql -U user-dev -d academy_db < static/RIdatabase/database/universes.sql
docker exec -i academy_db psql -U user-dev -d academy_db < static/RIdatabase/database/3d_models.sql
docker exec -i academy_db psql -U user-dev -d academy_db < static/RoboticsAcademy/database/exercises/db.sql
docker exec -i academy_db psql -U user-dev -d academy_db < static/RoboticsAcademy-premium/database/exercises/db.sql
```

### Migraciones
En el directorio unibotics-webserver/unibotics
```shell
python manage.py migrate
```
Si este comando lanza un error ``django.db.utils.OperationalError`` es posible que sea
producido porque haya 2 procesos (el Docker de la base de datos y otro) escuchando en
el puerto de la base de datos, para comprobarlo ejecuta:
```shell
netstat -ano | findstr :5432
```
y mira si en la columna de la derecha (los PID) sale un solo número (aunque sea repetido)
o salen varios (números distintos repetidos). Si salen varios, hay que ir al Administrador
de tareas y *Finalizar tarea* para el que **NO** sea de ``com.docker.backend.exe``, y ahora
debería de funcionar el comando ``python manage.py migrate``.

### Configurar usuarios de prueba
Esto habrá que hacerlo la primera vez:
```shell
# Desde el directorio unibotics-webserver (usando WSL)
docker exec -i academy_db psql -U user-dev -d academy_db < utils/dumps/new_dump.sql
```

### Arrancar servidor Django
Para ello ejecutaremos:
```shell
# Desde el directorio unibotics-webserver/unibotics
python manage.py runserver <puerto>
```
Siendo <puerto> el puerto que queramos usar, **debe coincidir con la variable SERVER_PORT
del archivo .env**, en nuestro caso el número 7000.  
Después de eso deberíamos poder entrar en http://127.0.0.1:7000/ y ver la página de Unibotics.

### Usuarios de prueba
Para usar nuestro despliegue en D1 podemos usar los siguientes usuarios:

| Usuario            | Role       | Contraseña      |
|--------------------|------------|-----------------|
| user               | Admin      | pass            |
| admin              | Admin      | admin           |
| admin_dummy        | Admin      | pass            |
| admin_dummy_2      | Admin      | unibotics123456 |
| admin_dummy_3      | Admin      | unibotics123456 |
| betatester_dummy_1 | Betatester | unibotics123456 |
| betatester_dummy_2 | Betatester | unibotics123456 |
| betatester_dummy_3 | Betatester | unibotics123456 |
| student_dummy_1    | Alumno     | unibotics123456 |
| student_dummy_2    | Alumno     | unibotics123456 |
| student_dummy_3    | Alumno     | unibotics123456 |

### Docker de RoboticsBackend
Por último, lo que nos falta es lanzar el Docker de RoboticsBackend, para ello ejecutaremos:
```shell
# Si no lo tenemos descargado
docker pull jderobot/robotics-backend:latest

# Ejecutarlo (si da error quitar ``--gpus all`` y ``--device /dev/dri``)
docker run --rm -it --gpus all -v /usr/lib/wsl:/usr/lib/wsl -e LD_LIBRARY_PATH=/usr/lib/wsl/lib --device /dev/dri -p 6080:6080 -p 1108:1108 -p 7163:7163 jderobot/robotics-backend:latest
```
