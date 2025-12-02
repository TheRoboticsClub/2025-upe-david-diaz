# Azure para un contenedor Docker en la nube

## Pasos:
- **1.** Después de iniciar sesión (en este caso se usará cuenta de
estudiante), le daremos a *Crear un recurso* y seleccionaremos
*Contenedores > Instancias de Contenedores* y le damos a *Crear*.

<div style="text-align: center;">
  <img src="../img.png" alt="Descripción de la imagen">
</div>

- **2.** Rellenamos los campos necesarios, como el nombre del
contenedor, si queremos crear un contenedor o importar uno... y 
le damos a *Crear*.

- **3.** Al igual que para el programa Python en la nube, contamos con
VS Code para la edición, o podemos seleccionar otro método desde
el recurso.

Para despliegues de contenedores podemos usar **Azure Container
Instances (ACI)**, para despliegues rápidos, contenedores aislados;
o **Azure Kubernetes Service (AKS)**, para aplicaciones en producción
con escalabilidad avanzada o mayor complejidad y gestión.

## Contenedor más complejo
Si lo que queremos es ejecutar un contenedor más complejo en la nube lo que
habrá que hacer es crear una instancia de Máquina Virtual y ahí instalar Docker
y los contenedores que queramos ejecutar. Para ello habrá que habilitar también
los puertos que usen esos contenedores (creando un firewall con esos puertos) para
que puedan ser accesibles desde fuera.