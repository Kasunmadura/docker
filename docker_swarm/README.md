
## Docker Engine Installation

* https://docs.docker.com/engine/installation/linux/docker-ce/centos/#upgrade-docker-ce-1


### CentOS/Red Hat process

* Make sure no other/older versions of Docker are installed on the system
if so, /var/lib/docker images, containers, volumes and networks will be preserved

        sudo yum install -y yum-utils device-mapper-persistent-data lvm2
        sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo

    Optionally, use sudo yum-config-manager --enable docker-ce-edge or docker-ce-test isable with --disable

        sudo yum install docker-ce
        sudo systemctl enable docker && systemctl start docker && systemctl status docker

* NOTE: installing/upgrading covered separately from packages
    Add a user for non-root use of Docker

         sudo usermod -aG user docker
* Restart Docker, check /var/run/docker.sock
     Will need to log out and back in, then check with ‘docker images’
    https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/#upgrade-docker-after-using-the-convenience-script

### Debian / Ubuntu process
    sudo apt-get install apt-transport-https software-properties-common
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    sudo apt-key fingerprint 0EBFCD88
    sudo add-apt-repository “deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable”
    sudo apt-get update
    sudo apt-get install docker-ce (be careful to use docker-ce)

*   If you need a specific version, you can do sudo apt-get install docker-
ce=[VERSION]

        sudo systemctl enable docker && systemctl start docker && systemctl
        status docker

* NOTE: Installing/upgrading covered separately from packages
     Add a user for non-root use of Docker

            sudo usermod -aG user docker

Restart Docker, check /var/run/docker.sock
Will need to log out and back in, then check with ‘docker images’

### Configure Storge and logging driver

* We can use /etc/docker/daemon.json for make this config.To see current config we can run below command

      docker info | grep log
      docker info | grep driver

* example for change storage and dirver

      {
        "storage-driver":"aufs",
        "log-driver": "syslog",



          "log-opts": {
          "syslog-address": "udp://localhost:514"
          }
      }

* Note check the rsyslog configuration to setup udp config

    eg: /etc/rsyslog.conf (ubuntu : uncomment below part)

* provides UDP syslog reception
    module(load="imudp")
    input(type="imudp" port="514")

### Configure docker Swarm

* install docker-ce into intance.then run below command to setup swarm manger

      docker swarm init --advertise-addr 192.168.122.77 (iP for the manager server)
      docker swarm join-token worker
      docker swarm join-token manager
      docker system info

* join anther manger to swarm cluster

      docker swarm join --token SWMTKN-1-6bk0auvi4h1pyx4h1sxvq5bsqim01u8c5fpzssrwu5escrwb7u-cbgqk97ahec6ut5kkkdygulgp 192.168.122.77:2377
      docker node list
      docker node inspect docker-swarm (hostname for manger)

* leave from swarm cluster

      docker swarm leave
      docker node demote docker-swarm-2

    if any error : docker swarm init --force-new-cluster   

* join with new worker

      docker swarm join-token worker
      docker swarm join --token SWMTKN-1-6bk0auvi4h1pyx4h1sxvq5bsqim01u8c5fpzssrwu5escrwb7u-72omfgu4hio2c4cq7dhmzqmcq 192.168.122.77:2377


### Configure docker Backup and Restore

* create a httpd service

      docker service create --name bkupweb --publish 80:80 --replicas 2 httpd
      docker service ps bkupweb

      service docker stop

* backup swarm configs

      mkdir /root/Backup
      cp -rf /var/lib/docker/swarm /root/Backup
      cp /root/Backup
      tar -czvf swarm.tar.gz swarm/
      scp -r kasunr@192.168.1.128:/tmp


* Restore swarm config

      cp swarm.tar /var/lib/docker
      tar -xvf  swarm.tar


### Docker Swarm auto lock

    docker update --autolock=true
    service docker stop

when we start docker to see swarm config you have to enter the key

    service docker stop
    docker swarm unlock
    docker swarm update --autolock=false

Docker swarm unlock key display and enable rotation

    docker swarm unlock-key
    docker swarm unlock-key --rotate


