# Cómo crear una imagen de Docker container desde cero.

Inspired by: [How to build a docker container from scratch - Faithful Anere](https://faun.pub/how-to-build-a-docker-container-from-scratch-docker-basics-a-must-know-395cba82897b)


## Step 1 - crear Dockerfile

```{shell}
$ mkdir myapp (create our app folder)

$ cd myapp (lets go into our folder)

$ nano Dockerfile (create a file called Dockerfile)
```

Y copiar

```{shell}
FROM ubuntu:18.04

LABEL MarcusRB <marcusrb@dataschool.tech>

RUN apt-get update && \
    apt-get install -y apache2 && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*
    
    
ENV APACHE_RUN_USER  www-data
ENV APACHE_RUN_GROUP www-data
ENV APACHE_LOG_DIR   /var/log/apache2
ENV APACHE_PID_FILE  /var/run/apache2/apache2.pid
ENV APACHE_RUN_DIR   /var/run/apache2
ENV APACHE_LOCK_DIR  /var/lock/apache2
ENV APACHE_LOG_DIR   /var/log/apache2

RUN mkdir -p $APACHE_RUN_DIR
RUN mkdir -p $APACHE_LOCK_DIR
RUN mkdir -p $APACHE_LOG_DIR

COPY index.html /var/www/html

EXPOSE 80

CMD ["/usr/sbin/apache2", "-D", "FOREGROUND"]
```

Para el resto de opciones para el Dockerfile

NOTE : For a production environment you can still use ubuntu but its better to use Alpine distribution because it is super light weight but for this guide i will stick to using ubuntu 18.04.

    MAINTAINER This command is the command that declares the author of the image. # Usage: MAINTAINER [name]
    COPY This command copies a file from your host OS to the docker container
    RUN builds your application
    CMD is used to run a command as soon as a container is launched. It is different from RUN as RUN is used at the time of building an image.
    EXPOSE this command is used to specify the port number upon which the container is running its process for networking with the outside world (your host). We are exposing port 80 because Apache web server runs on port 80 by default hence we can come from outside our container and run on hook up to the port 80 of our container. Think of it like a plug of like charges. #usage: EXPOSE [port]
    -D FOREGROUND When -D FOREGROUND is not passed apache is run in the background and the apache process will be detached from the shell. This will cause docker container to be stopped.
    ENV This are environment variables for re-usable commands. These variables consist of “key value” pairs which can be accessed within the container by scripts and applications alike. This functionality of Docker offers an enormous amount of flexibility for running programs.
    the . command instructs the build to take read from the current folder in which we are in.

There are others which we havent used but i will explain what they are used for:
USER

The USER directive is used to set the UID (or username) which is to run the container based on the image being built.

Example:

# Usage: USER [UID]
USER 751

VOLUME

The VOLUME command is used to enable access from your container to a directory on the host machine (i.e. mounting it).

Example:

# Usage: VOLUME ["/dir_1", "/dir_2" ..]
VOLUME ["/my_files"]

WORKDIR

The WORKDIR directive is used to set where the command defined with CMD is to be executed.

Example:

# Usage: WORKDIR /path
WORKDIR ~/

ENTRYPOINT

ENTRYPOINT argument sets the concrete default application that is used every time a container is created using the image. For example, if you have installed a specific application inside an image and you will use this image to only run that application, you can state it with ENTRYPOINT and whenever a container is created from that image, your application will be the target.

If you couple ENTRYPOINT with CMD, you can remove “application” from CMD and just leave “arguments” which will be passed to the ENTRYPOINT.

Example:

# Usage: ENTRYPOINT application "argument", "argument", ..
# Remember: arguments are optional. They can be provided by CMD
#           or during the creation of a container.
ENTRYPOINT echo# Usage example with CMD:
# Arguments set with CMD can be overridden during *run*
CMD "Hello docker!"
ENTRYPOINT echo




***
## Step 2 - crear index.html

Creamos el fichero que leerá la webservice

```{html}
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>My First Docker APP</title>
</head>
<body>
    <h1>Hello Datas, this is my first Docker Container, cheers!</h1>
</body>
</html>

```

***

## Step 3 - build docker image

Construimos 'build' la imagen de docker

```{shell}
# Para construir la imagen utilizamos el comando
# Ej.: docker build -t [name] .

$ docker build -t hellodata . 

# el punto final "." indica a cual de los directorios le decimos a docker de crearla
```


## Step 4 - docker run

Al ser un servicio de webservice realizamos el comando docker run instanciando a un puerto

```{shell}
$ docker run -d -p 80:80 hellodata

# -d habilita el comando en background
```

Ya podemos ver el resultado en nuestro browser:

`localhost:80`

## Step 5 - docker push

Para enviar nuestra imagen al Docker Hub, previo registro y login utilizamos `docker push`


```{shell}
$ docker tag first-image {docker-hub-username}/{default-repo-folder-name}:first-image

$ docker push {docker-hub-username}/{default-repo-folder-name}:first-image
```

## Step 6 - docker pull
Para traer a nuestro entorno una imagen de docker hub utilizamos `docker pull`

```{shell}
$ docker pull <nombre docker-hub>/<nombre-imagen>:versión
```

## Contributing
Esta guía es válida solo para entornos OSx y Linux



