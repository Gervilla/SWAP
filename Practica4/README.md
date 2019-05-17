# Practica 4: Seguridad: SSL y cortafuegos

Esta practica esta enfocada a la seguridad de nuestros servidores, algo imprescindible hoy en dia para defenderse de posibles ataques. Para ello configuraremos SSL y un corrtafuegos.

## Certificado SSL y configuracion

Primero necesitamos un certificado de SSL que nos verifique que nuestro servidor hace transmisiones seguras. Para ello activamos SSL en apache en nuestros servidores. Primero lo instalaremos en la maquina 1 usando:

`sudo a2anmod ssl`

![img](https://github.com/Gervilla/SWAP/blob/master/Practica4/images/1_activarSSL.PNG)

Reiniciamos el servicio con `service apache2 restart` y luego creamos un directorio `ssl` para alojar los datos del protocolo con `mkdir /etc/apache2/ssl`. A continuacion crearemos el certificado ejecutando:

`openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout/etc/apache2/ssl/apache.key -out /etc/apache2/ssl/apache.crt`

Rellenaremos los campos que nos pidan con nuestros datos como muestro en mi ejemplo.

![img](https://github.com/Gervilla/SWAP/blob/master/Practica4/images/2_keygenSSL.PNG)

Ahora editaremos el archivo `/etc/apache2/sites-available/default-ssl` y escribiremos despues de SSLEngine on las dos lineas que se ven a continuacion:

![img](https://github.com/Gervilla/SWAP/blob/master/Practica4/images/3_confSSL.PNG)

Si nos sale como que el archivo no existe y nos sale el editor vacio para crear uno nuevo, editamos `default-ssl.conf`. Activamos el sitio anterior con `a2ensite default-ssl` y reiniciamos apache: `service apache2 reload`. Despues de esto todo deberia funcionar correctamente:

![img](https://github.com/Gervilla/SWAP/blob/master/Practica4/images/4_SSL_OK.PNG)

Comprobamos ahora que se realizan las conexiones seguras mediante HTTPS. Le pediremos al servidor 1 que nos de su index.html usando curl.

`curl –k https://<ip de la máquina>/index.html`

![img](https://github.com/Gervilla/SWAP/blob/master/Practica4/images/5_pruebaSSL.PNG)

Como vemos, el servidor nos ama ya que hacemos un acceso seguro a el y asi no esta en peligro. La maquina 2 y el balanceador tambien quieren seguridad, por tanto les pasaremos el certificado creado en la maquina 1.

`scp /etc/apache2/ssl/apache.key gervilla@192.168.1.112:/home/gervilla` (usuario, IP y directorio de la maquina 2)
`scp /etc/apache2/ssl/apache.crt gervilla@192.168.1.112:/home/gervilla` (usuario, IP y directorio de la maquina 2)

![img](https://github.com/Gervilla/SWAP/blob/master/Practica4/images/6_copiaKey.PNG)

Despues de copiarlos, debemos moverlos a `/etc/apache2/ssl` y tambien configurarla como en el servidor 1. Para el balanceador, los moveremos a `/tmp` y abriremos el archivo de configuracion de nginx `/etc/nginx/conf.d/default.conf` y escribiremos lo siguiente:

![img](https://github.com/Gervilla/SWAP/blob/master/Practica4/images/7_conf_nginx.PNG)

A partir de ahora desde nuestra maquina cliente podremos hacer peticiones por HTTPS al balanceador y asi a nuestros servidores.

## Cortafuegos: Script iptables

Escribimos un script sencillo de iptables:

![img](https://github.com/Gervilla/SWAP/blob/master/Practica4/images/8_.PNG)

lo arrancamos y comprobamos que funciona correctamente con `iptables –L –n -v`.

Final de la practica 4.
