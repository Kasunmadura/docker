## Docker Command and Features

This repo will help you to get undestanding on docker and it featuse


#### Interactive mode with tty

    docker run -it ubuntu:latest /bin/bash
    docker run -it --rm ubuntu:latest /bin/bash

#### ENV variable with deattach

    docker run -d --env MYVAR=test --name test  centos:6

#### Deattched mode

    docker run -d ubuntu:latest

#### Docker pull

    docker pull hello-world

download the all images related to hello-world

    docker pull -a hello-world
    docker pull -disable-contant-trust hello-world

#### Docker images

    docker images

Show long name with docker images

    docker images --digests
    docker images --no-trunc

    docker images  --filter "before=centos:6"

Show only image ids

    docker images -q

Clean up images

    docker rm `docker ps -a -q`
    for x in $(docker images -q) ; do docker rmi $x ; done


Filter images     

    docker search --filter stars=50 --filter is-official=true apache
    docker search --limits 10 apache

Save image Save and import

    docker image save --output centos.test.tar centos:kasunapache
    docker load --input centos.test.tar

Or with diffent name

    docker import centos.test.tar localimport:centos6

Remove dangling images

    docker image prune
    docker image prune -a

Get image details as output

    docker image inspect centos:6 --format '{{.ContainerConfig.Hostname}}'
    docker image inspect centos:6 --format '{{.ContainerConfig}}'
    docker image inspect centos:6 --format '{{json .ContainerConfig}}'

#### Docker image history (only avaliable in image)

    docker history imagename  
    docker history --no-trunc centos:mine
    docker history --quiet centos:mine

### Image layer with union file system (save some space)

docker image with singel layer (experiment features should enable)

    docker build --pull --no-cache --squash -t optimized:v1 .

export container and consolidate

    docker export kasuntest > mybuild.tar
    docker import mybuild.tar mybuild:importv5

    docker image history mybuild:importv5



#### Docker images tag and customize the image

    docker image tag centos:latest kasunapache:v.1
    docker tag centos:6 mycentos:v1


#### Docker Save and loading

    docker commit dockername imagename
    docker save --output centos.latest.tar centos:mine
    gzip centos.latest.tar
    docker load --input centos.latest.tar.gz

#### Docker container full running info

    docker ps --no-trunc


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

#### SSH to docker instance (docker attach exit will stop the container)

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

#### Docker Inspect

    docker exec testdocker /bin/ps -ef | grep bash
    docker top testdocker
    docker exec -it apacheweb5  /bin/bash
    docker stats apacheweb5

#### Removing previous containers

    docker rm container_name_or_id
    docker ps -a -q
    docker ps -qa | wc -l
    docker rm 'docker ps -aq'
    for i in $(docker ps -qa);do docker rm "$i";done
    cd /var/lib/docker/containers | rm dockerfile

#### Docker container rename

    docker rename dbfdssdsaad kasunmaduratest

#### Docker event

    docker events --since '1h'
    docker events
    docker kill dockername
    docker events  -f event=attach


### Setup docker registory

setup self sign or proper ssl cert (this is for self sign)

    mkdir certs auth
    apt-get install open-ssl
    openssl  req -newkey rsa:4096 -nodes -sha256  -keyout  certs/dockerrepo.key -x509 -days 365 -out certs/dockerrepo.crt  -subj /CN=kasundomain.com

setup localhost entry or DNS entry for your domain (default port 5000)

    sudo mkdir -p  /etc/docker/certs.d/kasundomain.com:5000
    cp /home/docker/certs/dockerrepo.crt /etc/docker/certs.d/kasundomain.com:\5000/ca.crt
    docker pull registry:2
    docker run --entrypoint htpasswd  registry:2 -Bbn test password > auth/htpasswd

minimum requiment

    docker run  -d -p 5000:5000  -v /home/docker/certs:/certs -e REGISTRY_HTTP_TLS_CERTIFICATE=/certs/dockerrepo.crt -e REGISTRY_HTTP_TLS_KEY=/certs/dockerrepo.key

with htpasswd

    docker run  -d -p 5000:5000  -v /home/docker/certs:/certs -e REGISTRY_HTTP_TLS_CERTIFICATE=/certs/dockerrepo.crt -e REGISTRY_HTTP_TLS_KEY=/certs/dockerrepo.key -v /home/docker/auth:/auth -e REGISTRY_AUTH=htpasswd -e REGISTRY_AUTH_HTPASSWD_REALM="Registry Realm" -e REGISTRY_AUTH_HTPASSWD_PATH=/auth/htpasswd registry:2

    docker login kasundomain.com:5000
    docker push  kasundomain.com:5000/busybox

remote PC pull the custom docker image

    docker login kasundomain.com:5000
    docker pull kasundomain.com:5000/busybox

Note: we have to copy /etc/docker/certs.d/kasundomain:5000/ca.crt to all docker hosts to connect to this registry


#### Docker Custom Network

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

#### Connect volume

    This volume default mount point is "/var/lib/docker/$dockerid/_data" and this will not allow to add to Docker file because it should portable

    docker run -it --name voltest1 -v /data centos:latest /bin/bash
    docker run -it --name voltest2 -v /home/kasunr/docker/build/Voltest:/mydata centos:lastest /bin/bash
