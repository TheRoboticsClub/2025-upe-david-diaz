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
source bin/activate
```

### Clonar el repositorio e instalar dependencias
Previamente habrá que haber creado una clave SSH, como se explica [aquí](https://www.hatica.io/blog/how-to-configure-github-ssh-keys/).
```shell
# Clonar repositorio con los submodulos
git clone --recurse-submodules https://github.com/JdeRobot/unibotics-webserver.git

# Instalar dependencias
cd unibotics-webserver
pip install -r utils/requirements.txt
```

