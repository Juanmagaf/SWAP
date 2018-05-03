# Práctica 4:

En esta práctica se nos pide primero configurar generar un certificado SSL
autofirmado para así poder configurar el acceso por HTTPS. Para poder generar
el certificado tenemos que ejecutar las siguientes órdenes:

**sudo a2enmod ssl**
**sudo service apache2 restart**
**sudo mkdir /etc/apache2/ssl**
**sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/apache2/ssl/apache.key -out /etc/apache2/ssl/apache.crt**

Una vez empezamos a instalar el certificado, tenemos que añadir los datos del mismo,
para ello introducimos los siguiente:

*ES*
*Granada*
*Granada*
*swap*
*swap*
*swap*
*info@swap.es*

![Image](https://github.com/juanmagaf/SWAP/blob/master/Pr%C3%A1cticas/P4/opensslkeygen.PNG)

Una vez hemos instalado el certificado, tenemos que indicarle al sistema que los tenemos,
para ello lo que hacemos es acceder al archivo de configuración *default-ssl*,
para ello ejecutamos:

**sudo nano /etc/apache2/sites-avaiable/default-ssl.conf**

Y dentro del archivo añadimos tras la línea SSLEngine On:

**SSLCertificateFile /etc/apache2/ssl/apache.crt**
**SSLCertificateKeyFile /etc/apache2/ssl/apache.key**

![Image](https://github.com/juanmagaf/SWAP/blob/master/Pr%C3%A1cticas/P4/default-ssl-conf.PNG)

Y después de esto activamos default-ssl y reiniciamos apache:

**sudo a2ensite default-ssl**
**sudo service apache2 reload**

Para comprobar que ha funcionado correctamente probamos acceder por https
a través de cURL:

**curl -k https://192.168.175.131/index.html**

Una vez hemos comprobado que funciona, copiamos los certificados de la máquina 1
a la máquina 2 y para ello desde la máquina 2 hacemos:

**scp 192.168..175.131:/etc/apache2/ssl/apache.* /tmp**

Una vez lo tenemos en temporal, se ha puesto aquí para que no de fallos por pasarlo
a la carpeta que debería, lo pasamos a la misma que debería esta, para ello:

**sudo mkdir /etc/apache2/ssl**

Y tras esto:

**mv /tmp/apache* /etc/apache2/ssl/**

Una vez hecho esto añadimos los certificados para que la máquina los reconozca
y después se reinicia apache y se comprueba con cURL que podamos acceder.

Por último nos queda añadir estos certificados al balanceador(nginx), y para ello en vez
de copiar los archivos a apache2/ssl, los copiamos en este caso al escritorio. Los
copiamos aquí y no a apache2 porque no tenemos apache2 instalado, por tanto
una vez los hemos copiado, tenemos que indicarle a través de la configuración
del cortafuegos que tiene que usar estos certificados y además tenemos que habilitar
el puerto 443, es decir, el puerto para HTTPS:

**listen 80**
**listen 443**
**ssl on**
**ssl_certificate /home/swap1718/apache.crt**
**ssl_certificate_key /home/swap1718/apache.key**

![Image](https://github.com/juanmagaf/SWAP/blob/master/Pr%C3%A1cticas/P4/nginxconf.PNG)

Por último nos quedaría la configuración del cortafuegos, para la máquina 1 o la
máquina 2, ya que con nginx nos daría fallos. Para ello en este cortafuegos
se han dejado habilitados los puertos 23 (SSH), 80 (HTTP), 443 (HTTPS), quedando
la configuración con las siguientes reglas:

**# Script: firewall.sh**
**# (1) Eliminamos todas las reglas (configuración limpia)**
**iptables -F**
**iptables -X**
**iptables -Z**
**iptables -t nat -F**

**# (2) Política por defecto: denegar todo el tráfico**
**iptables -P INPUT DROP**
**iptables -P OUTPUT DROP**
**iptables -P FORWARD DROP**

**# (3) Permitir cualquier acceso desde loalhost (interface lo)**
**iptables -A INPUT -i lo -j ACCEPT**
**iptables -A OUTPUT -o -lo -j ACCEPT**

**# (4) Abrir el puerto 22 para permitir el acceso por SSH**
**iptables -A INPUT  -p tcp --dport 22 -j ACCEPT**
**iptables -A OUTPUT -p tcp --sport 22 -j ACCEPT**

**# (5) Abrir los puertos HTTP (80) del servidor web**
**iptables -A INPUT  -p tcp --dport 80 -j ACCEPT**
**iptables -A OUTPUT -p tcp --sport 80 -j ACCEPT**

**# (6) Abrir los puertos HTTPS (443) del servidor web**
**iptables -A INPUT  -p tcp --dport 443 -j ACCEPT**
**iptables -A OUTPUT -p tcp --sport 443 -j ACCEPT**

![Image](https://github.com/juanmagaf/SWAP/blob/master/Pr%C3%A1cticas/P4/cortafuegos.PNG)



Una vez hecho esto comprobamos que la configuración dada funciona, para ello ejecutamos
el script con:

**sudo bash firewall.sh**

Y obteniendo lo siguiente:

![Image](https://github.com/juanmagaf/SWAP/blob/master/Pr%C3%A1cticas/P4/cortafuegosON.PNG)

Por última queda hacer que el cortafuegos se ejecute al arrancar el sistema, para
ello lo que hemos hecho ha sido añadir crontab el archivo de configuración, pero primero
dándole permisos de ejecución al script con:

**sudo chmod +x firewall.sh**

Y luego añadimos el script a crontab quedando así:

![Image](https://github.com/juanmagaf/SWAP/blob/master/Pr%C3%A1cticas/P4/cortafuegosIni1.PNG)

Y ahora para comprobar que funciona reiniciamos el balanceador y comprobamos
que al arrancar se ejecuta el script quedando el cortafuegos activado:

![Image](https://github.com/juanmagaf/SWAP/blob/master/Pr%C3%A1cticas/P4/iptablesIni1.PNG)

Práctica realizada por:

[Alberto Argente del Castillo Garrido](https://github.com/AlArgente/SWAP)

[Juan Manuel González-Aurioles Fernández](https://github.com/Juanmagaf/SWAP)
