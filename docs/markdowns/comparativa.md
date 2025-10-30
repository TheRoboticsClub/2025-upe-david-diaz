# Comparativa Google Cloud, AWS y Azure

En este documento se compararán las nubes de **Google Cloud**, **AWS**
y **Azure**. Para ello se tendrá en cuenta en todas el paquete gratuito
para Cloud Computing y ejecución de programas y contenedores en ellas.

## Google Cloud
* **Disco duro:** 5 GiB de almacenamiento estándar en Cloud Run, y 5 TB
de tamaño máximo de imágen del contenedor.
* **RAM:** Entre 360.000 y 450.000 segundos GiB al mes, dependiendo
de si el servicio es basado en instncias (primero), o en
solicitudes (con 2 millones de solicitudes gratis al mes); con desde
128 MiB a 32 GiB de memoria para cada instancia del contenedor.
* **CPU:** 240.000 segundos de vCPU al mes, hasta 8 CPUs para
contenedores.

Tiene además **prueba gratuita** de **90 días** con **300 $ en
créditos** para **nuevos clientes**.

Es el **más intuitivo** de usar. Los contenedores también se pueden
implementar continuamente a partir de un repositorio GitHub por si se
hacen cambios periódicamente en el funcionamiento.

## AWS
* **Disco duro:** Entre 5 GB y 30 GB, dependiendo del servicio.
* **RAM:** 1 GB de RAM.
* **CPU:** 1 vCPU, 750 horas de instancia al mes, y hasta 3.2 M
de segundos de tiempo de cómputo al mes con Lambda (tamaño máximo de
la imágen del contenedor de 10 GB).

Nivel **gratuito** de **6 meses** o **200 USD en créditos**. **Después 
de ese tiempo** o cuando se usen **todos los créditos** se **cerrará la
cuenta** si no se cambia a un plan de pago.
Al crear cuenta te **retienen 1 USD** que te **devuelven en unos días**
para verificar el método de pago.

El que más problemas da a la hora de las cuentas y crear aplicaciones. 

## Azure
* **Disco duro:** 2 discos de 64 GB cada uno para máquinas virtuales.
5 GB de almacenamiento *Azure Blob Storage*; tamaño de imágen máxima
del contenedor de 15 GB (si se quiere más hay que solicitar cuota para
Contenedores Grandes).
* **RAM:** Entre 1 GB y 4 GB de RAM dependiendo de elección del modelo,
y 360.000 segundos GiB al mes para aplicaciones de contenedor.
* **CPU:** Entre 1 vCPU y 2 vCPU dependiendo de elección del modelo,
con 750 horas al mes de máquina virtual, que se puede combinar (por
ejemplo 2 VM de 375 horas cada una); 4 vCPU máximo para contenedores
en nivel gratuito (si se necesita más hay que solicitar cuota de
soporte técnico).
    
    180.000 segundos de vCPU al mes para aplicaciones de contenedor
y 2 M solicitudes al mes.

Mayor lentitud a la hora de cambios en el cógido, al tener que estar
haciendo *commits* y desplegando la aplicación de nuevo desde local a
remoto en Azure.

> Google Cloud es el mejor para análisis de datos, Big Data, para el 
> uso de Inteligencia Artificial (IA), y para contenedores en la nube.
> 
> AWS es el que más variedad de servicios y funcionalidades ofrece, 
> pero el nivel gratuito solo está disponible durante 6 meses o 200
> USD en créditos.
> 
> Azure es el mejor si ya se usan servicios híbridos, ya que permite
> utilizarla con otras nubes. Además, es compatible con los servicios
> de Microsoft, al ser de su propiedad.