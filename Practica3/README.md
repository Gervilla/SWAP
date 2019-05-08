# Practica 3: Balanceo de carga

El objetivo de esta practica es aprender a usar un balanceador de carga para nuestros servidores, y a pasarle un benchmark para ver como reparte el trabajo y como rinden nuestras maquinas.

Primero, vamos a crear y configurar dos maquinas mas, una sera el cliente y otra el balanceador.

## Nginx 

Primero instalaremos nginx en la maquina balanceador de la forma que ya se dijo en la primera practica. es aconsejable desinstalar apache del balanceador si vamos a usar nginx o haproxy ya que si no puede dar fallos.

![img](https://github.com/Gervilla/SWAP/blob/master/Practica3\images\1_error_nginx.PNG)

Despues de eso reiniciamos el servicio (o directamente la maquina si continua sin funcionar) con `systemctl restart nginx` y luego comprobamos su estado a ver si todo funciona correctamente:

`systemctl status nginx`

![img](https://github.com/Gervilla/SWAP/blob/master/Practica3\images\2_status_nginx.PNG)

Para configurar correctamente nginx, abriremos `/etc/nginx/conf.d/default.conf`, borraremos todo el contenido y escribiremos lo siguiente:

![img](https://github.com/Gervilla/SWAP/blob/master/Practica3\images\3_config_nginx.PNG)

En esta configuracion se ha añadido al lado de cada servidor, `weight=x`, indicando la carga de trabajo que ira a cada uno, y tambien debajo de estos se ha añadido `keepalive 3`, lo que nos permite hacer las conexiones http persistentes.

Ahora crearemos en cada maquina servidora un index.html diferente para saber de que servidor viene al pedirlo con `curl` desde la maquina cliente al balanceador y saber si este funciona correctamente.

![img](https://github.com/Gervilla/SWAP/blob/master/Practica3\images\4_html.PNG)

Sin la opcion de weight, el balanceador distribuiria a partes iguales la carga:

![img](https://github.com/Gervilla/SWAP/blob/master/Practica3\images\5_balanceo_nginx.PNG)

Y con ella hara una distribucion conforme al peso de cada servidor:

![img](https://github.com/Gervilla/SWAP/blob/master/Practica3\images\5_balanceo_nginx_edit.PNG)

**CUIDADO:** Recuerda borrar o comentar el rsync escrito en el crontab en la practica anterior, ya que si no se copiara cada minuto el index.html de la maquina 1 en la dos y creeremos que no funciona bien el balanceador, creeme, me pase una hora asi.

## Haproxy

Para evitar conflictos con nginx lo desactivaremos antes de instalar haproxy: `systemctl stop nginx`. Con `systemctl status haproxy` veremos si esta activo y sin errores.

![img](https://github.com/Gervilla/SWAP/blob/master/Practica3\images\6_status_haproxy.PNG)

Como hemos hecho antes con nginx, hay que configurar haproxy, para ello abriremos y editaremos el archivo de configuracion `/etc/haproxy/haproxy.cfg`:

![img](https://github.com/Gervilla/SWAP/blob/master/Practica3\images\7_conf_haproxy.PNG)

Reiniciaremos el servicio con `systemctl stop haproxy` y lo iniciaremos con:

`sudo /usr/sbin/haproxy -f /etc/haproxy/haproxy.cfg`

## Sobrecargar al balanceador

Antes de nada, es recomendable saber como cambiar de balanceador entre nginx y haproxy. Si queremos cambiar de nginx a haproxy por ejemplo, pararemos un servicio y luego iniviaremos el otro:

![img](https://github.com/Gervilla/SWAP/blob/master/Practica3\images\8_cambio_balanceador.PNG)

Para medir el trabajo del balanceador instalaremos Apache Benchmark en el cliente, instalando `apache2-utils`. Una vez hecho esto, ejecutaremos un `top` en las dos maquinas servidoras para mirar la carga en cada una, y ya con esto, ejecutaremos lo siguiente:

`ab -n 1000 -c 10 http://192.168.1.113/index.html` (direccion IP del balanceador)

![img](https://github.com/Gervilla/SWAP/blob/master/Practica3\images\9_benchmark.PNG) 

Final de la practica 3.
