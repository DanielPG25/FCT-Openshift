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

