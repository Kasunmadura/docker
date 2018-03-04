
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

* We can use /etc/docker/deamon.json for make this config.To see current config we can run below command

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
