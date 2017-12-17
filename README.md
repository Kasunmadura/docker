## Docker Command and Features

This repo will help you to get undestanding on docker and it featuse

### interractive mode with tty

    docker run -it ubuntu:latest /bin/bash




### daemon mode

    docker run -d ubuntu:latest /bin/bash

### docker log/inspec

    docker logs dockername
    docker inspect dockername

### docker stop/delete

    docker stop containername
    docker rm containername/id
    docker irm imagename

### docker port EXPOSE

    docker run -d ubuntu/apache:v1 -p 8080:80
    docker run -d ubuntu/apache:v1 -P

### ssh to docker instance

    docker attach dockername
    docker exec -it apacheweb6 bash

### docker build image with docker file (-t tag )
    docker build -t ubuntu/apache2:v1 .

### docker network
    docker network ls --no-trunc
    docker network inspect bridge
    docker network create --subnet 10.0.1.0/24 --gateway 10.0.1.1 kasunbridge01
    docker network rm mybridge01


### docker network attache to docker instance
    docker network create --subnet 10.101.0.0/16 --gateway 10.101.0.1 --ip-range=10.101.4.0/24 --driver=bridge --label=kasun4network kasunbridge04
    docker run  -it --name nettest01 --network kasunbridge04 centos:latest /bin/bash

### setup specific IP for container (not working for default brigde)

    docker run  -id --name nettest07 --network kasunbridge04 --ip 10.101.4.100 centos:latest

### connect volume
This volume default mount point "/var/lib/docker/$dockerid/_data"
And this not allow to add to Docker file because it should portable

    docker run -it --name voltest1 -v /data centos:latest /bin/bash
    docker run -it --name voltest2 -v /home/kasunr/docker/build/Voltest:/mydata centos:lastest /bin/bash
