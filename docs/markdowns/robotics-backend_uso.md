# Uso de robotics-backend

## Puertos
- **Consola noVNC:** 1108
- **Gazebo noVNC:** 6080
- **Socket:** 7163

Antes de nada hay que hacer ``docker pull jderobot/robotics-backend:latest``

Una vez hecho el pull, para ejecutar robotics-backend ejecutaremos:

## Ejecutar (Windows)
```shell
docker run --rm -it --gpus all -v /usr/lib/wsl:/usr/lib/wsl -e LD_LIBRARY_PATH=/usr/lib/wsl/lib --device /dev/dri -p 6080:6080 -p 1108:1108 -p 7163:7163 jderobot/robotics-backend:latest
```

## Ejecutar (Linux)
* Con GPU integrada:
```shell
docker run --rm -it --device /dev/dri -p 6080:6080 -p 1108:1108 -p 7163:7163 jderobot/robotics-backend:latest
```

* Solo con CPU:
```shell
docker run --rm -it -p 6080:6080 -p 1108:1108 -p 7163:7163 jderobot/robotics-backend:latest
```

## Ejecutar (MacOs)
* Solo con CPU:
```shell
docker run --rm -it --device /dev/dri -p 6080:6080 -p 1108:1108 -p 7163:7163 jderobot/robotics-backend:latest
```

Y ahora podemos acceder a través de los puertos indicados anteriormente mediante
http://localhost:6080 para acceder a la interfaz Gazebo noVNC.