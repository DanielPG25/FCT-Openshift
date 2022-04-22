# Casos Prácticos

Para este proyecto vamos a ver las diferentes formas que tiene Openshift de crear contendedores. Antes hemos mencionado el Source2Image, pero Openshift nos ofrece también otras opciones que son bastante interesantes.

## Image2Source de una aplicación basada en python

En primer lugar, vamos a comenzar con una aplicación simple escrita en pyhton. Para ello he elegido la aplicación desarrollada por José Domingo Muñoz de "temperaturas_flask", la cual la podéis encontrar en el siguiente [repositorio](https://github.com/josedom24/flask_temperaturas). Esta aplicación nos muestra la temperatura máxima y mínima del día en el municipio de Sevilla que le indiquemos. Así pues, lo primero que hemos hecho ha sido hacer un fork del repositorio y actualizar el fichero "requirements.txt", ya que la versión de los paquetes estaban desactualizadas y darían problemas en openshift:

```
Flask==2.0
itsdangerous==2.1.2
Jinja2==3.1.1
lxml==4.6.3
MarkupSafe==2.1.1
Werkzeug==2.1.0
```

Una vez que hemos hecho esto, entramos en la consola web de Openshift e indicamos que vamos a crear una nueva aplicación importando el código desde Git:

![importar_codigo_git.png](Practica/importar_codigo_git.png)

A continuación, solo tenemos que indicarle el repositorio en el que se encuentra el código, y algunas pequeñas configuraciones (puerto en el que escucha la aplicación, nombre de la misma, etc):

![url_git.png](Practica/url_git.png)

Una vez que hemos terminado de añadir esa configuración, le damos a crear y listo. Con esto ya se encarga Openshift de todo lo demás (crear deployment, replicasets, pods, servicios, rutas, etc):

![flask_temperaturas.png](Practica/flask_temperaturas.png)

Pasado un tiempo, se desplegarán los pods y podremos acceder a la aplicación a través del servicio y la ruta que crea automáticamente Openshift:

![ruta_flask.png](Practica/ruta_flask.png)

![flask_pagina.png](Practica/flask_pagina.png)

Como hemos visto, hemos podido desplegar una aplicación y ponerla en funcionamiento de forma muy sencilla, sin tener que crear por nuestra cuenta ninguno de los recursos que tendríamos que haber creado si hubiéramos tenido que desplegarla en Kubernetes o Docker (ReplicaSet, Service, Deployment, Dockerfile, etc). Sin embargo, esto no es todo. Openshift también nos ofrece la posibilidad de implantar las aplicaciones de forma continua, lo que se conoce como "Despliegue Continuo". Para ello nos ofrece varias posibilidades, de entre las cuales me he decantado por usar el `Webhook` de Github, el cual avisará a Openshift cada vez que se produzca un cambio en un repositorio.

Hacer esto en un cluster real es más sencillo que hacerlo en local, ya que normalmente las empresas disponen de repositorios privados y dichos repositorios tienen acceso al cluster de Openshift. Sin embargo, al tener nuestro cluster desplegado en CRC y en local, Github no puede comunicarse con él, por lo que no puede enviar el Webhook. Para solventar este problema, vamos a usar una herramienta gratuita llamada `ngrok`, la cual nos creará un túnel http a través del cual Github podrá comunicarse con nuestro cluster de Openshift local.

Para ello, una vez que hemos instalado la herramienta, ejecutamos el siguiente comando **dentro** de la máquina virtual que crea CRC (para que funcione no debemos cortar la ejecución del comando en ningún momento):

```
ngrok http https://localhost:6443
```

Una vez hecho esto, nos creará lo siguiente:

![ngrok1.png](Practica/ngrok1.png)

El enlace marcado en rojo es el que tenemos que añadir a la configuración del `Webhook` de Github. También debemos añadir lo siguiente en la configuración: `/apis/build.openshift.io/v1/namespaces/prueba/buildconfigs/flask-temperaturas/webhooks/<SECRET>/github` (la ruta del `Webhook` nos la indica el propio build de la aplicación). Así pues, la configuración del `Webhook` queda de la siguiente forma:

![webhook1.png](Practica/webhook1.png)

Ahora, cada vez que se haga un `push` al repositorio, enviará mensaje a Openshift avisando del cambio, el cual generará un nuevo build:

![dc1.gif](Practica/dc1.gif)

Como vemos, al hacer un cambio en el repositorio, se ha activado una nueva build. Cuando termine, si accedemos a la página de la aplicación, podemos ver que el cambio se ha producido correctamente:

![flask_pagina2.png](Practica/flask_pagina2.png)

## Creación de una aplicación a partir de un Dockerfile

Anteriormente hemos comprobado como Openshift puede crear una aplicación a partir del código fuente de la misma y una imagen base. Sin embargo, esta no es la única opción que nos ofrece Openshift para crear las aplicaciones. Ahora veremos otra de las opciones: crear una aplicación a partir de un Dockerfile.

Así pues, he creado un Dockerfile a partir del cual se desplegará una aplicación escrita en `php` llamada `bookmedik`. Dicho Dockerfile se encuentra en este [repositorio de Github](https://github.com/DanielPG25/bookmedik_openshift.git). Esta aplicación necesita de una base de datos para poder funcionar, por lo que en primer lugar he creado dicha base de datos usando como base la imagen de mariadb que nos ofrece openshift:

![mariadb.png](Practica/mariadb.png)

Dicha imagen es bastante simple de configurar, ya que lo único que nos pide son las variables de entorno necesarias para configurar mariadb (contraseña de root, nombre de la base de datos, etc):

![mariadb2.png](Practica/mariadb2.png)

Una vez hecho esto, ya podemos ponernos a crear la aplicación de bookmedik. Para ello, al igual de hicimos anteriormente, le indicamos el repositorio en el que se encuentra el fichero Dockerfile:

![bookmedik_docker.png](Practica/bookmedik_docker.png)

Como vemos, Openshift detecta automáticamente el Dockerfile y nos indica que esa es la opción deseable para construir la aplicación. Solo debemos indicarle el puerto en el cual escuchará la aplicación. Así pues, le indicamos que queremos que construya la aplicación a partir de dicho Dockerfile, lo cual generará todos los recursos y procesos necesarios para crear la aplicación (build, deployment, services, etc). Pasado un tiempo, ya tendremos creada y lista nuestra aplicación `bookmedik`:

![bookmedik.png](Practica/bookmedik.png)

Si entramos en la ruta que se ha creado automáticamente, nos encontramos a la aplicación funcionando perfectamente:

![bookmedik1.png](Practica/bookmedik1.png)

![bookmedik2.png](Practica/bookmedik2.png)

El despliegue continuo se configura exactamente de la misma forma forma que en el paso anterior: activar ngrok, cambiar la url en el webhook de Github, etc. Así pues, una vez hecho esto, cada vez que hagamos un cambio en el repositorio, Github avisará a Openshift y volverá a saltar el build y el despliegue de la aplicación:

![dockefile.gif](Practica/dockefile.gif)

Y si entramos en la aplicación, vemos que se ha producido el cambio:

![bookmedik_afterhook.png](Practica/bookmedik_afterhook.png)

Como vemos, desplegar aplicaciones tanto desde el código fuente usando Image2Source, como desplegarlas desde un fichero Dockerfile es bastante sencillo, lo que proporciona muchas posibilidades a los desarrolladores que trabajen con Openshift y no quieran/puedan aprender sobre como funiona Kubernetes y/o Docker. 

## Creación de una aplicación a partir de una Plantilla

Este método es algo más avanzado que los dos anteriores, y se escapa un poco de la filosofía que hemos presentado antes de que los desarrolladores no tenían por qué conocer como funciona Kubernetes para saber como desplegar sus aplicaciones. Sin embargo, debido a la gran potencia y versatilidad que ofrece, vamos a presentar como desplegar una aplicación usando una plantilla (Template). 

Una plantilla es simplemente una representación escrita de los recursos que va a necesitar nuestra aplicación, escrita en un fichero "yaml" o "json". Es básicamente un maniefiesto en el cual aparecen todos los recursos que serán necesarios para desplegar nuestra aplicación. Dichos recursos, en los dos apartados anteriores los creaba automáticamente Openshift. Entonces, ¿cuál es la ventaja de usar plantillas?

La principal ventaja de crear plantillas radica en la gran versatilidad y grado de personalización que podemos dar a los recursos que creemos. También nos ofrece la posibilidad de agrupar recursos necesarios para una misma aplicación. Así pues, en el apartado anterior, tenía que crear primero los recursos de la base de datos que necesita `bookmedik` y después los recursos del propio `bookmedik`. Si por alguna razón quisiera volver a crear dicha aplicación en otro proyecto, tendría que volver a realizar los mismos pasos, lo que a la larga consumiría bastante tiempo. 

Es por ello que usamos plantillas para agrupar recursos y ahorrar tiempo, ya que si hubiéramos usado una plantilla para desplegar la aplicación, solo tendríamos que seleccionar dicha plantilla y tanto la aplicación en sí como la base de datos se crearían a la vez y en un momento. Dicho esto, en este apartado vamos a desplegar la aplicación `Wordpress` junto con una base de datos `MySQL` usando la siguiente [plantilla](Practica/wordpress-template.yaml), en la cual hemos configurado también el almacenamiento persistente tanto para la base de datos como para los ficheros (imágenes, audios, vídeos, etc) que subamos a nuestro `Wordpress`.

Para poder usar dicha plantilla, nos dirigimos al apartado de `Add+` o le damos al botón de `+` en la parte superior de la pantalla:

![anadir_plantilla.png](Practica/anadir_plantilla.png)

Una vez hecho esto, simplemente tenemos que copiar el contenido del fichero `yaml` y crear la plantilla. Habiendo realizado esto, ya podemos hacer uso de nuestra plantilla desde el catálogo:

![uso_plantilla.gif](Practica/uso_plantilla.gif)

Como vemos, al iniciar la plantilla, se han creado automáticamente los dos despliegues que definimos en la misma: el de `MySQL` y el de `Wordpress`. También se han creado todos los recursos que definimos en la misma, tales como rutas, volúmenes, imagestreams, etc. De esta forma, cada vez que tengamos que volver a desplegar nuestra aplicación, simplemente tendremos que volver a usar la misma plantilla, sin necesidad de tener que crear cada uno de esos despliegues a mano. Si entramos en la ruta que se ha creado, podremos ver la pantalla de instalación de `Wordpress`:

![wodpress-instalacion.png](Practica/wodpress-instalacion.png)

Una vez completada la instalación, podemos ver nuestra aplicación funcionando sin problemas:

![wordpress-inicio.png](Practica/wordpress-inicio.png)

Como hemos definido unos volúmenes en la plantilla, tanto para guardar la información de la base de datos como para guardar la información relativa a nuestro despliegue de `Wordpress` (imágenes que subamos, configuración del idioma, etc), el almacenamiento es persistente, lo que quiere decir que si hay algún error y necesitamos eliminar la aplicación y volver a desplegarla, toda nuestra información y configuración seguirá ahí. 

Para configurar el despliegue continuo, los pasos son exactamente los mismos que la primera vez: activar ngrok, cambiar la url en el webhook de Github, etc. Así pues, una vez hecho esto, cada vez que hagamos un cambio en el repositorio, Github avisará a Openshift y volverá a saltar el build y el despliegue de la aplicación:

![dc_wordpress.gif](Practica/dc_wordpress.gif)

Con esto hemos comprobado como Openshift es capaz de desplegar aplicaciones desde el código fuente (Image2Source), desde un Dockerfile o usando una plantilla. Todas las opciones son válidas y dependerá de nosotros y del trabajo que estemos realizando el elegir cual de ellas usar para cada ocasión.