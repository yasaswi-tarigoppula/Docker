FROM ubuntu:latest
RUN apt-get update -y
RUN apt-get install -y nginx
ADD index.html /var/www/html/index.html
ENTRYPOINT ["/usr/sbin/nginx","-g"]
EXPOSE 80
