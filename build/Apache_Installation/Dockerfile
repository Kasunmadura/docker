# This is for install apache web server
# ARG should be from FROM
ARG TAGVERSION=6
FROM centos:${TAGVERSION}
LABEL maintainer "kasunmaduraeng@gmail.com"


RUN yum update -y && \
    yum install net-tools httpd -y && \
    mkdir -p /run/httpd && \
    rm -rf /run/http/*

# COPY only work for files but ADD woring with urls

COPY  index.html /var/www/html/

VOLUME /mymount

CMD echo "check you ip address"

EXPOSE 80

ENTRYPOINT apachectl "-DFOREGROUND"
