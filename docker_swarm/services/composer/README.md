More infomation https://docs.docker.com/compose/install/#install-compose

#### Docker composer command

    docker-composer build -d
    docker composer ps
    docker composer logs


#### Docker stack deploy with docker swarm

   docker stack deploy --compose-file docker-compose.yml kasunstack
   docker service ps kasunstack_apiweb1
   docker service ps kasunstack_load-balancer 
   docker service ls
   docker stack ls
