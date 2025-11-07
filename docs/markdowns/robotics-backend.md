# Robotics Backend

Ejecutar RoboticsBackend en local (habiendo hecho``dockerpull
jderobot/robotics-backend:latest``) con ``docker run``.  
Iniciar sesión en la página de Unibotics, y seleccionar un ejercicio.  
Darle a *Connect* en la parte inferior y esperar a que se conecte.  

La idea sería que el RoboticsBackend se estuviera ejecutando en la nube, 
por ejemplo en Google Cloud, y que el usuario de Unibotics al darle a
*Connect* se conectara con el RoboticsBackend que está en la nube.

El RoboticsBackend se comunicará mediante websocket (Python) con el browser
(front end, aplicación web Django) a través del puerto 7163. A través
del puerto 6080 enviará la información de Gazebo; y a traves del puerto 1108
la consola VNC.

```pycon
"""Server"""

import asyncio
from websockets.asyncio.server import serve

VNC_CONSOLE_PORT = 1108
GAZEBO_PORT = 6080
BROWSER_PORT = 7163

async def echo(websocket):
    async for message in websocket:
        await websocket.send(message)

async def main():
    async with serve(echo, "localhost", PORT) as server:
        await server.serve_forever()

asyncio.run(main())
```
```pycon
"""Client"""

from websockets.sync.client import connect

VNC_CONSOLE_PORT = 1108
GAZEBO_PORT = 6080
BROWSER_PORT = 7163

msg = ''  # Neccessary information

def hello():
    with connect(f"ws://localhost:{PORT}") as websocket:
        websocket.send(msg)
        message = websocket.recv()
        print(f"Received: {message}")

hello()
```
Para ello, debería correr en la nube habiendo ejecutado el comando:
### Linux:  
Integrated GPU:
```shell
docker run --rm -it --device /dev/dri -p 6080:6080 -p 1108:1108 -p 7163:7163 jderobot/robotics-backend:latest
```
Only CPU:
```shell
docker run --rm -it -p 6080:6080 -p 1108:1108 -p 7163:7163 jderobot/robotics-backend:latest
```

### Windows:
(si da error quitar ``--gpus all`` y ``--device /dev/dri``)
```shell
docker run --rm -it --gpus all -v /usr/lib/wsl:/usr/lib/wsl -e LD_LIBRARY_PATH=/usr/lib/wsl/lib --device /dev/dri -p 6080:6080 -p 1108:1108 -p 7163:7163 jderobot/robotics-backend:latest
```

### MacOS:
Only CPU:
```shell
docker run --rm -it --device /dev/dri -p 6080:6080 -p 1108:1108 -p 7163:7163 jderobot/robotics-backend:latest
```