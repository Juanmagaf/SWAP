# Práctica 2:

En esta entrega se nos pide saber aprender a copiar archivos mediante ssh,
para así poder clonar contenido entre máquinas virtuales de forma que en caso
de que en un problema real tengamos una máquina funcionando y posteriormente
falle la principal, entonces la máquina secundaria pueda funcionar de forma
directa en caso de que la principal falle. Para el proceso de copia usaremos
una tarea cron que haga la orden del ssh (rsync) y configuraremos el ssh para
que el acceso sea sin tener que poner la contraseña todo el tiempo.


Primero instalamos *rsync* para posteriormente crear ficheros locales en un
equipo remoto:

**sudo apt install rsync**

Una vez hemos instalado *rsync*, hacemos que el usuario de la máquina principal
sea dueño de los archivos de su espacio web:

**sudo chown swap1718:swap1718 -R /var/www**

Dado que en ambas máquinas tenemos los mismos archivos creados de la práctica 1,
entonces en la máquina principal creamos creamos el archivo *hola1.html* para
que cuando copiemos los archivos de la principal a la secundaria sepamos que lo
hemos hecho bien.

**cd /var/www/html && touch hola1.html && cd /home/swap1718**

Una vez tenemos hecho esto, ya podemos usar *rsync*:

**rsync -avz -e ssh 192.167.175.131:/var/www/ /var/www/**

![Image](https://github.com/Juanmagaf/SWAP/blob/master/Pr%C3%A1cticas/Practica2/usorsync.PNG)

Una vez comprobado que funciona, ahora vamos a generar la clave ssh para que así
la máquina secundaria pueda acceder sin tener que introducir la contraseña en
cada acceso. Generamos la clave:

**ssh-keygen -b 4096 -r rsa**

![Image](https://github.com/Juanmagaf/SWAP/blob/master/Pr%C3%A1cticas/Practica2/keygen2.PNG)

Desde la máquina secundaria copiamos la clave:

**ssh-copy-id 192.168.175.131**

![Image](https://github.com/Juanmagaf/SWAP/blob/master/Pr%C3%A1cticas/Practica2/pswcopiada.PNG)

Y una vez hecho esto ya accedemos con ssh:

**ssh 192.168.175.131**

Esto no sólo nos permite acceder sin utilizar la pass, sino que tras usar el shh,
podemos añadir comandos bash para que se ejecuten:

**ssh 192.168.175.131 uname -a**
**ssh 192.168.175.131 ls /var/www/**

![Image](https://github.com/Juanmagaf/SWAP/blob/master/Pr%C3%A1cticas/Practica2/sshcomandos.PNG)

Lo último que queda por hacer es que la máquina secundaria haga el *rsync* con
una tarea cron, y para ello tenemos que editar el archivo */etc/crontab*:

**sudo nano /etc/crontab**

Y añadimos:

**00 * *** swap1718 rsync -avz -r ssh 192.168.175.131:/var/www/ /var/www/**

Una vez añadimos la línea, el fichero quedaría:

![Image](https://github.com/Juanmagaf/SWAP/blob/master/Pr%C3%A1cticas/Practica2/crontab.PNG)


Guardamos y ya está en funcionamiento la tarea cron.


Práctica realizada por:

[Alberto Argente del Castillo Garrido](https://github.com/AlArgente/SWAP)

[Juan Manuel González-Aurioles Fernández](https://github.com/Juanmagaf/SWAP)
