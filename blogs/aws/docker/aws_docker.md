# Google Cloud para un contenedor Docker en la nube

## Pasos:
- **1.** Primero de todo iremos a *Elastic Container  Registry* de
AWS, apartado repositorios, y crearemos un nuevo repositorio.

- **2.** Ahora, rellenamos los campos solicitados y le damos a crear.

- **3.** Luego habrá que subir la imágen a *Amazon Elastic Container
Registry (ECR)* usando ``docker push ...`` y desplegarla usando
*Amazon Elastic Container Service (ECS)*.