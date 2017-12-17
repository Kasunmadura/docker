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
    docker irm imagename

#### Docker port EXPOSE

    docker run -d ubuntu/apache:v1 -p 8080:80
    docker run -d ubuntu/nginx:v1 -p 8080:80 8443:443
    docker run -d ubuntu/apache:v1 -P

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


#### Connect volume
This volume default mount point is "/var/lib/docker/$dockerid/_data" and this will not allow to add to Docker file because it should portable

    docker run -it --name voltest1 -v /data centos:latest /bin/bash
    docker run -it --name voltest2 -v /home/kasunr/docker/build/Voltest:/mydata centos:lastest /bin/bash
