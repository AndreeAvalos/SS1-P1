# ESTRUCTURA ApiP1

---

### index.js 

---

### package.json

---

## CREACION DE LA API
---
1. Crear un contenedor
2. En el contenedor crear un environment para poder usar Nodejs

        docker pull node

3. Utilizar el comando -v

* -v crear un carpeta compartida
* -p mapear puerto
* -it agregar un script de consola al iniciar el contenedor

5. Para trabajar en un IDE se utiliza el comando

        docker run -it -v ~/Documents/APIREST:/App --name api-node -p 3000:3000 node bash

6. Ip de un container

        docker inspect --format '{{ .NetworkSettings.IPAddress }}' ${CID}

7. Iniciar el proyecto para la API

        cd App
        npm init

8. Script del JSON para la configuracion inicial

        {
          "name": "app",
          "version": "1.0.0",
          "description": "",
          "main": "index.js",
          "scripts": {
            "test": "echo \"Error: no test specified\" && exit 1",
            "start": "node index.js"
          },
          "author": "Andree",
          "license": "ISC"
        }

9. Crear archivo el cual sera nuestra pagina de inicio en este caso **_index.js_**

10. Agregar al archivo index.js el siguiente codigo

        'use strict';
        const express = require('express');
        // App
        const app = express();
        var ip = process.env.IP || 'localhost';
        var h = process.env.HOST ||'0.0.0.0';
        // Constants
        const PORT = 3000;
        const HOST = h;

        var body_parser = require('body-parser').json();

        const mysql = require('mysql');
        // connection configurations
        const mc = mysql.createConnection({
            host: ip,
            user: 'root',
            password: '123456789',
            database: 'bd_p1'
        });
        mc.connect();



        app.get('/viewAlumno', (req, res) => {
          mc.query("Select * from Alumno",function (err, result, fields) {
            if (err) {throw err;}
            else{
              res.send(result);	
          }
          });
        });


        app.post('/insertAlumno',body_parser, function (req, res) {

        var dpi = req.body.dpi || '';
          var carnet = req.body.carnet || '';
          var nombre = req.body.nombre || '';
          var apellido = req.body.apellido || '';
          var email = req.body.email || '';
          var telefono = req.body.telefono || ''; 

        var query = 'insert into Alumno(carnet,dpi,nombre,apellido,email,telefono) values('+carnet+','+dpi+',"'+nombre+'","'+apellido+'","'+email+'","'+telefono+'");'
            mc.query(query, function (err, result) {
          if (err){res.send("FAIL!!!!");throw err;}
          else{
            res.send("SUCCESS");}
        });
        })


        app.listen(PORT,HOST);
        console.log(`Running on http://${HOST}:${PORT}`);

11. Ejecutamos el archivo.

         npm start

12. Se instala express

        npm i express 
--- 

# crear la imagen del docker

1. Se crea un archivo dockerfile con lo siguiente

        FROM node
        WORKDIR /app
        ADD . /app
        RUN npm install
        ENV PORT 3000
        ENV IP "192.168.0.0"
        CMD ["node","index.js"]

2. Creamos la imagen

        docker build -t mi-primera-api .

3. Se ejecuta

        docker run -d -p 3000:3001 --name mi-api --rm -e PORT=3001 -e IP="localapi" ApiP1

---

# INFORMACION API
        
### 1. viewAlumno
>Mostrar inforamcion de alumno

### 2. insertAlumno
>Inserta un alumno a la base de datos
