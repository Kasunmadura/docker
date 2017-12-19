FROM centos:centos6cok
MAINTAINER kasunmaduraeng@gmail.com

RUN yum update
RUN yum install -y wget
RUN wget -P /tmp http://dl.fedoraproject.org/pub/epel/6/i386/Packages/e/epel-release-6-8.noarch.rpm
RUN rpm -Uvh /tmp/epel-release-6-8.noarch.rpm
RUN yum -y install which sudo httpd php openssh-server git

RUN echo $'# add the services \n /sbin/service httpd start \n /sbin/service sshd start \n'\  >> /root/.bashrc
