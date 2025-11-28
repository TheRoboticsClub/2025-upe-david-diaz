### Para que el frontend se conecte con el RB local, granja o Google Cloud

Usaremos el **formulario que hay actualmente en la página de Unibotics**,
eligiendo el RoboticsBackend que queremos utilizar.

- Podemos usar una **variable de entorno** (por ejemplo *RB_URL*) para
identificar la URL del RoboticsBackend (sea local, granja o Google Cloud).
- Añadir una columna más a la base de datos de Usuarios para la URL del backend
(pero el tamaño de la base de datos aumentará con el número de usuarios).

Dependiendo del valor de esa URL usaremos HTTP (local) o HTTPS (granja y Google
Cloud).

### Información que debe guardarse para que un usuario use su Google Cloud para el RB
Credenciales del usuario, región de despliegue; asignar la máquina creada al usuario.

### DASH
...