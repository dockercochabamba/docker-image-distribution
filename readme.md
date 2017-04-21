# Image Docker Distribution


## USE CASE 1 - Hello World
So rich man wants deploy a server with following characteristics:
Amazon EC2 - t2.2xlarge (expert reviewed)
  - vCPU: 8
  - MEM: 32GB

The application that he wants it is a variation of cowsay, called dockersay but he needs a change in this app name to trumpsay.

### Step 1 - Ejecutando mi primer contenedor

```
docker run --rm docker/whalesay cowsay hello world
```

### Step 2 - Ejecutando un contenedor con volumenes

```
docker run --rm \
  -v `pwd`/trump.cow:/cowsay/cows/trump.cow \
  docker/whalesay cowsay -f /cowsay/cows/trump.cow "BOOM"
```

### Step 3 - Registrando como comando

```
alias trumpsay="docker run --rm \
  -v `pwd`/trump.cow:/cowsay/cows/trump.cow \
  docker/whalesay cowsay -f /cowsay/cows/trump.cow"
```

```
trumpsay BURN FU BURN, BOOM
```

### Step 4 - Debug de un contenedor

```
docker run -it --rm docker/whalesay bash
```

```
ls
pwd
(cd cows && ls)
```

```
cowsay -f turkey.cow aaa
```

## USE CASE 2.0 - Share image as file
So happy man wants to deploy ToDos App in North Korea, the server has the following specifications:
Unknown but same to “EC2 - t1.micro”
  - vCPU: 1
  - MEM: 0.6GB
They have not a Internet connection, but there is a USB port in the server.

### Usage
```
$ docker save [OPTIONS] IMAGE [IMAGE...]
```
### Step 1 - Create a backup that can then be used with docker load.
 - Way 1 
```
$ docker save todos > todos.tar

$ ls -sh todos.tar

2.7M todos.tar
```
- Way 2
```
$ docker save --output todos.tar todos

$ ls -sh todos.tar

2.7M todos.tar

```
- Way 3
```
$ docker save -o todos.tar todos

$ ls -sh todos.tar

2.7M todos.tar

```
- Way 4 (More compression)
```
$ docker save alpine:latest | bzip2 > compresed.tar

$ ls -sh todos.tar

1.7M todos.tar
```
### Step 2 - Load an image from a tar archive or STDIN.
- Way 1
```
$ docker load < todos.tar
```
- Way 2
```
$ docker load --input todos.tar
```

## USE CASE 3.0 - Share image using registry

### Step 1 - Deploy un docker registry

```
docker run -d -p 5000:5000 --name registry registry:2
```

### Step 2 - Tag mi image para subirla al registro

```
docker tag alpine localhost:5000/myfirstimage
```

### Step 3 - Push mi primera imagen

```
docker push localhost:5000/myfirstimage
```

### Step 4 - Verificar lista de imagenes

```
docker run -it --rm --net=host centos bash

curl http://localhost:5000/v2/_catalog
```

### Step 5 - Detener el servicio y eliminar el contenedor

```
docker stop registry && docker rm -v registry
```

## USE CASE 3.1 - Share image using registry

So sad man wants to deploy ToDos App in Russia, the server has the following specifications:
Amazon EC2 - t1.micro
  - vCPU: 1
  - MEM: 0.6GB
They have an good Internet connection, also they want periodical updates.


### Step 1 - Deploy un docker registry

```
docker run -d -p 5000:5000 --restart=always \
      --name registry \
      -v `pwd`/data:/var/lib/registry \
      registry:2
```

### Step 2 - Tag mi image para subirla al registro

```
docker tag todos:1.0.0 localhost:5000/todos:1.0.0
```

### Step 3 - Push todos:1.0.0 al registro

```
docker push localhost:5000/todos:1.0.0
```

### Step 4 - Eliminar la imagen local

```
docker rmi todos:1.0.0
```

### Step 5 - Pull todos:1.0.0 del registro

```
docker pull localhost:5000/todos:1.0.0
```

### Step 6 - Detener el servicio y eliminar el contenedor

```
docker stop registry && docker rm -v registry
```

