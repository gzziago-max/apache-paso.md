# apache-paso.md
## Paso 1. Crear las carpetas y paginas

Primero creamos las carpetas donde vamos a guardar las paginas

sudo mkdir -p /var/www/smr/web /var/www/smr/intranet

Despues creamos las dos paginas HTML

```bash
echo "<h1>Bienvenidos a SMR</h1>" | sudo tee /var/www/smr/web/index.html
echo "<h1>Intranet de SMR</h1>" | sudo tee /var/www/smr/intranet/intranet.html
```

## Paso 2. Crear el usuario de la intranet

Instalamos las herramientas necesarias

sudo apt install apache2-utils -y

Creamos el usuario alumno y ponemos una contraseña

sudo htpasswd -c /etc/apache2/.htpasswd alumno

## Paso 3. Configurar el puerto 9999

Entramos al archivo de configuracion

sudo nano /etc/apache2/ports.conf

Añadimos el puerto 9999 debajo del 80

```apache
Listen 80
Listen 9999
```

Guardamos los cambios y salimos

## Paso 4. Configurar los VirtualHost

Abrimos el archivo donde vamos a configurar las paginas

sudo nano /etc/apache2/sites-available/smr.conf

Ponemos esta configuracion

```apache
<VirtualHost *:80>
    ServerName www.smr.com
    DocumentRoot /var/www/smr/web
</VirtualHost>

<VirtualHost *:9999>
    ServerName www.smr.com
    DocumentRoot /var/www/smr/intranet
    DirectoryIndex intranet.html

    <Directory /var/www/smr/intranet>
        AuthType Basic
        AuthName "Intranet SMR"
        AuthUserFile /etc/apache2/.htpasswd
        Require valid-user
    </Directory>
</VirtualHost>
```

Con esto la pagina principal funciona en el puerto 80 y la intranet en el 9999 con contraseña

## Paso 5. Activar el sitio y reiniciar Apache

Primero activamos nuestra pagina

sudo a2ensite smr.conf

Despues desactivamos la que viene por defecto

sudo a2dissite 000-default.conf

Comprobamos que no tengamos errores

sudo apachectl configtest

Si esta todo bien tiene que salir Syntax OK

Por ultimo reiniciamos Apache

sudo systemctl restart apache2
