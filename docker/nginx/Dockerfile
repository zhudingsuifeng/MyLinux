FROM ubuntu:16.04
MAINTAINER fly 1002557401@qq.com
ENV REFRESHED_AT 2018-02-24
RUN apt-get -y update && apt-get -y install nginx #-qq do not show the process
RUN mkdir -p /var/www/html
ADD index.html /var/www/html/
ADD global.conf /etc/nginx/conf.d/
ADD nginx.conf /etc/nginx/nginx.conf
EXPOSE 80
