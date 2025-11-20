Ahora, vamos a desplegar por fin el contenedor de RoboticsBackend en la nube,
para ello usaremos Google Cloud, con Google Compute Engine.

> [!IMPORTANT]
> Para poder utilizar los puertos que queremos tendremos que **crear una
> regla de firewall**. Para ello, estando en el proyecto donde instanciamos
> la Máquina Virtual, nos vamos al menú de las 3 barras horizontales (zona
> superior izquierda) y nos vamos a **Red de VPC > Firewall**.  
> Una vez aquí, le daremos a *Crear una nueva regla de firewall* (en la
> zona superior de la pantalla) y rellenaremos los campos:
> - **Nombre:** allow-robotics-backend-ports (o el nombre que queramos).
> - **Registros:** Desactivado (para reducir los costes de Logging)
> - **Dirección de tráfico:** Entrada
> - **Destinos:** Todas las instancias de la red (para incluir la MV)
> - **Filtro de origen:** Rangos de IP
> - **Rangos de IP de origen:** 0.0.0.0/0 (permitir todas las de Internet)
> - **Protocolos y puertos:** Protocolos y puertos especificados
>   - **TCP**: 1108, 6080, 7163 (los de RoboticsBackend)
> 
> Y le damos a *Crear*. 

## Pasos:
- **1.** Crear una nueva instancia de Máquina Virtual.
- **2.** Rellenar los campos necesarios que salen por pantalla.

Usaremos:
#### Configuración de la máquina
- **Región:** europe-west1 (Bélgica)
- **Máquina:** e2-standard-4 (4 vCPU, 2 núcleos, 16 GB de RAM)

#### SO y almacenamiento
- **Sistema operativo:** Ubuntu
- **Versión:** Ubuntu 24.04 LTS Minimal (x86-64 amd64 noble minimal image)
- **Tipo de disco de arranque:** Disco persistente balanceado
- **Tamaño:** 50 GB

#### Protección de datos
- **Sin copias de seguridad:** Al ser para pruebas

#### Redes
- **Firewall:** Permitir tráfico HTTP, permitir tráfico HTTPS

#### Observabilidad
- **Instalar el Agente de operaciones para supervisión y registros:** Para
tener datos de registros y métricas, y saber lo que consume.

#### Seguridad
- **Predeterminado**

#### Avanzado
- **Predeterminado**

Y ahora le damos a *Crear*.

## Instalación de RoboticsBackend en instancia de Máquina Virtual
Una vez tenemos la instancia creada, pincharemos en ella y en el apartado de *Detalles*
le daremos a la flecha que está al lado de *SSH* y seleccionaremos *Abrir en otra
ventana del navegador*.  
A continuación se nos abrirá una nueva ventana con una shell, en la que ejecutaremos:
```shell
# Actualizar la lista de paquetes
sudo apt update
```
```shell
# Instalar Docker en Ubuntu
sudo apt install docker.io -y
```
```shell
# Asegurarse de que el servicio está iniciado y habilitado
sudo systemctl start docker
sudo systemctl enable docker
```
```shell
# Descargar la imágen de RoboticsBackend
sudo docker pull jderobot/robotics-backend:latest
```
```shell
sudo docker run --rm -it -p 6080:6080 -p 1108:1108 -p 7163:7163 jderobot/robotics-backend:latest
```

Y ya nos podríamos comunicar con el Roboticsbackend corriendo en la nube
de Google Compute Engine, usando la IP externa de la instancia de MV, que nos
sale en el apartado *Instancias de VM* en la columna *IP externa*.

> [!IMPORTANT]  
> Acuérdate de detener la instancia para reducir costes.  
> Para ello, en el apartado *Instancias de VM*, selecciona tu instancia en el
> cuadrado a la izquierda de su nombre y dale a *Detener*, así Google Cloud
> solamente te cobrará por tener la IP externa fija, y por el disco persistente,
> pero no por CPU ni tiempo de cómputo (ya que la instancia está detenida).