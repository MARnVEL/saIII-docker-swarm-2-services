
# Docker Swarm (servers + DB)
>
> This repo was created to address some practical questions about Docker Swarm posed by the class 'Upgrade Seminar III' of the **Formosa Polytechnic Institute** .
> 
> *Este repositorio fue creado para abordar algunas preguntas prácticas sobre Docker Compose planteadas por la clase 'Seminario de Actualización III' del **Instituto Politécnico Formosa***.

## Tecnologías utilizadas

<div align="center" style="display: flex; justify-content: center; align-items: center;">
      <span style="margin-right: 20px;">
         <a href="https://es.javascript.info/" target="_blank">
               <img width="100" title='JavaScript' src='https://upload.wikimedia.org/wikipedia/commons/6/6a/JavaScript-logo.png'>
         </a>
      </span>
      <span style="margin-right: 20px;">
         <a href="https://www.docker.com/" target="_blank" title='Docker'>
               <img width="100" title='Docker' src='https://upload.wikimedia.org/wikipedia/en/thumb/f/f4/Docker_logo.svg/1920px-Docker_logo.svg.png'>
         </a>
      </span>
      </br>
</div>

# Introducción

> Este proyecto se trata de la "orquestación" de distintos contenedores con Docker Swarm.
> *Son simples servidores hechos con **JavaScript** y **NodeJS**, los cuales consultan a un contenedor que aloja una base de datos **MySQL***.
> La aplicación en sí no es importante. Lo único que hace es mostrar una lista alojada en una tabla de la base de datos. Adicionalmente se proporciona un formulario para añadir registros.
> 
> Lo que se intenta demostrar es la administración de distintos servidores ejecutándose en contenedores de *Docker*, y su orquestación con *Docker Swarm*.
> 
> Cada servidor se ejecuta en un contenedor independiente.
> Los contenedores que alojan los servidores se crean a partir de imágenes de **node:18-alpine**. Luego en estos contenedores hacemos las instalaciones correspondientes (a través de los Dockerfile's) para poder ejecutar servidores desarrollados con **NodeJS**.
> El balanceo de los contenedores se realiza a partir de **Docker Swarm**.

# Características

**SO: Windows 10**.

# Requerimientos

* Tener instalado **Dokcer Desktop**
* Tener instalado **Git**

# Usos

> **Aclaración**: En esta guía se supone que los pasos se ejecutan secuencialmente y no se consideran posibles errores que puedan surgir, tales como falta de conexión a internet, errores en el SO, etc. A su vez, en cada paso deben hacerse las respectivas verificaciones de la correcta ejecución y finalización de los procesos involucrados. Si en un paso ocurre un error o este no finaliza aún, no podrá realizarse correctamente el paso siguiente.
> 
> Para utilizar este proyecto,  abrimos un CLI y nos dirigimos al directorio del sistema donde deseamos guardarlo. Ejecutamos el siguiente comando

```bash
git clone https://github.com/MARnVEL/saIII-docker-swarm-2-services.git
```

1. Iniciar la aplicación Docker Desktop

2. En un CLI **inicializamos docker swarm**:

      ```bash
      docker swarm init
      ```

3. En el directorio de nuestro sistema donde guardamos el proyecto, ejecutamos en el CLI

      ```bash
      cd node-in-apache/
      ```

      * Luego:

      ```bash
      docker build -t nia-img .
      ```

      * Con este comando construiremos la imagen que luego será utilizada en nuestro [fichero .yml](./docker-services.yml) (línea 13).

4. Luego hacemos un `cd ..` para volver al directorio raíz de nuestro proyecto y ejecutamos:

      ```bash
      cd nodejs/
      ```

      * Luego:

      ```bash
      docker build -t node-img .
      ```

      * Con este comando construiremos la imagen que luego será utilizada en nuestro [fichero .yml](./docker-services.yml) (línea 22).

5. Luego hacemos un `cd ..` para volver al directorio raíz de nuestro proyecto y ejecutamos:

      ```bash
      cd database/
      ```

      * Luego:

      ```bash
      docker build -t mysql-img .
      ```

      * Con este comando construiremos la imagen que luego será utilizada en nuestro [fichero .yml](./docker-services.yml) (línea 5).

6. Luego hacemos un `cd ..` para volver al directorio raíz de nuestro proyecto y ejecutamos:

      ```bash
      docker stack deploy -c docker-services.yml ns
      ```

      * *Esperamos un momento a que inicien todos nuestros servicios*. En total ***se levantarán 7 servicios***, 3 para los servidores del contenedor *Apache*, 3 para los servidores del contenedor *NodeJs* y uno para el servicio de la base de datos del contenedor *MySQL*.
      * También se creará un directorio `mysql-datos`, dentro de la carpeta del proyecto que guardará todos los datos que se carguen a la base de datos. Este es un volumen creado en el servicio de *mysql*.
      * **¡Listo!**. Ya podríamos probar el correcto funcionamiento de los servicios.
      * **Primero**: abrimos nuestro navegador de preferencia y nos dirigimos a: `http://localhos:8080`. Deberíamos ver una lista de 'alumnos' con su respectivos nombres, apellidos y dni's. También deberíamos tener la posibilidad de cargar alumnos a nuestra BD.
      * **Segundo**: abrimos nuestro navegador de preferencia y nos dirigimos a: `http://localhos:8090`. El resultado debería ser el mismo que en el párrafo anterior.

7. Podemos listar los servicios de **swarm**:

      ```bash
      docker service ls
      ```

8. Para eliminar los servicios ejecutamos:

      ```bash
      docker service rm ns_apache
      ```

      ```bash
      docker service rm ns_nodejs
      ```

      ```bash
      docker service rm ns_mysql
      ```

9. Escalamiento. Para poder aumentar la cantidad de servicios disponibles, abrimos un CLI y ejecutamos:

      ```bash
      docker service scale ns_apache=9
      ```

      ```bash
      docker service scale ns_nodejs=9
      ```

      Con el comando anterior hemos aumentado la cantidad de nuestros servicios de nombre `ns_apache` y `ns_nodejs` a `9`.
      Debemos aclarar que este comando, elimina los contenedores que se están ejecutando y se levantan nuevamente en la cantidad asignada.
