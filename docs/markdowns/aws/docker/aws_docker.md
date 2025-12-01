# AWS para un contenedor Docker en la nube

## Pasos:
- **1.** Crearemos una nueva instancia EC2, creando una Máquina Virtual donde
ejecutaremos el Docker.
- **2.** Rellenaremos los campos que salen por pantalla, usando sistema Ubuntu
Server 24.04. Hay que añadir en las reglas del firewall mínimo los puertos que necesita
RoboticsBackend (7164, 1905, 2303, 8765, 6080-6090, 7681, 8080, 2304, 1904, 5900, 7163, 23750).
Y, para mayor seguridad podemos crear un par de claves RSA para la conexión con la
Máquina Viertual.
- **3.** Le damos a crear y nos conectamos con la instancia, para ello clickamos
en ella y le damos a *Conectar* (parte superior de la pantalla).
- **4.** Se nos abrirá una consola, donde ejecutaremos:
    ```shell
    sudo apt update
    sudo apt install docker.io -y  # Instalar Docker
    ```
  Y, una vez hecho esto, podremos bajarnos y ejecutar el Docker que queramos (en
  este caso el RoboticsBackend).