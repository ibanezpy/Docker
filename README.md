# Docker

## Listar contenedores activos
docker ps

## Listar todos los contenedores
docker ps -a 

## Inspeccionar un contenedor
docker inspect d4b783c1e2ba

## Ejecutar contenedor y especifica el nombre del contenedor
docker run --name ibanez hello-world

## Renombrar contenedores
docker rename ibanez hola-ibanez

## Eliminar contenedores
docker rm funny_wing hola-ibanez vigilant_montalcini

## Remover todos los contenedores
docker container prune


## Instalamos ubuntu en docker
docker run ubuntu

## Correr ubuntu y que no pare pasando el proceso tail -f /dev/null
docker run --name alwaysup -d ubuntu tail -f /dev/null

## Ingresamos a nuestro ubunto en modo terminal
docker exec -it alwaysup bash

## Obtener el proceso para matarlo con kill
docker inspect --format '{{.State.Pid}}' alwaysup 
docker stop alwaysup

## Instalando NGINX
docker run -d --name proxy nginx

## Ejecutar proxy con puerto expuesto
##  -p anfitrion:docker
docker run -d --name proxy -p 80:80 nginx

docker logs -f proxy
docker logs --tail 1 proxy


## Jugando con mongo container
docker run -d --name db mongo
docker exec -it db bash

root@10d69ea90d45:/##  mongo
> use platzi
> db.users.insert({"nombre":"ibanez"})
> db.users.find()

docker run -d --name db -v /home/ponky/Desktop/Docker/mongo_data:/data/db mongo

VOLUMENES
## Creando volumenes
docker volume create dbdata
docker volume ls
docker run -d --name db --mount src=dbdata,dst=/data/db mongo

## ## 15 - Insertar y extraer archivos de un contenedor
docker run -d --name copytest ubuntu tail -f /dev/null
docker exec -ti copytest bash
## Mandar archivo al contendedor
docker cp hola copytest:/test/hola.txt
## Extraer archivo al contendedor
docker cp copytest:/test/hola.txt localtesting.txt

## ##  16 - Conceptos fundamentales de Docker: imágenes
## Listar las imagenes de docker
docker image ls
## Bajar imagen sin instalar
docker pull ubuntu:20.04

## ## 17 - Construyendo una imagen propia
docker build -t ubuntu:ibanez .
## Ingresar al docker y pushear
docker login
## Renombar la imagen y que haga referencia a nuestro repositorio de dockerhub
docker tag ubuntu:ibanez ibanezpy/ubuntu:ibanez
docker push ibanezpy/ubuntu:ibanez

## ## 18 - El sistema de capas
## Analizar el historial de la imagen, que se instalo/corrio luego del from
docker history ubuntu:ibanez
## otra opcion es utilizar dive
## instalar en Ubuntu
wget https://github.com/wagoodman/dive/releases/download/v0.9.2/dive_0.9.2_linux_amd64.deb
sudo apt install ./dive_0.9.2_linux_amd64.deb
dive ubuntu:ibanez
dive mongo

## ## 19 - Usando Docker para desarrollar aplicaciones
mkdir  platzi
cd platzi/
git clone https://github.com/platzi/docker
docker build -t platziapp . (creo la imagen local)
docker image ls (listo las imagenes locales)
docker run --rm -p 3000:3000 platziapp (creo el contenedor y cuando se detenga se borra, lo publica el puerto 3000)
docker ps (veo los contenedores activos)

## ## 20 - Aprovechando el caché de capas para estructurar correctamente tus imágenes
## Lo que importa es el orden en que definis las sentencias en el docker file. 
docker run --rm -p 3000:3000 -v /home/ponky/Desktop/Docker/platzi/docker/index.js:/usr/src/index.js platziapp

## ## 21 - Docker networking: colaboración entre contenedores
## Creamos una nueva red para que los nuevos contenedores se puedan conectar
docker network create --attachable platzinet
## Inspecionar la nueva red creada
docker network inspect platzinet
## Creando contenedor y atacheando a la nueva red
docker run -d --name db mongo
docker network connect platzinet db 
docker network inspect platzinet 
docker run -d --name app -p 3000:3000 --env MONGO_URL=mongodb://db:27017/test platziapp

## ## 22 - Docker Compose: la herramienta todo en uno
docker-compose up -d

## ## 23 - Subcomandos de Docker Compose
docker-compose up -d
docker-compose ps
docker-compose down
docker network ls
docker network inspect docker_default 
docker-compose logs -f app


## ## 25 - Compose en equipo: override
docker-compose up -d --scale app=2

## ## 26 - Administrando tu ambiente de Docker
## Borra todo los conteniner, redes imagesy buil que no esten corriendo.
docker system prune
## borrado forzado de los containers
docker rm -f $(docker ps -aq)
## Limitar recursos con 1 GB de memoria
docker run -d --name app --memory 1g platziapp
docker stats
## Inspeccionar el contenedor
docker inspect app

## ## 27 - Deteniendo contenedores correctamente: SHELL vs. EXEC
docker build -t loop . (construyo la imagen)
docker run -d --name looper loop (corro el contenedor)
docker stop looper (le envía la señal SIGTERM al contenedor)
docker ps -l (muestra el ps del último proceso)
docker kill looper (le envía la señal SIGKILL al contenedor)
docker exec looper ps -ef (veo los procesos del contenedor)

## ## 28 - Contenedores ejecutables: ENTRYPOINT vs CMD
docker buil -t ping . (construyo la imagen)
docker run --name pinger ping <hostname> (ahora le puedo pasar un parámetro, previamente tengo que agregar el ENTRYPOINT en el Dockerfile)


## ## 30 - Multi-stage build
docker build -t prodapp -f build/production.Dockerfile .

## ## 31 - Docker-in-Docker
## solo para jugar, mucho cuidado con esto
docker run -it --rm -v /var/run/docker.sock:/var/run/docker.sock docker:19.03.12
docker run --rm -it -v /var/run/docker.sock:/var/run/docker.sock -v $(wich docker):/bin/docker wagoodman/dive:latest prodapp


problemas del software prof
construir
distribuir
Ejecutar

problemas de virtualizacion
permite atacar los problemas de software profesional


sehll vs exec
siempre escribi en formato exec []}y no shell

¿Cuál es el efecto de usar el comando docker-compose build?
construya una imagen a partir de los archivos que tiene en disco