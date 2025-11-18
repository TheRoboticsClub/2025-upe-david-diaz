# Conectarse con el RoboticsBackend corriendo en la nube

Una vez ya hemos conseguido desplegar y ejecutar el RoboticsBackend en la nube
de Google Cloud, con Google Compute Engine, el último paso es conectarse a ese
RoboticsBackend corriendo en remoto, para ello habrá que hacer:

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
# En el directorio unibotics-webserver/unibotics
docker exec -i academy_db psql -U user-dev -d academy_db < static/RIdatabase/database/universes.sql
docker exec -i academy_db psql -U user-dev -d academy_db < static/RIdatabase/database/3d_models.sql
docker exec -i academy_db psql -U user-dev -d academy_db < static/RoboticsAcademy/database/exercises/db.sql
docker exec -i academy_db psql -U user-dev -d academy_db < static/RoboticsAcademy-premium/database/exercises/db.sql
```

### Migraciones
En el directorio unibotics-webserver/unibotics
```shell

```
