# Casos Prácticos

## Aplicación basada en python

En primer lugar, vamos a comenzar con una aplicación simple escrita en pyhton. Para ello he elegido la aplicación desarrollada por José Domingo Muñoz de "temperaturas_flask", la cual la podéis encontrar en el siguiente [repositorio](https://github.com/josedom24/flask_temperaturas). Esta aplicación nos muestra la temperatura máxima y mínima del día en el municipio de Sevilla que le indiquemos. Así pues, lo primero que hemos hecho ha sido hacer un fork del repositorio y actualizar el fichero "requirements.txt", ya que la versión de los paquetes estaban desactualizadas y darían problemas en openshift:

```
Flask==2.0
itsdangerous==2.1.2
Jinja2==3.1.1
lxml==4.6.3
MarkupSafe==2.1.1
Werkzeug==2.1.0
```

Una vez que hemos hecho esto, si entramos en la 
