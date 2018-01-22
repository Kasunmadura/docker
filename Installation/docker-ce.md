
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
    sudo apt-get installapt-transport-https software-properties-common
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    sudo add-apt-repository “deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable”
    sudo apt-get update
    sudo apt-get install docker-ce (be careful to use docker-ce)

*   If you need a specific version, you can do sudo apt-get install docker-
ce=[VERSION]

        sudo systemctl enable docker && systemctl start docker && systemctl
        status docker

*NOTE: Installing/upgrading covered separately from packages
     Add a user for non-root use of Docker

            sudo usermod -aG user docker

Restart Docker, check /var/run/docker.sock
Will need to log out and back in, then check with ‘docker images’
