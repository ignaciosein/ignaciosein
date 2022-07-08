![logotipo de The Bridge](https://user-images.githubusercontent.com/27650532/77754601-e8365180-702b-11ea-8bed-5bc14a43f869.png  "logotipo de The Bridge")


# [Bootcamp Web Developer Full Stack](https://www.thebridge.tech/bootcamps/bootcamp-fullstack-developer/)

### HTML, CSS,  JS, ES6, Node.js, Frontend, Backend, Express, React, MERN, testing, DevOps


# Despligue de nuestra aplicación MERN en Heroku

![img](../../../../../assets/core/front/react/clase41/heroku.png)

Vamos a definir el procedimiento a seguir para hacer un despliegue de nuestra aplicación MERN en Heroku.

Antes de comenzar la estructura de directorios en nuestro proyecto debe ser la siguiente (client contiene el front con React):

```
   - client
   - controllers
   - database
   - model
   - node_modules
   - routes
    .gitignore
    .package.json
    .server.js
    .package-lock.json

```

# Configurando el server 

1. Primero vamos a configurar el archivo **package.json** situado en la raíz de nuestro proyecto

```

   - client
   - controllers
   - database
   - model
   - node_modules
   - routes
    .gitignore
    .package.json  <-----------
    .server.js
    .package-lock.json
  

```

Dentro de él tenemos que agregar la siguientes línea dentro de **"scripts"**: 

```
    "heroku-postbuild": "cd client && npm install --only=dev && npm install && npm run build"

```

Vista previa de como se vería nuestro package.json:

```json
  "scripts": {
     "start": "node server.js",
     "test": "echo \"Error: no test specified\" && exit 1", 
    "heroku-postbuild": "cd client && npm install --only=dev && npm install && npm run build"
  },

```
2. Ahora editamos el archivo .gitignore de nuestro servidor, situado en el directorio raíz:

```
 - client
   - controllers
   - database
   - model
   - node_modules
   - routes
    .gitignore   <-----------
    .package.json  
    .server.js
    .package-lock.json 
  
```

Dentro de él agregamos **"node_modules"** para que cuando hagamos el deploy no suba la carpeta a heroku:

```
  node_modules
  
```

3. En nuestro servidor debemos instalar las variables de entorno en el directorio del servidor: 

```
npm install dotenv
```
 

### Variables de entorno

Las variables de entorno son variables externas a nuestra aplicación que residen en el SO o en el contenedor de la aplicación que se está ejecutando. Una variable de entorno es simplemente un nombre asignado a un valor.

Por convención, el nombre se escribe con mayúscula y los valores son cadenas de texto, por ejemplo: **PORT=8080**.

Normalmente, nuestras aplicaciones requieren que se establezcan muchas variables de entorno para que funcionen. Al confiar en configuraciones externas, una aplicación se puede implementar fácilmente en diferentes entornos. Estos cambios son independientes de los cambios en el código, por lo que no requieren que su aplicación sea reconstruida.

Los datos que cambian según el entorno en el que se ejecuta su aplicación deben configurarse como variables de entorno. Algunos ejemplos comunes son:

- Dirección y Puerto HTTP.
- Credenciales de Base de Datos.
- Ubicación de archivos y carpetas estáticos.
- Credenciales de API's externas.

Continuemos con nuestro despliegue: 

4. Ahora abrimos el archivo que tiene la configuración de nuestro servidor, en este caso **"server.js"**

```
 - client
   - controllers
   - database
   - model
   - node_modules
   - routes
    .gitignore   
    .package.json  
    .server.js <-----------
    .package-lock.json 
  
```

Y modificamos su código, de la siguiente forma: 

```javascript

const express = require("express");
require("dotenv").config();  
const router = require("./routes/routes");

const app = express();

app.use(express.json());
app.use(express.static("build")); 
 

app.use("/", router);

 
if (process.env.NODE_ENV === "production") {
  app.use(express.static("client/build"));

  app.get("*", (req, res) => {
    res.sendFile(path.join(__dirname, "client", "build", "index.html"));
  });
}

const port = process.env.PORT || 5000;

app.listen(port, () => console.log(`Example app listening on port ${port}!`));


```

## NOTA IMPORTANTE

Las variables de entorno se crean en nuestro proyecto de node con la extensión **".env"**,aunque en este caso no es necesario agregarlas en ese archivo porque heroku nos da la opción de agregarlas desde "Settings" si queremos trabajar en local con *variables de entorno* tenemos que crear este archivo en el nodo raíz de nuestro proyecto

```
 - client
   - controllers
   - database
   - model
   - node_modules
   - routes
    .gitignore   
    .package.json  
    .server.js 
    .package-lock.json 
    .env <-----------
  
```
Y dentro de él tendriamos que colocar las variables de entorno que necesitamos, en este caso tenemos 2 variables : **NODE_ENV**(Para poner nuestra App en producción) y **PORT**(Para indicar el puerto en el que queremos que nuestro servidor se ejecute).Por lo tanto nuestro archivo **".env"** quedaría de la siguiente forma:

```
  NODE_ENV=production
  PORT=5000

```
 

Con esto nuestro servidor ya estaría configurado. Ahora procederemos a configurar nuestro cliente, en este caso React.
  

## Configurando el cliente

Un proyecto básico de React se compone de la siguiente estructura:

```

    - public
    -------favicon.ico
    -------index.html
    -------logo192.png
    -------logo512.png
    -------manifest.json
    - src
    -------App.js
    -------App.css
    -------index.js
    -------index.css
    - package.json 
    - package-lock.json
 

```

**Recuerda que nuestro cliente se encuentra en la carpeta "/client/"**

1. Tenemos que irnos a el fichero package.json 

```

    - public
    -------favicon.ico
    -------index.html
    -------logo192.png
    -------logo512.png
    -------manifest.json
    - src
    -------App.js
    -------App.css
    -------index.js
    -------index.css
    - package.json <-------------------
    - package-lock.json
 

```

Dentro de este fichero tenemos que agregar un **proxy** para que nuestro cliente sepa a donde tiene que dirigerse, es decir , en qué dirección está nuestro servidor. Y esto tenemos que agregarlo al final de nuestro archivo.

Agrega a package.json la siguiente clave y el siguiente valor. 

**Ojo: esta es la configuración por defecto wue hemos decidido, se pueden cambiar los puertos por necesidades el proyecto, pero hay que ver si Heroku lo permite**

```json
},
  "proxy": "http://localhost:5000"
  }
  
```

Ya tenemos configurados tanto nuestro **cliente** como nuestro **servidor**, ahora procederemos a hacer la conexión con Heroku.

## Despligue Heroku tras la configuración

Recuerda que para hacer esto tienes que tener instalado el cliente de Heroku, que puedes encontrar aquí: [HEROKU_CLI](https://devcenter.heroku.com/articles/heroku-cli)

1. Hacemos login desde la terminal y dentro de nuestro proyecto:

```
  heroku login
```

2. Una vez nos conectemos a nuestra cuenta de usuario de heroku, procederemos a crear nuestra app de Heroku,
para ello usaremos el siguiente comando:


```
  heroku create
```

3. Cuando ya esté creada nuestra aplicación nos devolverá la consola el siguiente resultado

```
  Creating app... done, NOMBRE_DE_LA_APLICACION_CREADA

  ---Ej: 
  Creating app... done, safe-anchorage-60433

```

4. Copiamos ese nombre de app que nos ha creado heroku de manera automática, porque vamos a usarlo a continuación.

5. Ahora inicializamos git en nuestro proyecto con el siguiente comando :

```
git init
```
6. Ahora vamos a vincular nuestro repo local con el repo remoto que tiene heroku de nuestro proyecto, usando el sigiuente comando

```javascript
heroku git:remote -a NOMBRE_DE_LA_APLICACION_CREADA

----Ej:  heroku git:remote -a safe-anchorage-60433
```

7. Para que todos vaya bien debemos crear la rama main en el repositorio de Heroku:

```
  git checkout -b main
```

8. Añadimos los archivos y hacemos efectivos los cambios en el repositorio:

```
git add .
git commit -m "Deploy APP" 
```

9. Subimos los cambios: 

```
git push heroku main
```

10. Si todo ha ido bien, la consola nos mostrará la **URL** de nuestra aplicación desplegada para que podamos visualizarla en el navegador.

[REPO_DE_LA_APLICACIÓN_MERN](https://github.com/igonzaleztb/FS_FS_ABR22_MERNSTACK)