# Practica 2: Sincronizacion de servidores

En esta practica veremos como copiar archivos de manera segura con `ssh` y como sincronizar ambas maquinas para replicar la informacion la una de la otra con `rsync`.

Tambien veremos como programar tareas con la herramienta `corn`.

## Copia de archivos entre maquinas

Para asegurarnos de que los archivos se mandan de forma segura usaremos `ssh`.

Para este ejemplo crearemos un archivo comprimido con `tar`, y concatenaremos con el `ssh` usando `|`. El comando quedaria asi:

`tar czf - /var/www | ssh gervilla@192.168.1.112 'cat > ~/tar.gz`.

![img](https://github.com/Gervilla/SWAP/blob/master/Practica2/images/1_tar_ssh.PNG)

Aqui vemos como la maquina 1 crea el archivo y lo envia por ssh a la otra maquina exitosamente, y luego se hace un ssh para acceder a la otra maquina y probar que el archivo se ha enviado correctamente.

## Sincronizacion con Rsync

Rsync nos permite clonar datos entre dos maquinas haciendo que cualquier cambio que hagamos en una, se vea reflejado en la otra tras usar rsync.

Primero debemos dar los premisos necesarios a nuestro usuario a las carperas que queremos sincronizar, en este caso `/var/www`.

`sudo chown gervilla:gervilla -R /var/www`

Una vez hecho esto probaremos a sincronizar en la maquina2 (sera la que pida la sincronizacion a la principal, la 1) los directorios www usando:

`rsync -avz -e ssh 192.168.1.111:/var/www /var/www`

![img](https://github.com/Gervilla/SWAP/blob/master/Practica2/images/2_rsync.PNG)

Funciona correctamente, salvo que si miramos dentro del directorio `/var/www` de la maquina 2, vemos que hay otro directorio `www`. Para evitar esto y que mezcle ambos directorios, dejaremos la orden asi:

`rsync -avz -e ssh 192.168.1.111:/var/www /var`

Podemos usar la opcion `--delete` para que tambien sincronize lo que se borra en la maquina 1, asi ademas borrariamos el directorio `www` sobrante de la maquina dos hecho anteriormente.

![img](https://github.com/Gervilla/SWAP/blob/master/Practica2/images/3_rsync2.PNG)

Vemos como se ha creado un archivo `aaaaaaaaaaaa` en la maquina 1 y al ejecutar `rsync` en la 2 queda copiado.

## Acceso sin contraseña

Ya hemos visto varias veces antes que siempre nos pide que nos autentifiquemos escribiendo la contraseña, podemos evitar eso crearemos unas claves publicas y privadas en ambas maquinas y asi despues de pasar las publicas no hara falta que volvamos a escribir las contraseñas.

Generaremos primero las claves publica y privada usando la siguiente orden en las dos maquinas:

`ssh-keygen -b 4096 -t rsa`

Nos pedira que rellenemos unos campos, nosotros dejaremos en blanco todos. Despues de esto nos saldra una imagen similar a esta:

![img](https://github.com/Gervilla/SWAP/blob/master/Practica2/images/4_keygen.PNG)

Luego, para mandarle las claves publicas a las maquinas contrarias ejecutaremos:

`ssh-copy-id gervilla@192.168.1.112` en la maquina 1.
`ssh-copy-id gervilla@192.168.1.111` en la maquina 2.

![img](https://github.com/Gervilla/SWAP/blob/master/Practica2/images/5_copyID.PNG)

Nos pedira que introduzcamos la contraseña, pero esta sera la ultima vez.

## Programacion de tareas con cron

Cron nos permite ejecutar procesos y ordenes de forma periodica y automatica en los momentos que le indiquemos. Para ello abriremos y editaremos el fichero `/etc/crontab`.

![img](https://github.com/Gervilla/SWAP/blob/master/Practica2/images/6_crontab.PNG)

En mi caso he puesto que a cada minuto de cada hora de cada dia y cada mes, de cada semana, el usuario gervilla va a ejecutar un rsync como el qu hemos visto mas atras.

`* *    * * *   gervilla rsync -avz --delete -e ssh 192.168.1.111:/var/www /var`

![img](https://github.com/Gervilla/SWAP/blob/master/Practica2/images/7_crontab2.PNG)

En este ejemplo he creado un archivo en la maquina 1 `copiamePorfi` y hemos borrado el archivo `aaaaaaaaaaa`. Al minuto, ambas cosas se han visto reflejadas en la maquina 2, el crontab funciona correctamente.

Final de la practica 2.