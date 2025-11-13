# Conectarse con el Docker del servidor que hace echo en Google Run

Para acercarnos más al objetivo final de tener ejecutando el Docker de 
RoboticsBackend en la nube, y poder conectarnos con el navegador a ese Docker,
el siguiente paso es conectarse con el navegador a un pequeño Docker que
contiene un servidor que hace echo, escuchando en el puerto 7163 (para emular
a RoboticsBackend).

En este caso se usará una página HTML con todo el código TypeScript / JavaScript
incluido (cambiando <URL_del_Docker_ejecutando_en_Google_Cloud> por la URL)

```html
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <title>Cliente websocket</title>
</head>
<body>
    <h1>Cliente WebSocket en el navegador</h1>
    <p>Estado: <strong id="status">Desconectado</strong></p>
    <div>
        <input type="text" id="messageInput" placeholder="Escribe un mensaje...">
        <button onclick="sendMessage()">Enviar</button>
    </div>
    <h2>Mensajes recibidos:</h2>
    <ul id="messagesList"></ul>

    <script>
        const socketUrl = 'wss://<URL_del_Docker_ejecutando_en_Google_Cloud>';
        let socket;

        const statusElement = document.getElementById('status');
        const messageInput = document.getElementById('messageInput');
        const messagesList = document.getElementById('messagesList');

        function connect() {
            // Crea una nueva instancia de WebSocket
            socket = new WebSocket(socketUrl);

            // Evento de apertura de conexión
            socket.addEventListener('open', (event) => {
                statusElement.textContent = 'Conectado';
                statusElement.style.color = 'green';
                logMessage('Conexión establecida con el servidor.', 'server');
            });

            // Evento de recepción de mensajes
            socket.addEventListener('message', (event) => {
                // El servidor echo simplemente devuelve el mensaje enviado
                logMessage(`Recibido: ${event.data}`, 'received');
            });

            // Evento de cierre de conexión
            socket.addEventListener('close', (event) => {
                statusElement.textContent = 'Desconectado';
                statusElement.style.color = 'red';
                logMessage('Conexión cerrada.', 'server');
            });

            // Evento de error
            socket.addEventListener('error', (event) => {
                logMessage('Error en la conexión WebSocket.', 'error');
            });
        }

        function sendMessage() {
            const message = messageInput.value;
            if (message && socket && socket.readyState === WebSocket.OPEN) {
                socket.send(message);
                logMessage(`Enviado: ${message}`, 'sent');
                messageInput.value = '';
            } else if (socket.readyState !== WebSocket.OPEN) {
                logMessage('No conectado. Inténtalo de nuevo.', 'error');
            }
        }

        function logMessage(message, type) {
            const li = document.createElement('li');
            li.textContent = message;
            li.className = type; // Añade una clase para posibles estilos
            messagesList.appendChild(li);
        }

        // Estilos básicos para los mensajes (opcional)
        const style = document.createElement('style');
        style.innerHTML = `
            .server { color: blue; }
            .sent { color: black; }
            .received { color: purple; font-weight: bold; }
            .error { color: red; }
        `;
        document.head.appendChild(style);

        // Conectar automáticamente al cargar la página
        connect();
    </script>
</body>
</html>
```

Rellenando el valor de <URL_del_Docker_ejecutando_en_Google_Cloud> y abriendo
la página HTML en un navegador, vemos que la página consigue establecer 
conexión y comunicación con el servidor websocket ejecutando en Google Cloud,
y que los mensajes se envían y reciben bien (al igual que con un cliente
Python con websockets).
