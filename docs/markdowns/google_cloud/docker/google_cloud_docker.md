# Google Cloud para un contenedor Docker en la nube

> [!IMPORTANT]  
> Para poder subir un contenedor a Google Cloud con Google Run hay que
> haberlo subido anteriormente al registro privado de imágenes de Google.
> 
> Para ello abriremos la Cloud Shell de nuestro proyecto y escribiremos:
> 
> ```{r, engine='sh'}
> docker pull {nombre_dockerHub}/{repo}
> ```
> 
> Para importarla a la máquina de Google y la renombraremos para poderla
> enviar al registro, con:
> 
> ```{r, engine='sh'}
> docker tag {nombre_dockerHub}/{repo} gcr.io/{nombre_proyecto_Google_Cloud}/{nuevo_nombre_imagen}:v1
> ```
> 
> Y, a continuación empujaremos la imágen al registro mediante:
> ```{r, engine='sh'}
> docker push gcr.io/{nombre_proyecto_Google_Cloud}/{nuevo_nombre_imagen}:v1
> ```
> 
> Para que funcione habrá que tener la API de Google Container Registry activada.

## Pasos:
- **1.** Después de iniciar sesión iremos al apartado "*Consola*", en la parte
superior derecha de la pantalla.

- **2.** Luego, creamos un nuevo proyecto en Google Cloud.

- **3.** A continuación, ya creado, le daremos a "*Implementar una aplicación*".

<div style="text-align: center;">
  <img src="../img.png" alt="Descripción de la imagen">
</div>

- **4.** En el menú izquierdo, en Descripción General, podemos conectar
con un repositorio GitHub, implementar un contenedor Docker... Le daremos a
"*Implementa un contenedor*".

<div style="text-align: center;">
  <img src="../img_1.png" alt="Descripción de la imagen">
</div>

- **5.** A continuación, rellenamos los campos necesarios, URL del contenedor,
Nombre del Servicio... y le damos a *Crear*. Y sería entrar en la URL y listo.

> [!IMPORTANT]  
> Antes de eso deberemos tener las credenciales de Docker en el *Secret Manager*
> de Google Cloud. Para ello nos iremos a la página de 
> [Secret Manager](https://console.cloud.google.com/security/secret-manager?)
> y seleccionaremos nuestro proyecto. A continuación le daremos a *Crear secreto*
> y rellenamos los campos.
> 
> En el campo **Nombre** pondremos ``docker-username`` y en **Valor secreto** 
> escribiremos nuestro nombre de usuario de Docker, y le daremos a *Crear secreto*.
> 
> También tendremos que repetir estos pasos para almacenar la contraseña de Docker
>    
>    <div style="text-align: center;">
>        <img src="img.png" alt="Descripción de la imagen">
>    </div>
>
> Ahora le daremos a *Mostrar panel de información*. En el panel de *Permisos*
> seleccionamos *Añadir principal* (seleccionando los secretos creados previamente)
> y pondremos en *Principales nuevas* por ejemplo nuestro correo de Google Cloud.
> Y en el campo *Rol* le asignaremos ``Administrador de Secret Manager`` y guardamos.