# Escenario del proyecto

Openshift es una herramienta de pago de Red Hat, por lo que a la hora de utilizarla, si no queremos pagar, tenemos básicamente dos opciones disponibles:

1. Minishift
2. CodeReady Containers (CRC)
   
## Minishift

Minishift es una herramienta de software libre que crea una máquina virtual con un solo cluster de Openshift. No necesita de registro ni subscripciones en ninguna página. Esta herramienta necesita de una software de virtualización para poder funcionar, admitiendo los siguientes softwares:

* Windows:
    + [Hyper-V](https://docs.microsoft.com/en-us/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v)

* Linux:
    + [KVM](https://en.wikipedia.org/wiki/Kernel-based_Virtual_Machine)

* MacOS:
    + [hyperkit](https://github.com/moby/hyperkit)

* Todos los anteriores:
    + [VitualBox](https://www.virtualbox.org/wiki/Downloads) (A partir de la versión 5.1.12)

Esta es una gran herramienta para realizar pruebas en local, pero cuenta con el inconveniente de que la versión de Openshift que utiliza es la 3 (en el momento de escribir esto). Esto, para la realización de este proyecto es un gran incoveniente, ya que el cambio de la versión 3 a la 4 de Openshift es bastante grande e influye en muchas cosas. Si aún así, no tienes problemas con este inconveniente y quieres usar "minishift", el siguiente [enlace](https://docs.okd.io/3.11/minishift/getting-started/index.html) te dará más información.

## CodeReady Containers (CRC)

CodeReady Containers es una herramienta, que al igual que ocurre con "minishift" despliega una máquina virtual en la que se instala un cluster de Openshift de un solo nodo. Esta herramienta es ofrecida por la propia Red Hat, por lo que es necesario tener, como mínimo, una cuenta gratuita en Red Hat. La máquina que crea tiene los siguientes requisitos de hardware: 9 GB de ram, 4 vcpu y 35 GB de disco duro disponible. Debido a esto, es remendable tener una máquina algo potente para poder usar herramienta, ya que esos son los requisitos de la máquina virtual, no de la antitriona. También es necesario que el sistema operativo anfitrión sea Centos (versión 7.5 o mayor), Red Hat o Fedora (las dos últimas versiones estables).

La gran ventaja que ofrece esta herramienta con respecto a "minishift", es que podemos usar la versión 4 de Openshift, por lo que es una herramienta que nos ofrece la capacidad de administrar y utilizar un cluster de Openshift con todas las funcionalidades que ofrece dicha versión. Es debido a esto, que para la realización de este proyecto vamos a utilizar esta herramienta y no "minishift".

--------------------------------------------------

## Escenario

Dicho esto, vamos a explicar como montar el escenario en el que desarrollaremos el proyecto:

1. En primer lugar, he creado una máquina virtual con el sistema operativo Centos 8, con 10 GB de ram, 6 vcpu y 60 GB de disco duro.
2. Nos aseguramos de que nuestro usuario (no `root`) esté en el grupo de `sudoers` (tenga permisos de `root`).
3. Instalamos los paquetes necesarios:
   
```
sudo dnf install -y libvirt NetworkManager
```

4. Descargamos crc:

![descarga.png](descarga.png)

5. Descomprimimos el fichero:

```
tar xvf crc-linux-amd64.tar.xz
```

6. Dentro hay un binario llamado `crc`. Este binario lo movemos a algún directorio que se encuentre en el PATH:

```
sudo install crc /usr/local/bin/crc
```

7. Ejecutamos el siguiente comando, el cual comprobará si la máquina cumple con los requisitos y realizará la configuración necesaria para instalar la máquina virtual:

```
crc setup
```

8. Una vez que haya finalizado el comando anterior, debemos ejecutar el siguiente para levantar la máquina virtual y el cluster:

```
crc start
```

9. Durante la primera inicialización, nos pedirá un `pull secret`, para verificar que estamos registrados en Red Hat. Dicho `pull secret` lo podemos encontrar en la misma página de donde descargamos CRC:

![pullsecret.png](pullsecret.png)

10. A continuación, instalamos el comando `oc` y ejecutamos el siguiente comando:

```
oc adm policy add-scc-to-user anyuid -z default
```

*Nota:* El `default` lo sustituimos por el "namespace" o proyecto con el que estemos trabajando. 

El comando anterior nos permite crear contenedores que se ejecutan como `root`, algo que normalmente (por seguridad) no está permitido en Openshift. En un contexto real, un administrador daría los permisos individuales o de grupo que los desarrolladores necesitaran, y se usarían imágenes adaptadas a Openshift, por lo que el uso de dicho comando no sería necesario, pero para el contexto del proyecto nos viene muy bien para hacer las pruebas y prácticas que necesitemos.

Así pues, hemos terminado de crear el escenario con el que trabajaremos a lo largo del proyecto.