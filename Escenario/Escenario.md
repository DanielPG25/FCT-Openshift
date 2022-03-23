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
    + [VitualBox](https://www.virtualbox.org/wiki/Downloads). (A partir de la versión 5.1.12)

Esta es una gran herramienta para realizar pruebas en local, pero cuenta con el inconveniente de que la versión de Openshift que utiliza es la 3 (en el momento de escribir esto). Esto, para la realización de este proyecto es un gran incoveniente, ya que el cambio de la versión 3 a la 4 de Openshift es bastante grande e influye en muchas cosas. Si aún así, no tienes problemas con este inconveniente y quieres usar "minishift", el siguiente [enlace](https://docs.okd.io/3.11/minishift/getting-started/index.html) te dará más información.

## CodeReady Containers (CRC)


    