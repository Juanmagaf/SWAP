# Práctica 5:

El objetivo de esta práctica es la replicación de las bases de datos entre las
máquinas que tengamos  para que así mantener un backup de la base de datos
y que en caso de error de la máquina 1 que contiene todos los datos no perdamos
todos los datos.

Lo primero que hacemos es en la máquina 1 crear una base de datos e insertar los datos
para que así tengamos algo que replicar, de una máquina a otra.
Para crear la Base de Datos (BD) hacemos lo siguiente:

**mysql -u root -p**

Y a continuación introducimos la clave que teníamos en MySQL, y tras esto creamos
una base de datos:

**create database contactos;**
Indicamos que use la BD creada:
**use contactos**
Y como está vacía creamos la tabla datos:
**create table datos(nombre varchar(100), tlf int);**
Y añadimos datos:
**insert into datos(nombre,tlf) values("pepe",95834987);**
Para comprobar que la hemos creado correctamente hacemos un describe sobre la tabla:
**describe datos;**

![Image](https://github.com/juanmagaf/SWAP/blob/master/Pr%C3%A1cticas/P5/BDCreada.PNG)

Una vez hemos creado la base de datos en la máquina 1, vamos a replicarla a la
máquina 2 usando mysqldump, para ello desde la máquina 1 accedemos a mysql:

**mysql -u root -p**
**FLUSH TABLES WITH READ LOCK**
**QUIT**

Ahora desde la máquina 1 guardamos los datos de la BD:
**mysqldump contactos -u root -p > /tmp/contactosdb.sql**

Una vez la hemos copiado desbloqueamos la BD:
**mysql -u root -p**
**UNLOCK TABLES**
**QUIT**

Una vez hecho esto, desde la máquina 2 podemos copiar la BD:
**scp 192.168.175.131:/tmp/contactosdb.sql /tmp/**

La movemos al home de la máquina 2 y hacemos lo siguiente:
**mysql -u root -p**
**CREATE DATABASE contactosdb;**
**QUIT**
**mysql -u root -p contactodb < /home/swap1718/contactosdb.sql**

Y podemos comprobar que ha funcionado correctamente quedando:

![Image](https://github.com/juanmagaf/SWAP/blob/master/Pr%C3%A1cticas/P5/DBCopiada.PNG)

Una vez hecho esto el objetivo es hacer que la máquina 2 copie de manera automática
cada dato que se cree en la máquina 1, dentro de la misma BD claramente, para ello
tenemos que editar la configuración de mysql:

**sudo nano /etc/mysql/mysql.conf.d/mysqld.cnf**

Y una vez dentro comentamos la línea que tenga *bind-address 127.0.0.1*
También comentamos la línea que tenga *log_error = /var/log/mysql/error.log*
Y establecemos el nivel identificdor del servidor: *server-id = 1*
Activamos si no lo estaba el servicio binario descomentando la línea o
añadiéndola: *log_bin = /var/log/mysql/bin.log*

![Image](https://github.com/juanmagaf/SWAP/blob/master/Pr%C3%A1cticas/P5/CongMysqlMaestro.PNG)

Y tras eso guardamos el documento y reiniciamos el servicio:
**/etc/init.d/mysql restart**

![Image](https://github.com/juanmagaf/SWAP/blob/master/Pr%C3%A1cticas/P5/MysqlRestart.PNG)

Una vez hecho esto en la máquina 1 tenemos que hacer lo mismo en la máquina 2,
pero poniendo el identificador de la máquina 2 a 2:

![Image](https://github.com/juanmagaf/SWAP/blob/master/Pr%C3%A1cticas/P5/CongMysqlEsclavo.PNG)

Ahora queda crear el usuario en el Maestro y darle los privilegios para que pueda
darse la replicación de datos. Para ello entramos en MySQL y:
**CREATE USER esclavo IDENTIFIED BY 'esclavo';**
**GRANT REPLICATION ON SLAVE ON *.* TO 'esclavo'@'%' IDENTIFIED BY 'esclavo';**
**FLUSH PRIVILEGES;**
**FLUSH TABLES WITH READ LOCK**

![Image](https://github.com/juanmagaf/SWAP/blob/master/Pr%C3%A1cticas/P5/CrearUserEsclavo.PNG)

Una vez hecho esto, pasamos a la máquina 2 y cambiamos el maestro, y lo iniciamos
como esclavo:

![Image](https://github.com/juanmagaf/SWAP/blob/master/Pr%C3%A1cticas/P5/CreasMaestroEnEsclavo.PNG)

Por último volvemos al maestro y activamos las tablas para que se puedan añadir nuevos datos:
**UNLOCK TABLES;**

Para comprobar que funciona correctamente se han añadido datos en las máquinas
de forma independiente y mostramos los datos:

![Image](https://github.com/juanmagaf/SWAP/blob/master/Pr%C3%A1cticas/P5/ClonadoAutomatico.PNG)
![Image](https://github.com/juanmagaf/SWAP/blob/master/Pr%C3%A1cticas/P5/ClonadoAutomatico1.PNG)

Como ejercicio extra se pedía crear una configuración Maestro-Maestro, para ello
lo que se ha hecho es crear un usuario 'esclavo1' en la máquina 2, al igual que
cuando se creo en la máquina 1, y luego lo añadimos a la máquina 1 como se hizo
anteriormente para añdir el primer maestro. Una vez se ha creado el usuario,
lo que hacemos es poner desde la máquina 1 **START SLAVE**, y ya funcionaría.

Para mostrar el correcto funcionamiento, se han creado distintos datos en las tablas
desde ambas máquinas y se ha mostrado el resultado:

![Image](https://github.com/juanmagaf/SWAP/blob/master/Pr%C3%A1cticas/P5/MaestroMaestroOn.PNG)
![Image](https://github.com/juanmagaf/SWAP/blob/master/Pr%C3%A1cticas/P5/MaestroMaestroOn1.PNG)

Práctica realizada por:

[Alberto Argente del Castillo Garrido](https://github.com/AlArgente/SWAP)

[Juan Manuel González-Aurioles Fernández](https://github.com/Juanmagaf/SWAP)
