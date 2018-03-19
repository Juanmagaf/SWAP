# Práctica 1
En esta práctica se nos pide la instalación de 2 máquinas virtuales con Ubuntu Server. En nuestro caso utilizaremos el Ubuntu Server 17.10.

Una vez instalada la MV procedemos a instalar **LAMP** mediante las siguientes ordenes:

sudo apt-get install apache2 mysql-server mysql-client

Para comprobar como ha ido la instalación:

apache2 -v 

Como es necesario obtener la ip de las máquinas virtuales instalamos el paquete **net-tools** y así poder ejecutar **ifconfig** y obtener la ip de cada máquina virtual:

sudo apt install net-tools

También se nos pide tener instalado ssh y curl, para ello instalamos:

sudo apt install ssh sudo apt install curl

Para comprobar que Curl funciona, vamos a hacerlo mediante un html ubicado en /var/www/html llamado hola.html con el siguiente contenido:

*Esto funciona :)*

Y podemos comprobar en la siguiente imagen que funciona:

![imagen](https://github.com/Juanmagaf/SWAP/P1/curlOn.PNG)

![imagen](https://github.com/Juanmagaf/SWAP/P1/curlOn1.PNG)

Y para mostrar que funciona correctamente el ssh conectamos una máquina a otra:

![imagen](https://github.com/Juanmagaf/SWAP/P1/sshOn1.png)

Práctica realizada por:

[Alberto Argente del Castillo Garrido](https://github.com/AlArgente/)

[Juan Manuel González-Aurioles Fernández](https://github.com/Juanmagaf/)