# Image Docker Distribution


## USE CASE 1 - Hello World
So rich man wants deploy a server with following characteristics:

Amazon EC2 - t2.2xlarge (expert reviewed)
  - vCPU: 8
  - MEM: 32GB

The application that he wants it is a variation of cowsay, called dockersay but he needs a change in this app name to trumpsay.

### Step 1 - Run my first container

```
docker run --rm docker/whalesay cowsay hello world
```

### Step 2 - My first container with volumes

```
docker run --rm \
  -v `pwd`/trump.cow:/cowsay/cows/trump.cow \
  docker/whalesay cowsay -f /cowsay/cows/trump.cow "BOOM"
```

### Step 3 - Create an alias

```
alias trumpsay="docker run --rm \
  -v `pwd`/trump.cow:/cowsay/cows/trump.cow \
  docker/whalesay cowsay -f /cowsay/cows/trump.cow"
```

```
trumpsay BURN FU BURN, BOOM
```

### Step 4 - Container debug

```
docker run -it --rm docker/whalesay bash
```

```
(pwd && ls -lh)
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

```
docker save todos:1.0.0 > todos.tar
docker save --output todos_2.tar todos:1.0.0
docker save todos:1.0.0 | bzip2 > todos_3.tar
```

### Step 2 - Verify image files

```
ls -sh todos.tar
```

### Step 3 - Copy file

```
cp todos.tar /path/to/usb/todos.tar
```

### Step 4 - Remove image from host

```
docker rmi todos:1.0.0
```

### Step 5 - Load an image from a tar archive or STDIN.

```
docker load < todos.tar
docker load --input todos.tar
```

### Step - Create container

```
docker run -d -p 3000:3000 --restart=always \
  --link mongo \
  -e MONGO_URL=mongodb://mongo:27017/todos \
  --name=app \
  todos:1.0.0
```

## USE CASE 3.0 - Share image using registry

So sad man wants to deploy ToDos App in Russia, the server has the following specifications:

Amazon EC2 - t1.micro
  - vCPU: 1
  - MEM: 0.6GB

They have an good Internet connection, also they want periodical updates.


### Step 1 - Deploy my first registry

```
docker run -d -p 5000:5000 --name registry registry:2
```

### Step 2 - Tag a docker image

```
docker tag alpine localhost:5000/myfirstimage
```

### Step 3 - Push my first image into local registry

```
docker push localhost:5000/myfirstimage
```

### Step 4 - Check images

```
docker run -it --rm --net=host centos bash

curl http://localhost:5000/v2/_catalog
```

### Step 5 - Stop & Remove registry

```
docker stop registry && docker rm -v registry
```

## USE CASE 3.1 - Share image using registry

### Step 1 - Deploy a registry

```
docker run -d -p 5000:5000 --restart=always \
      --name registry \
      -v `pwd`/data:/var/lib/registry \
      registry:2
```

### Step 2 - Tag my image

```
docker tag todos:1.0.0 localhost:5000/todos:1.0.0
```

### Step 3 - Push todos:1.0.0 into registry

```
docker push localhost:5000/todos:1.0.0
```

### Step 4 - Remove image from my local cache

```
docker rmi todos:1.0.0
```

### Step 5 - Pull todos:1.0.0 from local registry

```
docker pull localhost:5000/todos:1.0.0
```

### Step 6 - Stop & Remove registry

```
docker stop registry && docker rm -v registry
```

