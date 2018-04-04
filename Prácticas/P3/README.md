# Práctica 3:

En esta prácita el objetivo es la instalación de 2 balanceadores de carga,
*Nginx* y *Haproxy*, que se encargarán de distrubuir la carga de trabajo
según indiquemos en la configuración. Además de estos dos, hemos instalado
otro opcional, *Pound*.

Tras la instalación de la máquina virtual, y haber instalado net-tools para
así saber la ip de nuestra máquina, en este casi tras hacer ifconfig sabemos que
es **192.168.175.132**, y primero para preparar la máquina hacemos:

**sudo apt update && sudo apt dist-upgrade && sudo apt autoremove**

Y una vez hecho, instalamos *nginx*

**sudo apt install nginx**

Y después podemos arrancarlo con la siguiente orden:

**sudo systemctl start nginx**

Una vez hemos instalado nginx, procedemos a la configuración de este, para ello
tenemos que editar el archivo */etc/nginx/conf.d/default.conf*, y en caso de que
no exista lo creamos, y si existe borramos todo su contenido:

**sudo nano /etc/nginx/conf.d/default.conf**

Tras acceder al archivo de configuración y haber borrado su contenido, añadimos
la configuración que se puede ver en la siguiente imagen:

![Image](https://github.com/Juanmagaf/SWAP/blob/master/Pr%C3%A1cticas/Practica3/nginxconf.PNG)

Como se puede ver hemos añadido diferentes pesos a las máquinas, para que así una
de ellas reciba más carga de trabajo que otra, cosa que es útil si disponemos de
una máquina más potente que otra, para así sacar más rendimiento a la máquina que
sea más potente.

Además de esto tenemos que editar otro archivo para completar la configuración
de *Nginx*, y se trata del archivo /etc/nginx/nginx.conf

**sudo nano /etc/nginx/nginx.conf**

Y comentamos la línea **include /etc/nginx/sites-enabled/*;** para que así
funcione como un balanceador web.

![Image](https://github.com/Juanmagaf/SWAP/blob/master/Pr%C3%A1cticas/Practica3/nginxconf2.PNG)

Una vez hecho esto iniciamos nginx:

**sudo systemctl start nginx**


Si todo ha ido bien tendremos *Nginx* funcionando correctamente, y para ello
desde una cuarta máquina haremos cURL a la máquina donde hemos instalado *Nginx*,
y lo haremos dos veces, para que así nos muestre la página de cada una de las
máquinas que tenemos en la configuración, que en este caso son 2.

Una vez hemos hecho desde una cuarta máquina ejecuamos la línea:

**ab -n 1000 -c 10 http://192.168.175.132/index.html**

Para así mostrar que está configurado correctamente.

![Image](https://github.com/Juanmagaf/SWAP/blob/master/Pr%C3%A1cticas/Practica3/pruebaABNginx.PNG)

Tras comprobar que *Nginx* funciona correctamente, procedemos a instalar *Haproxy*,
para ello en una nueva máquina, dado que sino tendriamos que apagar *Nginx* puesto
que ambas las estamos configurando para que funcionen por el puerto  80, y para
ello en la nueva máquina hacemos lo siguiente:

**sudo apt install haproxy**

Después de haberlo instalado, accedemos a la carpeta donde se encuentra el archivo
de configuración:

**cd /etc/haproxy/**

Obtenemos nuestra dirección IP con **ifconfig** y editamos el archivo de configuración:

**sudo nano haproxy.cfg**

Tras acceder borramos todo el contenido que contenga este archivo y lo dejamos
como en la siguiente imagen:

![Image](https://github.com/Juanmagaf/SWAP/blob/master/Pr%C3%A1cticas/Practica3/haproxycfg.PNG)

Una vez hemos terminado la configuración, procedemos a iniciar *Haproxy*:

**sudo /usr/sbin/haproxy -f /etc/haproxy/haproxy.cfg**

Y tras esto para comprobar que funciona correctamente podemos hacer desde otra
máquina 2 veces cURL para comprobar que funciona correctamente mostrando así
las páginas de las 2 máquinas distintas que hemos añadido en la configuración.

Por último sólo queda comprobar su funcionamiento ejecutando ab como con *Nginx*:

**ab -n 1000 -c 10 http://192.168.175.133/index.html**

![Image](https://github.com/Juanmagaf/SWAP/blob/master/Pr%C3%A1cticas/Practica3/pruebaABHaproxy.PNG)

Por último como adicional hemos instalado el balanceador *Pound*, para ello en
una nueva máquina hemos instalado *Pound* con la siguiente orden:

**sudo apt install pound**

Y ahora accedemos al archivo de configuración:

**sudo nano /etc/pound/pound.cfg**

Una vez estamos dentro, bajamos hasta la parte de ListenHTTP, modificando el
Adress que encontramos aquí por la de la máquina donde hemos instalado *Pound*,
192.168.175.134, y luego dentro de Service modificamos el BackEnd que hay ya
añadiendo una de las máquinas, y posteriormente añadimos de la misma forma
la otra máquina, y además hemos añadido prioridades a cada máquina para así
simuar el mismo caso que si tuviéramos dos máquinas distintas, siendo una de
ellas más potente que otra, quedando como en la siguiente imagen:

![Image](https://github.com/Juanmagaf/SWAP/blob/master/Pr%C3%A1cticas/Practica3/poundcfg.PNG)

Una vez hecho esto procedemos a iniciar *Pound*, para ello primero modificamos
el siguiente archivo:

**sudo nano /etc/default/pound**

E igualamos startup a 1:

![Image](https://github.com/Juanmagaf/SWAP/blob/master/Pr%C3%A1cticas/Practica3/poundiniciar.PNG)

Una vez actualizado iniciamos el servicio con:

**sudo /etc/init.d/pound start**

Si todo ha ido bien nos saldrá una línea que dirá que *Pound* está funcionando
correctamente, y para comprobar que lo hemos configurado bien ejecutamos desde
otra máquina ab:

**ab -n 1000 -c 10 http://192.168.175.134/index.html**

![Image](https://github.com/Juanmagaf/SWAP/blob/master/Pr%C3%A1cticas/Practica3/pruebaABPound.PNG)


Para ayudarnos en la configuración de *Pound* hemos seguido el siguiente [enlace](https://help.ubuntu.com/community/Pound)


Práctica realizada por:

[Alberto Argente del Castillo Garrido](https://github.com/AlArgente/SWAP)

[Juan Manuel González-Aurioles Fernández](https://github.com/Juanmagaf/SWAP)
