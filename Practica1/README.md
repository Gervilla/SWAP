# Practica 1: Preparacion del entorno de trabajo

En esta practica pondremos a punto nuestras maquinas virtuales y las herramientas necesarias para comenzar las practicas, es mas que nada, una practica de introduccion.

Si durante el desarrollo de esta o las siguientes practicas no tenemos una aplicacion instalada, escribiremos:

`sudo apt-get install "nombre de la aplicacion"`

## Creando las maquinas virtuales

Para el desarrollo de estas practicas se ha usado VirtualBox.

Primero debemos crear una maquina ubuntuSWAP1 y le instalamos ubuntu Server. Una vez hecho esto, cerramos dicha maquina y la clonamos. Debemos cambiar el nombre de esta nueva maquina (ubuntuSWAP2) en VirtualBox, y dentro de esta cambiaremos el nombre en en `/etc/hosts` y en `/etc/hostname`.

Para cambiar la IP y configurar la red entramos en `/etc/network/interfaces` y modificamos el archivo de manera que quede asi:

![img](https://github.com/Gervilla/SWAP/blob/master/Practica1\images\1_network_interfaces.PNG)
La IP tiene que ser la que cada uno quiera, este es un ejemplo de la de mi maquina.

Debemos hacer todo esto con ambas maquinas. La linea de gateway esta comentada ya que muchas veces da problemas.

## Conexion con SSH

Para conectarnos desde la maquina 1 a la dos escribimos `ssh` seguido del nombre de usuario, `@`, y la IP de la otra maquina. En mi caso desde la maquina ubuntuSWAP1:

`ssh gervilla@192.168.1.112`

Nos preguntara si estamos seguros de querer conectarnos, le daremos que si y nos pedira la contraseña. Hecho esto estara hecha la conexion por ssh:

![img](https://github.com/Gervilla/SWAP/blob/master/Practica1\images\2_prueba_ssh.PNG)

## Obtener un HTML con CURL

Esto nos va a permitir obtener un HTML de la otra maquina. Escribiremos:

`curl http://192.168.1.112/index.html | tail`

En este ejemplo, la maquina 1 perdira a la 2 el archivo index.html. el comando `tail` lo ponemos para mostrar por pantalla solo el final del archivo, ya que es muy extenso. Analogamente podemos usar`head`

![img](https://github.com/Gervilla/SWAP/blob/master/Practica1\images\3_curl_index.PNG)

Final de la practica 1.