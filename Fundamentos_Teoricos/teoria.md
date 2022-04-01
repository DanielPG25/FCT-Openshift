## Fundamentos Teóricos

### ¿Qué es el PaaS?

El "PaaS" (*Platform as a Service* o Plataforma como Servicio) es una modalidad de Cloud Computing especialmente pensada para desarrolladores y programadores, ya que permite a los mismos desarrollar sus aplicaciones sin tener que preocuparse por la infraestructura ni el *middleware*, herramientas de desarrollo, administración de bases de datos, etc. Básicamente ofrecen al desarrollador/programador total libertad para crear sus aplicaciones sin tener que preocuparse por nada más. Existen otras modalidades de Cloud Computing como son la Infraestructura como Servicio ("IaaS") y el Software como Servicio ("SaaS"). Las diferencias entre ellos las podemos apreciar en la siguiente imagen:

![iaas-paas-saas-diagram.png](./iaas-paas-saas-diagram.png)

Como podemos ver, en el caso del "IaaS", el proveedor se encarga únicamente de mantener la infraestructura, dejando el resto en manos del usuario, mientras que en el "SaaS", el proveedor de encarga de todo, de forma que usuario solo debe preocuparse de usar el software que ha contratado.

### ¿Qué es OpenShift?

Actualmente, OpenShift es considerado como una distribución completa de Kubernetes, y un servicio de *Cloud Computing* de tipo PaaS. Con OpenShift, los desarrolladores y programadores pueden llegar a desplegar sus aplicaciones directamente desde el código fuente, sin la necesidad de conocer como funciona internamente OpenShift. 

OpenShift utiliza Kubernetes y Docker para hacer el despliegue de las aplicaciones en contenedores, integrando de forma muy eficaz diferentes lenguajes de programación en los que los desarrolladores pueden escribir y crear sus aplicaciones. OpenShift se encargará de desplegar la aplicación indicada y de mantener los servicios subyacentes a la aplicación, a la vez que garantiza la escalabilidad de la aplicación según sea necesario.

Debido a las características de OpenShift vamos a poder desplegar nuestras aplicaciones en diferentes entornos (desarrollo, producción, etc), pudiendo incluso programar a través de OpenShift una integración continua o un despliegue continuo, simplificando el trabajo que de otra forma tendríamos que realizar manualmente si usáramos Kubernetes de forma independiente. Hay varias formas formas de comunicarse con OpenShift (CLI, aplicación web, API RESTFUL).

Una de las principales ventajas que ofrece OpenShift es que podemos desplegar directamente las aplicaciones que desarrollemos utilizando la funcionalidad *Source2Image*. Esta funcionalidad nos permite seleccionar un repositorio en el cual se encuentra el código de la aplicación y una imagen base de las que nos ofrece OpenShift o que hayamos creado nosotros, y combinando estas dos cosas, ya se encarga Openshift de todo lo que conlleva el despliegue de la aplicación (compilación, descarga de paquetes necesarios, creación de la ruta de acceso a la misma, comunicación entre los contenedores, etc). De esta forma, es posible que los desarrolladores se centren exclusivamente en el desarrollo de la aplicación, dejando todo lo demás en manos de OpenShift.

Debido a que OpenShift es un producto de Red Hat, la mayoría de las opciones para practicar y aprender a usar OpenShift son de pago. Red Hat nos ofrece una capa gratuita temporal muy limitada (uno o dos meses), por lo que para este proyecto vamos a tener opciones, las cuales desarrollaremos con más profundidad en el siguiente apartado. 

### ¿Qué es Ngrok?

Ngrok es una herramienta que nos permite exponer al exterior servidores que se encuentran en una red local a través de diversos tipos de túneles (HTTP, TLS, TCP, etc). Esto nos permite hacer que nuestro servidor de Openshift pueda recibir notificaciones desde fuera de la red local, lo que nos será muy útil, sobre todo a la hora de configurar los `webhooks` de Github.

Es una herramienta de pago que ofrece una capa gratuita, que será la que usaremos en el proyecto. Podemos encontrar más información sobre "Ngrok" en el siguiente [enlace](https://ngrok.com/docs).











https://www.redhat.com/es/topics/cloud-computing/what-is-paas

https://azure.microsoft.com/es-es/overview/what-is-paas/