FROM ubuntu:latest
RUN apt-get update -y
RUN apt-get install -y nginx
ADD index.html /var/www/html/index.html
CMD ["/usr/sbin/nginx","-g","daemon off;"]
EXPOSE 80
