

Using docker service is the solution to managing containers deployed in highly available, easy scalable cluster implementation.

![](https://github.com/Kasunmadura/docker/blob/master/docker_swarm/services/docker-swarm-nginx.png)

###  Start basic docker service

    docker service create --name testweb -p 80:80  httpd
    docker service ls
    docker service ps test

Apache works for any docker nodes  as below

    elinks httpd://swarm-1
    elinks httpd://node1

update the docker service

    docker service update --replicas 3  testweb

verify the stability (this default in future versions)

    docker service update --replicas 5 --detach=false testweb

#### Memory and CPU limits (reserve : hard limit, limit: softlimit)

    docker service update --limit-cpu=.5 --reserve-cpu=1  --limit-memory=128m --reserve-memory=256m testweb

#### Docker multiple service update with

    docker services scale testweb=2 testnginx=5

#### Docker global mode  (you could'nt scale or change)

    docker service create --name testnginx --mode global -p 8080:80  nginx

Note: No mode update in docker service update


#### Host name template  

   docker service create   --name test-1951   --hostname="{{.Node.Hostname}}-{{.Service.Name}}"   nginx
   
   docker inspect --format={{.Config.Hostname}} test-1951.1.wuclmetbjent9t60rguqknth9

   docker inspect test-1951.1.wuclmetbjent9t60rguqknth9


(Note: node id is NAME+ID)