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

