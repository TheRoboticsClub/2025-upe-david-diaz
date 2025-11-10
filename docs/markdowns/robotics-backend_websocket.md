# Comunicación con RoboticsBackend usando websockets

## Cliente simple de prueba
Para comunicarse con RoboticsBackend se utilizan websockets. Para
establecer la primera comunicación, usaremos este pequeño cliente
Python, usando websockets.

```python
import asyncio
import websockets

VNC_CONSOLE_PORT = 1108
GAZEBO_PORT = 6080
BROWSER_PORT = 7163

MESSAGE = 'message'  # Neccessary information

async def send_msg(msg: str):
    async with websockets.connect(f"ws://localhost:{BROWSER_PORT}") as web_socket:
        await web_socket.send(msg)

asyncio.run(send_msg(MESSAGE))

# If asyncio.run(send_msg(MESSAGE)) raises error, use:
# await send_msg(MESSAGE)
```

Para comprobar si conseguimos establecer comunicación entre el cliente
websocket y el RoboticsBackend, primero ejecutaremos el Docker de
RoboticsBackend y, a continuación, el pequeño cliente websocket.

Observando los *logs* del contenedor vemos que nos han salido erroes,
esto es debido a que no estamos usando el protocolo de comunicación
que espera recibir el Roboticsbackend, pero hemos comprobado que con
este pequeño cliente podemos comunicarnos con él.
