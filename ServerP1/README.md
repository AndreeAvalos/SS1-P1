# Server Node js
Proyecto realizado con docker para el curso seminario de Sistemas, archivos pertenecientes para la creacion 
de un container con node js.


### Pre-requisitos 📋
Tener instalado Docker.
Si no podemos instalarlo de esta forma.
Actualizar los paquetes.
```
sudo apt-get update
```
Ahora vamos a instalar Docker. Agregue la clave GPG para el repositorio oficial de Docker al sistema:
```
sudo apt-key adv --keyserver hkp://p80.pool.sks-keyservers.net:80 --recv-keys 58118E89F3A912897C070ADBF76221572C52609D
```
Agregue el repositorio Docker a fuentes APT:
```
sudo apt-add-repository 'deb https://apt.dockerproject.org/repo ubuntu-xenial main'
```
Actualizar los paquetes.
```
sudo apt-get update
```
Asegúrese de que está a punto de instalar desde el repositorio de Docker en lugar del repositorio predeterminado.
```
apt-cache policy docker-engine
```
Por ultimo, instalar Docker:
```
sudo apt-get install -y docker-engine
```

## Comenzando 🚀

1. Para poder comenzar debemos construir un contenedor para el area de  trabajo.
2. Creamos el ambiente de desarrollo para docker con node js
Este comando baja del repositorio de docker las herramientas necesarias para ejecutar node js
```
docker pull node js
```
3. Para un mejor manejo de archivos entre el contenedor y la computadora local, podemos hacer una carpeta compartida.
Example.
```
docker run -it -v /home/andree/ServerP1:/server --name server-node -p 3001:3001 node bash
```
* **-it** Agrega un script de consola al iniciar el contenedor
* **-v** Crea una carpeta compartida
* **-name** Asigna una nombre al contenedor
* **-p** Mapea el puerto

4. A la hora de ejecutar el comando anterior, nos ingresa al contenedor
Nos dirigimos hacia nuestro proyecto e iniciamos la configuracion
```
cd server
npm init
```
5. Nos mostrara una configuracion por defecto de package.json
```
{
  "name": "app",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "Andree",
  "license": "ISC"
}
```
6. Creamos un archivo en la carpeta compartida el cual llamaremos index.js
```
nano index.js
```
7. Instalamos express, request, body-parser, request-promise y mysql
```
npm i express
npm install mysql
npm install request
npm install body-parser
npm install request-promise
```
8. Modificamos index.js y copiamos lo siguiente
```
const express = require('express')
const app = express()
const api_helper = require('./API_helper')
var request = require('request');
var body_parser = require('body-parser')
const HOST = process.env.HOST || "172.17.0.4"
const port =  process.env.PORT || 3000
const ip = process.env.IP || "172.17.0.3"
const p = 3001
var rp = require('request-promise');

app.use(body_parser.urlencoded({extended:true}));

bodyParser = require('body-parser').json();
var formulario = '<form method="post" action= "/insertar">'
    + '<label for="dpi">DPI</label>'
    + '<input type="text" name="dpi" id="dpi">' 
    + '<label for="carnet">Carnet</label>'
    + '<input type="text" name="carnet" id="carnet">' 
    + '<label for="nombre">Nombre</label>'
    + '<input type="text" name="nombre" id="nombre">' 
    + '<label for="apellido">Apellido</label>'
    + '<input type="text" name="apellido" id="apellido">'    
    + '<label for="email">Correo</label>'
    + '<input type="text" name="email" id="email">'  
    + '<label for="telefono">Telefono</label>'
    + '<input type="text" name="telefono" id="telefono">'  
    + '<input type="submit" value="Enviar"/>'
    + '</form>';

var cabecera = '<h1>Insertar Alumno</h1>';

app.get('/insertar', function (req, res) {
    res.send('<html><body>'
            + cabecera
            + formulario
            + '</html></body>'
    );
});

app.post('/insertar', function (req, res) {
 
    var dpi = req.body.dpi || '';
    var carnet = req.body.carnet || '';
    var nombre = req.body.nombre || '';
    var apellido = req.body.apellido || '';
    var email = req.body.email || '';
    var telefono = req.body.telefono || ''; 

    var requestData ={
    	"carnet":carnet,
    	"dpi":dpi,
    	"nombre":nombre,
    	"apellido":apellido,
    	"email":email,
    	"telefono":telefono
    };
    var ruta = 'http://'+ip+':'+port+'/insertAlumno';

    request.post(ruta, {
	  json: requestData
	}, (error, res, body) => {
	  if (error) {
	    console.log('Error')
	    return
	  }
	  console.log(`statusCode: ${res.statusCode}`);
	  console.log(body);
	})

});

app.get('/', function (req, res) {
      	res.send('<p><a href="/insertar">Insertar un Alumno</a></p>'
      			+'<a href="/viewAlumno">Ver Alumnos</a>'
    );
})

app.get('/viewAlumno', function (req, res) {

	var ruta = 'http://'+ip+':'+port+'/viewAlumno';
	api_helper.make_API_call(ruta)
    .then(response => {
    	//console.log(response);
        res.json(response);
    })
    .catch(error => {
        res.send(error)
    })


})


app.listen(p, HOST)

console.log(`Running on http://${HOST}:${p}`);
```
9. Creamos la clase de apoyo API_helper
```
nano APiI_helper.js
```
Y copiamos lo siguiente:
```
const request = require('request')

module.exports = {
    /*
    ** This method returns a promise
    ** which gets resolved or rejected based
    ** on the result from the API
    */
    make_API_call : function(url){
        return new Promise((resolve, reject) => {
            request(url, { json: true }, (err, res, body) => {
              if (err) reject(err)
              resolve(body)
            });
        })
    }
}
```
Esta clase es muy basica, para una consumir un api get desde una url
Example:
```
http://172.17.0.3:3000/viewAlumno
```

### Creación de una imagen a partir de un Dockerfile 🔧
1. Creamos el archivo Dockerfile en la misma ruta donde tenemos index.js
```
nano Dockerfile
```
Con la siguiente informacion
```
FROM node
WORKDIR /server
ADD . /server
RUN npm install
ENV PORT 3001
ENV IP "172.17.0.4"
CMD ["node","index.js"]
```
2. Creamos nuestra imagen 
```
docker build -t server-node
```
Con esto teminamos la instalacion y configuracion de node y nuestro proyecto en un contenedor de docker

## Ejecutando las pruebas ⚙️
1. Ingresamos al contenedor 
```
docker exec -it server-node bash
```
2. Nos dirigimos a l ruta donde se encutra el proyecto
```
cd server
```
3. Ejecutamos nuestro index.js
```
node index.js
```
Podemos agregarle variables, ya que maneja variables de entorno
Example: 
```
HOST=192.168.0.1 PORT=3050 IP=172.172.0.5 node index.js -e
```

## Deployment 📦
Para poder subir la imagen a Docker hub
1. Agregamos un tag
```
docker tag nombreimagen:tag user/server:tagname
```
2. Damos push
```
docker push user/server:tagname
```
Example:
```
docker tag bd_p1:latest andreeavalos/server:01
docker push andreeavalos/server:01
```
3. Para poder bajar el repositorio 
```
docker pull andreeavalos/server
```

## Construido con 🛠️
[Docker](https://docs.docker.com/install/linux/docker-ce/ubuntu/) - Ambiente de trabajo
[Node JS](https://nodejs.org/es/docs/)-Framework


## Autores ✒️

* *Carlos Andree Avalos Soto*-*Trabajo Inicial*-[andreeavalos](https://github.com/andreeavalos)