# Instal·lació d'aplicacions web en contenidors
## Creació del contenidor
```console
lxc launch ubuntu:20.04 elmeucontenidor
```

## Executar el contenidor
```console
lxc exec elmeucontenidor bash
```

## Exportar i importar un contenidor
Com exportar i importar un contenidor

https://serverfault.com/questions/759170/copy-lxd-containers-between-hosts

## Instal·lar apache2, mysql i algunes llibreries al contenidor '''root@elmeucontenidor'''

```console
 apt update
 apt upgrade
 apt install apache2
 apt install mysql-server
 apt install php libapache2-mod-php
 apt install php-fpm php-common php-mbstring php-xmlrpc php-soap php-gd php-xml php-intl php-mysql php-cli php-ldap php-zip php-curl
```

# Configurem apache2

Activem el mòdul proxy_fcgi:
 a2enmod proxy_fcgi

Activem la configuració de php-fpm:
 a2enconf php-fpm

Reiniciem el servidor:
 systemctl restart apache2

= Creem una base de dades i un usuari al MySQL =

 https://elpuig.xeill.net/Members/vcarceler/articulos/mysql-en-un-contedor-lxd/index_html

 https://elpuig.xeill.net/Members/vcarceler/articulos/instalacion-basica-de-wordpress-en-ubuntu-20.04/index_html

Accedim al MySQL amb l'usuari root
 mysql -u root

Creem la base de dades amb el nom que volgueu, en el meu cas '''lamevabasededades'''
 CREATE DATABASE lamevabasededades;

Creem un usuari anomenat '''elmeuusuari''', li posem el password '''elmeupassword''' i li donem privilegis sobre la nostra base de dades '''lamevabasededades'''
 CREATE USER 'elmeuusuari'@'localhost' IDENTIFIED WITH mysql_native_password BY 'password';

 GRANT ALL ON lamevabasededades.* to 'elmeuusuari'@'localhost';

 exit

Comprovem que tot ha funcionat bé
 mysql -u elmeuusuari -p

Un cop ja tenim la base de dades i el nou usuari que utilitzarem ja podem continuar la nostra instal·lació. Primer sincronitzem la carpeta on estarà la web al contenidor '''/var/www/html''' amb una carpeta a la màquina host, per comoditat.

= Crea un parell de claus al host =
 '''@host'''

 ssh-keygen -f ~/.ssh/elmeucontenidor -N ""

= Mostra la clau-pública generada al host =
 '''@host'''

 cat ~/.ssh/elmeucontenidor.pub

= Selecciona la clau pública i copía-la al contenidor=

Enganxa-la al fitxer '''/root/.ssh/authorized_keys''' del contenidor el contingut de la clau pública.
 '''root@elmeucontenidor'''

 vim /root/.ssh/authorized_keys

= Crea una carpeta anomenada '''elmeucontenidor''' al host =
 '''@host'''

 mkdir ~/elmeucontenidor

= Esbrina l'adreça IP del contenidor =
 '''root@contenidor'''

 ip a

També pots veure la IP del contenidor fent '''lxc list'''

= Sincronitza la carpeta '''elmeucontenidor''' del host amb la carpeta '''/var/www/html''' del contenidor =
Substitueix la IP de la comanda per la IP que tingui el teu contenidor:
 '''@host'''

 sshfs root@10.161.122.237:/var/www/html ~/elmeucontenidor

Dins de la carpeta '''elmeucontenidor''' hauria d'apareixer l'index.html que ha creat '''apache2''' en la instal·lació.

= Copiem l'aplicació web que volem instal·lar a la nostra carpeta i actualitzem els permisos =

Baixem l'arxiu comprimit de l'aplicació que hem baixat d'Internet:

 '''@host'''
 cp ~/Baixades/aplicacio.zip ~/elmeucontenidor
 cd ~/elmeucontenidor
 unzip aplicacio.zip


Un cop copiat i descomprimit l'arxiu canviem els permisos de tota la carpeta /var/www/html
 '''root@contenidor'''

 chown -R www-data:www-data /var/www/html
 chmod -R 775 /var/www/html


= Accedim a l'instal·lador de l'aplicació mitjançant el navegador web =
Poseu la IP del vostre contenidor.
 http://10.161.122.237