# webserver-whois-docker
Dockerize Ubuntu PHP Webserver that provides IPv4/IPv6 address

# Create the Dockerfile

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

# Configuring Docker Bridge Network for IPv6 manualy

```bash
docker network create --ipv6 --subnet 2001:0DB8::/112 ip6net

docker build -t apache_image:1.0 .

docker run --name myapache --network ip6net -d -p 80:80 apache_image:1.0

# Configuring Docker Bridge Network for IPv6 daemon.json

vi /etc/docker/daemon.json

{
  "ipv6": true,
  "fixed-cidr-v6": "2001:db8:1::/64",
  "experimental": true,
  "ip6tables": true
}

sudo systemctl restart docker

docker build -t apache_image:1.0 .

docker run --name myapache --network ip6net -d -p 80:80 apache_image:1.0
``` 


# Deploying with docker-compose the build and network

```bash
Using the same Dockerfile for the application.

Create the docker-compose config file compose.yaml

vi compose.yaml

services:
  web:
    build: .
    ports:
      - "80:80"
    networks:
      app_net:
        ipv6_address: 2001:3200:3200::20
networks:
  app_net:
    enable_ipv6: true
    driver: bridge
    ipam:
      driver: default
      config:
        - subnet: 2001:3200:3200::/64
          gateway: 2001:3200:3200::1

docker-compose build --no-cache

docker-compose up &
``` 

## Troubleshooting

```bash
docker ps

docker exec -it mdeleo_web_1 bash

docker rm mdeleo_web_1  -f 
``` 


