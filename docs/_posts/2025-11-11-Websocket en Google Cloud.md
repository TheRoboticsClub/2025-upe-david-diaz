Para ir un paso más allá con el objetivo de desplegar RoboticsBackend en
la nube y que ejecute allí, vamos a comprobar la interacción entre un servidor
y un cliente WebSocket, pero el servidor estará en la nube de Google
Cloud (emulando ser el RoboticsBackend, aunque en este caso solamente le
enviaremos al cliente el mismo mensaje que nos envió). Para ello, meteremos
este servidor en un Docker y lo subiremos a la nube de Google Cloud.

Para ello usaremos los siguientes ficheros:

#### echo_server.py
```python
"""Websocket Python echo server."""

import asyncio
import websockets
import os

PORT = 7163  # RoboticsBackend browser port

async def echo(websocket):
    """Returns the received messages from the client."""
    
    print(f"Client connected at: {websocket.remote_address}")
    try:
        async for message in websocket:
            print(f"Received: {message}")
            await websocket.send(message)
    except websockets.exceptions.ConnectionClosed:
        print(f"Clossed connection with: {websocket.remote_address}")

async def main():
    """Main server function."""
    
    # Get the environment variable PORT of Google Cloud
    port = int(os.environ.get("PORT", PORT))
    
    async with websockets.serve(echo, "0.0.0.0", port):
        print(f"Socket listening in port: {port}")
        await asyncio.Future()  # Serve forever

if __name__ == "__main__":
    asyncio.run(main())
```

#### Dockerfile
```dockerfile
FROM python:3.11-slim
WORKDIR /app
RUN pip install websockets
COPY . .

# Usamos -u para ver los logs en el terminal
CMD ["python", "-u", "echo_server.py"]
```

Ahora, para crear el Docker y probarlo en local, ejecutaremos:
```shell
# Para crear el Docker (ws-echo-server es el tag que elegimos)
docker build -t ws-echo-server .
```
```shell
# Mapeamos el puerto 7163 al 7163
docker run -p 7163:7163 ws-echo-server
```

Una vez hemos comprobado que funciona correctamente en local habrá que subirlo a
DockerHub para poder importarlo desde la consola remota de Google Cloud, o descargar
la consola de Google Cloud para poder acceder al contenedor local. Luego, subiremos
el Docker a la nube de Google Cloud, con Cloud Run. Para ello, ejecutaremos:
```shell
docker login
```
```shell
docker tag ws-echo-server <dockerhub_name>/ws-echo-server:latest
```
```shell
docker push <dockerhub_name>/ws-echo-server:latest
```

Y, ahora seguiremos los pasos de [este documento](https://github.com/TheRoboticsClub/2025-upe-david-diaz/blob/main/docs/markdowns/google_cloud/docker/google_cloud_docker.md)
poniendo el número mínimo de instancias en 0 para que no consuma CPU si no hay ninguna
petición.

Una vez en la nube, vamos a probarlo con el cliente local y la URL de Google Run:
```python
import asyncio
import websockets

BROWSER_PORT = 7163

GOOGLE_CLOUD_URL = 'wss://URL.run.app'  # Use wss:// because of Google Run security
MESSAGE = 'test message'  # Neccessary information

async def send_msg(msg: str):
    async with websockets.connect(GOOGLE_CLOUD_URL) as web_socket:
        await web_socket.send(msg)
        
        response = await web_socket.recv()
        print(f'Received from server: {response}')

asyncio.run(send_msg(MESSAGE))
```

Al ejecutar esto, debería de salirnos en nuestro terminal el mensaje enviado al
servidor de la nube.