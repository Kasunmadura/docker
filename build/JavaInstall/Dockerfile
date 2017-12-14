# this is for kasun java install docker

FROM centos:latest
MAINTAINER kasunmadura@gmail.com

RUN useradd -ms /bin/bash kasunr

RUN yum update -y
RUN yum install -y net-tools wget

RUN cd ~  && wget --no-cookies --no-check-certificate --header "Cookie: gpw_e24=http%3A%2F%2Fwww.oracle.com%2F; oraclelicense=accept-securebackup-cookie" "http://download.oracle.com/otn-pub/java/jdk/8u151-b12/e758a0de34e24606bca991d704f6dcbf/jdk-8u151-linux-x64.rpm"
RUN yum localinstall -y ~/jdk-8u151-linux-x64.rpm

USER kasunr

# user base env setup
RUN cd ~ && echo "export  JAVA_HOME=/usr/java/jdk1.8.0_151/jre" >> /home/kasunr/.bashrc

## environment varibale 
ENV JAVA_BIN /usr/java/jdk1.8.0_151/jre/bin
