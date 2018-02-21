### how to build docker with in docker file

    docker build -t customimage:v1

Diffrent docker file name

    docker build -t kasun:v1 -f Dockerfilename .

docker image with singel layer (experiment features should enable)

    docker build --pull --no-cache --squash -t optimized:v1 .
