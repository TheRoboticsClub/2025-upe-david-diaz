# Uso de robotics-academy

## Instalación y ejecución
Antes de nada hay que hacer pull a los siguientes contenedores:
```shell
docker pull jderobot/robotics-database:latest
```
```shell
docker pull jderobot/robotics-academy:latest
```

Una vez hechos los pull, lanzaremos las bases de datos, ejecutando:
```shell
docker run --hostname my-postgres --name academy_db -d 
    -e POSTGRES_DB=academy_db 
    -e POSTGRES_USER=user-dev 
    -e POSTGRES_PASSWORD=robotics-academy-dev 
    -e POSTGRES_PORT=5432 
    -d -p 5432:5432 
    jderobot/robotics-database:latest
```

Luego, ejecutaremos el robotics-academy:

### Linux:  
Integrated GPU:
```shell
docker run --rm -it --device /dev/dri -p 6080:6080 -p 1108:1108 -p 7163:7163 -p 7164:7164 --link academy_db jderobot/robotics-academy:latest
```
Only CPU:
```shell
docker run --rm -it -p 6080:6080 -p 1108:1108 -p 7163:7163 -p 7164:7164 --link academy_db jderobot/robotics-academy:latest
```

### Windows:
(si da error quitar ``--gpus all`` y ``--device /dev/dri``)
```shell
docker run --rm -it --gpus all -v /usr/lib/wsl:/usr/lib/wsl -e LD_LIBRARY_PATH=/usr/lib/wsl/lib --device /dev/dri -p 6080:6080 -p 1108:1108 -p 7163:7163 -p 7164:7164 --link academy_db jderobot/robotics-academy:latest
```

### MacOS:
Only CPU:
```shell
docker run --rm -it -p 6080:6080 -p 1108:1108 -p 7163:7163 -p 7164:7164 --link academy_db jderobot/robotics-academy:latest
```

Una vez tenemos ejecutando el robotics-academy, podemos acceder a los ejercicios abriendo un
navegador y entrando en http://localhost:7164/

## Puertos RoboticsAcademy:
* **1905:** Interactuar con el cerebro del robot, envía el código del usuario a exercise.py a través
de este puerto
* **2303:** Enviar datos del backend al frontend
* **5432:** Base de datos PostgreSQL
* **6080 - 6090:** Puertos para herramientas de uso (tool ports)
* **7163:** Websocket
* **7164:** Servidor Django
* **8765:** Interactuar con los ejercicios, protocolo RAMP (Robotics Academy Manager Protocol)

## Puertos RoboticsBackend:
* **1108:** Consola noVNC
* **6080:** Gazebo noVNC
* **7163:** Websocket 

## Base de datos:
Tiene 12 tablas, con el siguiente formato:  
https://github.com/JdeRobot/RoboticsAcademy/blob/humble-devel/docs/database.md

## Servidor:
Usa **Django** para la parte de servidor y páginas web.

## Contribuir:
Clonar el repositorio de RoboticsAcademy:  
(sin el argumento -b se trabajará en la rama *main*)
```shell
git clone --recurse-submodules https://github.com/JdeRobot/RoboticsAcademy.git -b <src-branch>
cd RoboticsAcademy/
```
Luego:
(sin argumentos se preparará un escenario con la rama actual)
```shell
./scripts/develop_academy.sh -r <link to the RAM repo/fork> -b <branch of the RAM repo> -i <humble>
```
