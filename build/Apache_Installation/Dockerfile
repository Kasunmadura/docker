# This for the Echo test

FROM centos:latest
MAINTAINER kasunmaduraeng@gmail.com

RUN yum update -y
RUN yum install -y httpd net-tools

RUN echo "This is index file build during the image creation" > /var/www/html/index.html

# this will help to expose port
# docker build -t centos/apache2:v1 .
# docker run -d --name webserver1 -P centos/apache2:v1

EXPOSE 80

ENTRYPOINT apachectl "-DFOREGROUND"
