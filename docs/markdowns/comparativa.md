# Comparativa Google Cloud, AWS y Azure

En este documento se compararán las nubes de **Google Cloud**, **AWS**
y **Azure**. Para ello se tendrá en cuenta en todas el paquete gratuito
para Cloud Computing y ejecución de programas en las nubes.

## Google Cloud
* **Disco duro:** 5 GiB de almacenamiento estándar en Cloud Run.
* **RAM:** Entre 360.000 y 450.000 segundos GiB al mes, dependiendo
de si el servicio es basado en instncias (primero), o en
solicitudes (con 2 millones de solicitudes gratis al mes).
* **CPU:** 240.000 segundos de vCPU al mes.

Tiene además **prueba gratuita** de **90 días** con **300 $ en
créditos** para **nuevos clientes**.

Es el **más intuitivo** de usar.

## AWS
* **Disco duro:** Entre 5 GB y 30 GB, dependiendo del servicio.
* **RAM:** 1 GB de RAM.
* **CPU:** 1 vCPU, 750 horas de instancia al mes, y hasta 3.2 M
de segundos de tiempo de cómputo al mes con Lambda.

Nivel **gratuito** de **6 meses** o **200 USD en créditos**. **Después 
de ese tiempo** o cuando se usen **todos los créditos** se **cerrará la
cuenta** si no se cambia a un plan de pago.
Al crear cuenta te **retienen 1 USD** que te **devuelven en unos días**
para verificar el método de pago.

El que más problemas da a la hora de las cuentas y crear aplicaciones. 

## Azure
* **Disco duro:** 2 discos de 64 GB cada uno para máquinas virtuales.
5 GB de almacenamiento *Azure Blob Storage*.
* **RAM:** Entre 1 GB y 4 GB de RAM dependiendo de elección del modelo,
y 360.000 segundos GiB al mes para aplicaciones de contenedor.
* **CPU:** Entre 1 vCPU y 2 vCPU dependiendo de elección del modelo,
con 750 horas al mes de máquina virtual, que se puede combinar (por
ejemplo 2 VM de 375 horas cada una).
    
    180.000 segundos de vCPU al mes para aplicaciones de contenedor
y 2 M solicitudes al mes.

Mayor lentitud a la hora de cambios en el cógido, al tener que estar
haciendo *commits* y desplegando la aplicación de nuevo desde local a
remoto en Azure.