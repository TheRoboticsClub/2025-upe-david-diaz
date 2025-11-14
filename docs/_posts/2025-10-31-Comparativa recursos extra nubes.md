Una vez anañizados los recursos que nos propirciona cada nube en la capa
gratuita, es interesante mirar el precio de los recursos extra que podamos
necesitar para realizar un despliegue a gran escala. En este caso, vamos a
comparar 2 de las mayores nubes actuales para el despliegue de RoboticsBackend
en la nube, tabulando sus sobrecostes para los recursos que necesitemos de más
por encima del nivel gratuito.

## Google Cloud

### Basado en solicitudes en Madrid (europe-southwest1)

|         Recurso          |         Tipo          |     Precio      |
|:------------------------:|:---------------------:|:---------------:|
|    CPU (vCPU/segundo)    |  Tiempo de actividad  |  0,000.024 US$  |
|                          | Tiempo de inactividad | 0,000.002.5 US$ |
|  Memoria (GiB/segundo)   |  Tiempo de actividad  | 0,000.002.5 US$ |
|                          | Tiempo de inactividad | 0,000.002.5 US$ |
| Solicitudes (por millón) |          N/A          |    0,40 US$     |


## AWS

### Con servicio AWS Fragatte en Europe (Spain) con Linux/X86

|        Recurso        |        Tipo         |      Precio       |
|:---------------------:|:-------------------:|:-----------------:|
|  CPU (vCPU/segundo)   | Tiempo de actividad | 0,000.012.931 US$ |
| Memoria (GiB/segundo) | Tiempo de actividad | 0,000.001.42 US$  |

### Con el servicio AWS Fragatte tolerante a interrupciones en Europe (Spain) con Linux/X86

|        Recurso        |        Tipo         |      Precio       |
|:---------------------:|:-------------------:|:-----------------:|
|  CPU (vCPU/segundo)   | Tiempo de actividad | 0,000.003.879 US$ |
| Memoria (GiB/segundo) | Tiempo de actividad | 0,000.000.426 US$ |

Máximo de 20 GB de almacenamiento efímero en Fragatte, hay un
sobrecoste de 0,000.000.034 US$ por GB adicional necesario por hora.

    "Los precios se calculan por segundo, con un mínimo de 1 minuto.
    La duración se calcula a partir del momento en que comienza a
    descargar la imagen de contenedor (docker pull) y hasta que la
    tarea termina, redondeada al segundo más cercano. Para los
    contenedores de Windows, la facturación se calcula por segundo
    con un mínimo de 5 minutos."

### Reducción de precio usando un Saving Plan para Amazon Fragatte:

| Duración del término | Ahorro en comparación con bajo demanda |
|:--------------------:|:--------------------------------------:|
|        1 año         |                  20%                   |
|        3 años        |                  45%                   |

### Para servicios de Amazon Elastic Compute Cloud

Servicio para computación aplicado a 
[Amazon EC2](https://aws.amazon.com/es/ec2/pricing/on-demand/) depende
del tipo de instancia.

Se puede reducir el coste entre un 40% y un 60% con un [Saving Plan para
Amazon EC2](https://aws.amazon.com/es/savingsplans/compute-pricing/)
dependiendo del tipo de instancia y duración del término.