## Docker Command and Features

This repo will help you to get undestanding on docker and it featuse

#### Interactive mode with tty

    docker run -it ubuntu:latest /bin/bash

#### Daemon mode

    docker run -d ubuntu:latest /bin/bash

#### Docker log/inspec

    docker logs dockername
    docker inspect dockername

#### Docker stop/delete

    docker stop containername
    docker rm containername/id
    docker rmi imagename
    docker rmi -f imagename

#### Docker port EXPOSE

    docker run -d -p 8080:80 ubuntu/apache:v1
    docker run -d -p 8080:80 8443:443 ubuntu/nginx:v1
    docker run -d -p 127.0.0.1:8089:80  nginx:latest
    docker run -d -P ubuntu/apache:v1

#### SSH to docker instance

    docker attach dockername
    docker exec -it apacheweb6 bash

#### Docker build image with docker file (-t tag )
    docker build -t ubuntu/apache2:v1 .

#### Docker network
    docker network ls --no-trunc
    docker network inspect bridge
    docker network create --subnet 10.0.1.0/24 --gateway 10.0.1.1 kasunbridge01
    docker network rm mybridge01


#### Docker network attache to docker instance
    docker network create --subnet 10.101.0.0/16 --gateway 10.101.0.1 --ip-range=10.101.4.0/24 --driver=bridge --label=kasun4network kasunbridge04
    docker run  -it --name nettest01 --network kasunbridge04 centos:latest /bin/bash

#### Setup specific IP for container (Not working for default bridge)

    docker run  -id --name nettest07 --network kasunbridge04 --ip 10.101.4.100 centos:latest

### Docker Inspect

    docker exec testdocker /bin/ps -ef | grep bash
    docker top testdocker
    docker exec -it apacheweb5  /bin/bash
    docker stats apacheweb5

### Removing previous containers

    docker rm container_name_or_id
    docker ps -a -q
    docker ps -qa | wc -l
    docker rm 'docker ps -aq'
    for i in $(docker ps -qa);do docker rm "$i";done
    cd /var/lib/docker/containers | rm dockerfile

### Docker container rename

    docker rename dbfdssdsaad kasunmaduratest

### Docker event

    docker events --since '1h'
    docker events
    docker kill dockername
    docker events  -f event=attach

### Docker Save and loading

   docker commit dockername imagename
   docker save --output centos.latest.tar centos:mine
   gzip centos.latest.tar
   docker load --input centos.latest.tar.gz

### Docker image history (only avaliable in image)

   docker history imagename  
   docker history --no-trunc centos:mine
   docker history --quiet centos:mine

### Docker images tag

    docker image tag 323132391a kasunapache:v.1

### Docker Custom Network

    ip link add br10 type bridge
    ip addr add 10.10.100.1/24 dev br10
    ip link set br10 up
    
    docker -b br10 &

    cat /etc/network/interface
    auto br10
    iface br10 inet static
        address 10.10.100.1
        netmask 255.255.255.0
        bridge_port dummy0
        bridge_stp off
        bridge_fp 0

### Connect volume

This volume default mount point is "/var/lib/docker/$dockerid/_data" and this will not allow to add to Docker file because it should portable

    docker run -it --name voltest1 -v /data centos:latest /bin/bash
    docker run -it --name voltest2 -v /home/kasunr/docker/build/Voltest:/mydata centos:lastest /bin/bash
