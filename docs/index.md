# Práctica 3.2 - Despliegue con Node Express y con Netlify

## Despliegue con Node Express

En primer lugar debemos asegurarnos de que Tomcat no está ejecutándose: 

```console
sudo systemctl status tomcat9	# Comprobar el estado de Tomcat
sudo systemctl stop tomcat9	# Parar Tomcat
```

A continuación instalamos Node.js y el gestor de paquetes npm:

```console
sudo apt install nodejs npm
```

Para realizar el despliegue, clonamos un repositorio de GitHub que contenga 
una aplicación Node. Nos colocamos dentro del directorio del proyecto e 
instalamos los paquetes requeridos ejecutando: 

```console
npm install
```

También es importante comprobar en el código fuente de la aplicación el puerto 
que utilizará para comunicarse. En mi caso es el puerto 3000, por lo que ejecuto: 

```console
sudo ufw allow 3000/tcp
```
Si tratamos de desplegar la aplicación, aparecerá el mensaje de error "nodemon: 
not found". Para solventar este problema, agregamos dicho paquete al archivo 
package.json ejecutando:

```console
npm install nodemon --save-dev
```

Y ahora podremos desplegar la aplicación correctamente con: 

```console
npm run start
```

Y podremos acceder a ella desde el navegador de nuestra máquina anfitriona indicando 
la IP de nuestra máquina virtual y el puerto en el que se aloja la aplicación (en 
mi caso, el 3000):

![Aplicación Node desplegada](./images/app_node_desplegada.png)


## Cuestiones sobre Node Express


## Despliegue con Netlify

En primer lugar, preparamos una aplicación simple consistente de dos archivos HTML y un
JS e inicializamos el proyecto con:

```console
npm init
```

Y podemos comprobar en local que funciona correctamente con:

```console
node aplicacion.js
```

Y accediendo desde el navegador a la IP y puerto de la aplicación (debemos asegurarnos de 
que Tomcat u otras aplicaciones no están utilizando ya ese puerto):

![Comprobación funcionamiento de la aplicación](./images/comp_inicial.png)

Alternativamente, también podemos comprobarlo con curl:

```console
curl 192.168.0.20:8080
```

Para poder desplegar sin problemas la aplicación, debemos modificar el archivo 
'package.json'. En concreto, hay que modificar el bloque 'scripts' dentro de este 
archivo para que quede de la siguiente manera:

```console
"scripts": {
	"start":"node aplicacion.js"
}
```

Teniendo en cuenta todas estas cuestiones, borramos esta aplicación simple y clonamos 
un repositorio de GitHub que contiene una aplicación de ejemplo que sí desplegaremos con 
Netlify.

Utilizaremos dos métodos para desplegar aplicaciones con Netlify: el despliegue manual 
desde el CLI (Command Line Interface) y el despliegue desde el código publicado en 
GitHub.

Antes de empezar con el despliegue, es necesario registrarse en Netlify. En mi caso, me 
registro con mi cuenta de correo, para que no se enlace mi cuenta de GitHub directamente. 

![Registro Netlify](./images/registro_netlify.png)

Netlify envía un email de confirmación, que nos reenvía a su página donde debemos 
responder a una serie de preguntas sobre nosotros (nombre, si pertenecemos a alguna 
institución o empresa, etc) donde lo único relevante es que he saltado el paso de 
enlace con GitHub, que realizaré más adelante. A continuación, pasamos a los procesos 
del despliegue en sí.

### Despliegue mediante Interfaz de Línea de Comandos

En primer lugar, será necesario generar un token de acceso personal para que podamos 
establecer una conexión segura con Netlify. Para ello, desde la página web, accedemos 
a las 'users settings' y clicamos en 'Applications'. Desde ahí podremos seleccionar 
'New access token' y se generará un nuevo token, que es una cadena de texto que 
debemos copiar y guardar.

Volviendo al terminal (conectado por SSH a nuestro servidor), debemos definir una 
variable de entorno que contenga el token generado por Netlify:

```console
export NETLIFY_AUTH_TOKEN={token_generado_por_netlify}
```

Ahora ya podemos instalar el CLI de Netlify: 

```console
sudo npm install netlify-cli -g
```

Y si tratamos de ejecutar 'netlify login', nos dirá que ya estamos loggeados porque ya 
se ha comprobado el contenido de la variable de entorno 'NETLIFY_AUTH_TOKEN'.

Este es el momento de instalar las dependencias de la aplicación, buildearla y 
desplegarla. Nos colocamos dentro del directorio de la aplicación y ejecutamos:

```console
sudo npm install
sudo npm run build
netlify deploy
```

Le indicamos que queremos crear un nuevo sitio y respondemos las preguntas que nos 
netlify nos plantea. En mi caso: 

![Pre-deploy Netlify](./images/predeploy_netlify.png)

Esa es la fase de "pre-deploy". Para llevar la aplicación al despliegue definitivo, 
ejecutamos:

```console
netlify deploy --prod
```

Y le indicamos el directorio con el contenido a desplegar. En mi caso, './build' desde 
el directorio de la aplicación. Para comprobar que el despliegue ha resultado exitoso, 
accedo a la URL que indica Netlify:

![Aplicación desplegada](./images/despliegue_netlify_cli.png)

### Despliegue mediante conexión con GitHub

Para realizar el despliegue mediante una conexión entre Netlify y GitHub, en primer 
lugar, eliminamos desde la página de Netlify la página desplegada anteriormente 
(accedemos a la configuración del site y bajamos hasta encontrar la opción 'delete site') 
y en nuestra MV, eliminamos el directorio con el contenido de la aplicación.

## Cuestiones sobre Netlify
