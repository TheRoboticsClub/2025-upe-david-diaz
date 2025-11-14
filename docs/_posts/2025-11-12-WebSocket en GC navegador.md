Para acercarnos un poco más a la realidad de la interacción con RoboticsBackend
vamos a conectarnos al mismo contenedor que tiene un servidor WebSocket que hace
*echo*, pero en lugar de usar un cliente escrito en Python, usaremos una página HTML
con TypeScript / JavaScript (que se aproxima bastante más a lo que usará el usuario).

En este caso se usará una página HTML con todo el código TypeScript / JavaScript
incluido (cambiando <URL_del_Docker_ejecutando_en_Google_Cloud> por la URL).

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Websocket client</title>
</head>
<body>
    <h1>WebSocket client in the browser</h1>
    <p>Status: <strong id="status">Disconnected</strong></p>
    <div>
        <input type="text" id="messageInput" placeholder="Write a message...">
        <button onclick="sendMessage()">Send</button>
    </div>
    <h2>Received messages:</h2>
    <ul id="messagesList"></ul>

    <script>
        const socketUrl = 'wss://<URL_del_Docker_ejecutando_en_Google_Cloud>';
        let socket;

        const statusElement = document.getElementById('status');
        const messageInput = document.getElementById('messageInput');
        const messagesList = document.getElementById('messagesList');

        function connect() {
            // Creates a new WebSocket instance
            socket = new WebSocket(socketUrl);

            // Open connection event
            socket.addEventListener('open', (event) => {
                statusElement.textContent = 'Connected';
                statusElement.style.color = 'green';
                logMessage('Established connection with server.', 'server');
            });

            // Message reception event
            socket.addEventListener('message', (event) => {
                logMessage(`Received: ${event.data}`, 'received');
            });

            // Close connection event
            socket.addEventListener('close', (event) => {
                statusElement.textContent = 'Disconnected';
                statusElement.style.color = 'red';
                logMessage('Closed connection.', 'server');
            });

            // Error event
            socket.addEventListener('error', (event) => {
                logMessage('Error with the connexion.', 'error');
            });
        }

        function sendMessage() {
            const message = messageInput.value;
            if (message && socket && socket.readyState === WebSocket.OPEN) {
                socket.send(message);
                logMessage(`Sent: ${message}`, 'sent');
                messageInput.value = '';
            } else if (socket.readyState !== WebSocket.OPEN) {
                logMessage('Not connected, try again.', 'error');
            }
        }

        function logMessage(message, type) {
            const li = document.createElement('li');
            li.textContent = message;
            li.className = type;
            messagesList.appendChild(li);
        }

        // Basic style for messages
        const style = document.createElement('style');
        style.innerHTML = `
            .server { color: blue; }
            .sent { color: black; }
            .received { color: purple; font-weight: bold; }
            .error { color: red; }
        `;
        document.head.appendChild(style);

        // Connect automatically when loading the page
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