# _BASE DE DATOS_

# sql-scripts 

_SS1-P1/BDP1/sql-scripts/_

Contiene los archivos que son utilizados para crear una base de datos al crear una base de datos

- DDL

Ejemplo de como creamos una tabla alumno en la base de datos

```
CREATE TABLE Alumno (
carnet int,
dpi bigint,
nombre varchar(25),
apellido  varchar(25),
email  varchar(50),
telefono varchar(8)
);
```

- DML
Ejemplo de como insertamos un dato a la base de datos
```
insert into Alumno(carnet,dpi,nombre,apellido,email,telefono) values(201408580,2977840130108,"Andree","Avalos","aavalosoto@gmail.com","35385252");

```


# DockeFile
Contiene como se debe crear una base de datos y lo necesario para poder ejecutar un contenedor con una base datos

```
# Derived from official mysql image (our base image)
FROM mysql:8

# Add a database
ENV MYSQL_DATABASE bd_p1

# Add the content of the sql-scripts/ directory to your image
# All scripts in docker-entrypoint-initdb.d/ are automatically
# executed during container startup
COPY ./sql-scripts/ /docker-entrypoint-initdb.d/
```

### Contruir container
```
docker build -t [nombre de la base de datos] [dockerfile].
```
**entrar al container **
```
docker exec -it [nombre de la base de datos] bash
```
# Pasos a seguir
1. Crear una carpeta para el proyecto 
    ```
    cd ~
    mkdir miProyectoDocker
    ```
2. Entrar a la carpeta
```
    cd miProyectoDocker
```
3. Crear un archivo dockerfile y agregar las instrucciones para construir
```
    nano dockerfile
```
4. Copiar los archivos para construir la base de datos
5. Crear la imagen de docker con docker build