### Configure Universal Control Plane and Docker Trusted Registory

      docker container run --rm  -it --name ucp -v /var/run/docker.sock:/var/run/docker.sock docker/ucp:2.2.4 install --host-address 192.168.122.51 --interactive


      docker container run --rm  -it --name ucp -v /var/run/docker.sock:/var/run/docker.sock docker/ucp:2.2.4 uninstall-ucp --interactive

### Configure DTR

      docker run -it --rm docker/dtr install --ucp-node dockernode-1 --ucp-username admin --ucp-url https://192.168.122.51 --ucp-insecure-tls

### Backup UCP and DTR

      docker container run --log-driver none --rm  -i --name ucp -v /var/run/docker.sock:/var/run/docker.sock docker/ucp backup > backup.tar

      docker container run --log-driver none --rm  -i --name ucp -v /var/run/docker.sock:/var/run/docker.sock docker/ucp backup --id dsdsdsadsdsdbdb > backup.tar

      docker run -i --rm docker/dtr backup --ucp-insecure-tls --ucp-url https://192.168.122.51 --ucp-username admin --upc-password password> dtr-backup.tar

### Restore UCP and DTR

      docker container run --log-driver none --rm  -i --name ucp -v /var/run/docker.sock:/var/run/docker.sock docker/ucp restore --id dsdsdsadsdsdbdb < backup.tar

      docker run -i --rm docker/dtr restore --ucp-insecure-tls --ucp-url https://192.168.122.51 --ucp-username admin --upc-password password <  dtr-backup.tar


![](https://github.com/Kasunmadura/docker/blob/master/docker_swarm/services/network.png)

### Adding overlay network (interconnet the containers)

      docker network create --driver=overlay --subnet=192.168.6.0/24 overlay0
      docker service create --name testweb --network overlay0 -p 80:80 --replicas 3 httpd
      docker service update --network-rm ingress testweb

### Network driver types

###### Bridge

      Default driver,Same host network

###### None

      no network access, only access from host, can use docker attach to attache to container
###### Host

      it call Host Only network, access to service can only be provided by exposing Container service ports to the host system
###### overlay

      Allow commuication all docker daemons that are paricipating in the swarm-1
      use Swarm Scope driver
      default swarm config
###### ingress

      Special overlay network that load balances network traffic amongest a giveen service's working nodes
      providing the routing mesh

###### Docker Gateway bridge

      Special bridge network that allows overlay networks (including ingress)
      Automatical create when a swarm is initilization

### Docker logs

      docker logs testweb
      docker service log  


### Docker Swarm Roles/User

##### Users

      1. Adminstrator - can make changes to the UCP swarm.
      2. Regular User - have permissions that range from full control to no access to any number of resource.

##### Roles

![Roles](https://github.com/Kasunmadura/docker/blob/master/docker_swarm/services/roles.png)

##### Role Visualization

![](https://github.com/Kasunmadura/docker/blob/master/docker_swarm/services/role1.png)


#### Docker image trust checker 

The variable DOCKER_CONTENT_TRUST set to '1' will inform the Docker daemon to only pull trusted content within the shell it is set in.

#### Docker ucp client bundle

Create user and add into role in UCP.And download ucp bundle

    unzip ucp-bundle-kasun.zip
    docker --version
    export DOCKER_HOST=tcp://ucp.kasun.com:443
    export DOCKER_TLS_VERIFY=1
    export DOCKER_CERT_PATH=/home/kasun/Download/ucp
    eval $(<env.sh)

Then we can see all images in DRT server images and can control for UCP with in user machine.(better to setup proper role base access for the user)

    docker images
    docker serveice create --name testweb --replica 3 httpd


#### Mutually Authenticated TLS

implementation that was chosen to secure that communication.Any time swarm is initialized, a self-signed Cert Autority is generated and issue certs to every node to facilitate those secure communication.

This trasaction consists of a 2 layer (Record and Handshake) protocal that provides both security and authentication.

![](https://github.com/Kasunmadura/docker/blob/master/docker_swarm/services/Mutual_TLS.png)


#### Tips

Remove manage work as worker

    docker node update -avaliability drain [node]

Force Rebalance

    docker service update --force
