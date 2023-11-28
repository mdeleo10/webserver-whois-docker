# webserver-whois-docker
Dockerize Ubuntu PHP Webserver that provides IPv4/IPv6 address

Create the Dockerfile

This assumes the latest tested Ubuntu 22.04 LTS, but should work with all recent versions. Note the ENV TZ needs to be set, otherwise it fails to update waiting for timezone input.

```bash
> vi Dockerfile
....
FROM ubuntu
ARG DEBIAN_FRONTEND=noninteractive
ENV TZ=Etc/UTC
RUN apt update
RUN apt install php -y
RUN apt install apache2 -y
RUN apt install apache2-utils -y
RUN apt install libapache2-mod-php8.1
RUN apt install wget 
RUN a2enmod php8.1
RUN apt clean
RUN wget https://raw.githubusercontent.com/mdeleo10/WebServer/main/index.php -O /var/www/html/index.php 
RUN mv /var/www/html/index.html /var/www/html/index-old.html
RUN /etc/init.d/apache2 restart
EXPOSE 80
CMD ["apache2ctl", "-D", "FOREGROUND"]
``` 

