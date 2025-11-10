# Servidor *echo* websocket Python en la nube

## Servidor websocket que hace *echo* en Google Cloud
En este caso, comprobaremos la interacción entre servidor y cliente
websocket, pero el servidor estará en la nube de Google Cloud (emulando
ser el RoboticsBackend, aunque en este caso solamente le enviaremos
al cliente el mismo mensaje que nos envió). Para ello, meteremos este
servidor en un Docker y lo subiremos a la nube de Google Cloud.

Para ello usaremos los siguientes ficheros:

#### echo-server.py
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
CMD ["python", "server.py"]
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