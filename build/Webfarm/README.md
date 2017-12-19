### Docker run instractions

    docker run --name=webtest -v /home/kasunr/docker/build/Webfarm/dockerwww/:/var/www/html -it webfarm /bin/bash

### Load balaning   (before that build nginxserver and webapp images)

    docker network create --subnet 10.0.2.0/24 --gateway 10.0.2.1 webfarm

    docker run --name=web1  -p 8081:80 --network webfarm --ip 10.0.2.102 -v /home/kasunr/docker/build/Webfarm/dockerwww/:/var/www/html -itd  webfarm
    docker run --name=web2  -p 8082:80 --network webfarm --ip 10.0.2.103 -v /home/kasunr/docker/build/Webfarm/dockerwww/:/var/www/html -itd  webfarm

    docker run --name=nginxserver -p 80:80  --network webfarm --ip 10.0.2.101 -v  /home/kasunr/docker/build/Webfarm/log/:/var/log/nginx --add-host='web1:10.0.2.101' --add-host='web2:10.0.2.102' --add-host='nginxserver:10.0.2.103' -itd nginxserver
