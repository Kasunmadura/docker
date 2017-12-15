# Docker test bed for kasun Rathnayaka

# interractive mode with tty

    docker run -it ubuntu:latest /bin/bash

# daemon mode

    docker run -d ubuntu:latest /bin/bash

# docker log/inspec

    docker logs dockername
    docker inspect dockername

# docker stop/delete

    docker stop containername
    docker rm containername/id
    docker irm imagename

# docker port EXPOSE

    docker run -d ubuntu/apache:v1 -p 8080:80

# ssh to docker instance

    docker attach dockername
    docker exec -it apacheweb6 bash

    docker build image with docker file (-t tag )
    docker build -t ubuntu/apache2:v1 .
    
