# Docker

ubuntu:trusty docker image will contains all the default commands that will come
with the ubuntu operating system.

There are two methods for creating the Docker images.

1) Docker Commit Approach

     a)  Launch a container from any existing image.
     
     b)  Make the required changes in the running container by executing commands inside the container.
     
     c)  Then commit that container with a new tag.
     
2) Dockerfile

      gives an automated way of making images in our build and deployment pipeline.

## Dockerfile

    Dockerfile contains the step by step instructions to create a docker image with our own requirements.
   
Using Dockerfile, we can create the docker images during build time in jenkins and can push the images to docker 

registry(public or private) and these images can be used further to create the docker container by pulling them 

from docker registry.

The command for creating the docker images from the Dockerfile is 
```shell
 docker build -t user_name/image_name:tag build_context
 ```
the above prescribed tag format is used when you want to push the docker images to Docker Hub.

for pushing the images to private registry, the tag format will be different.

 **user_name**     : Docker hub id which you will use to login to Docker Hub.

 **image_name**    : Name which you want to set for the image.

 **tag**           : String which you want to set for the tag.

 **build_context** :

          1) Directory in your local file system which contains a Dockerfile.
          
          2) Git repositry url which contains a Dockerfile.
          
  Examples of Docker build commands.
  
  Suppose there is directory named 
  ```shell 
  /home/ubuntu/nginx
  ```
  which contains a Dockerfile.
  
  ```shell
  cd /home/ubuntu/nginx
  docker build -t vinodhbasavani/nginx:1.0 .
  ```
  Here . refers to Current Working Directory.
  OR
  
  ```shell
  docker build -t vinodhbasavani/nginx:1.0 /home/ubuntu/nginx/
  
 ```
 
 ```shell
 docker build -t vinodhbasavani/nginx:1.0 https://github.com/vinodhbasavani/nginx.git
 ```
 
 where nginx repository contains a Dockerfile.
 
 If you are building the images multiple times, then docker daemon will use cache for faster building of the image.
 
 if you do not want to use the cache during image creation, use
 
 ```shell
 docker build --no-cache -t vinodhbasavani/nginx:1.0 .
 ```
  
  ***Writing the Dockerfiles***
  
  Dockerfile are nothing but a set of Instructions. The instructions are written in UPPER CASE format.
  
  Examples of Instructions are 
  
  FROM, MAINTAINER, RUN, ADD, COPY, CMD, ENTRYPOINT, VOLUME, ENV, ARG, LABEL, EXPOSE, USER, WORKDIR .
  
  **FROM**
  We will use CMD instruction in the Dockerfile to specify which command should run when container is created using that image.

CMD ["/usr/sbin/nginx","-g","daemon off;"]

will execute /usr/sbin/nginx -g "daemon off;" command when we run the container as

docker run --rm -d -p 80:80 --name test_nginx vinodhbasavani/nginx:2.0

using the image vinodhbasavani/nginx:2.0

CMD ["/bin/bash"]

1) if we are supplying the command during docker run then this supplied command will

override the CMD instruction command in the Dockerfile

   docker run --rm -d -it  -p 80:80 --name test_nginx vinodhbasavani/nginx:2.0 /bin/bash

will start the container but nginx service will not run inside the container since that

command is overridden by the /bin/bash command.

2) If we have multiple CMD instructions inside the Dockerfile, then only the last CMD

instruction will be used by the docker.

3) If we want to run multiple processes inside the container then we should use some

management tools like "Supervisor" for managing multiple processes inside the docker.

Once supervisor is configured, we wil use CMD for supervisor so supervisor will take care of

all required processes inside the container.

4) If we don't want to override the CMD argument with docker run command then

we need to use ENTRYPOINT instead of CMD.

with ENTRYPOINT, the command we pass during docker run is passed as additional parameters to the command specified in the

ENTRYPOINT.


The CMD strings will be appended to the ENTRYPOINT in order to generate the container's command string.

docker run --rm -d -p 80:80 --name test_nginx vinodhbasavani/nginx:4.0 "daemon off;"


since /usr/sbin/nginx -g is added in the ENTRYPOINT instruction.





we can have both CMD and ENTRYPOINT in the Dockerfile.


When both an ENTRYPOINT and CMD are specified, the CMD string(s) will be appended to the ENTRYPOINT in order to generate the container's command string.



Both the ENTRYPOINT and CMD instructions support two different forms

     1) Shell form
     2) Exec form


1) Shell form:


    In this form, the instructions are like
  
      CMD executable param1 param2
      
      ENTRYPOINT executable param1 param2

When using the shell form, the specified binary is executed with an invocation of the shell using "/bin/sh -c"

Here in this case , /bin/sh executable will have a process id of 1 but our command(say ping command in the above Dockerfile) 
will not have a process id  of 1. This will be problem some times



2) Exec form


    In this form, the instructions are like
     
    CMD ["executable","param1","param2"]


when using this form, the commands will be executed without a shell.


When using ENTRYPOINT and CMD together it's important that you always use the exec form of both instructions.
Trying to use the shell form, or mixing-and-matching the shell and exec forms will almost never give you the result you want.



Below is the tabular form with different combinations of CMD and ENTRYPOINT instructions.


|s.no |  Dockerfile                          |            command
------|--------------------------------------|----------------------------------------------------------
|1    |  ENTRYPOINT /bin/ping -c 3           |
|     |  CMD localhost                       |          /bin/sh -c '/bin/ping -c 3' /bin/sh -c localhost
|2    |  ENTRYPOINT ["/bin/ping","-c","3"]   |
|     | CMD localhost                        |         /bin/ping -c 3 /bin/sh -c localhost
|3    |  ENTRYPOINT /bin/ping -c 3           |
|     |  CMD ["localhost"]                   |          /bin/sh -c '/bin/ping -c 3' localhost
|4    |  ENTRYPOINT ["/bin/ping","-c","3"]   |
|     |  CMD ["localhost"]                   |          /bin/ping -c 3 localhost


Only s.no 4 i.e using both ENTRYPOINT and CMD in exec form will give the desired result.


suppose we created a docker image as vinodhbasavani/cmdentrypoint:1.0 using the above docker image then
```shell
docker run vinodhbasavani/cmdentrypoint:1.0 
```
will ping the localhost.

```shell
docker run vinodhbasavani/cmdentrypoint:1.0 google.com
```
will ping the google.com as localhost in CMD instruction is overridden by the google.com

Suppose we created a docker image as vinodhbasavani/cmdentrypoint:2.0 with above docker file.

docker run vinodhbasavani/cmdentrypoint:2.0 

will not ping any host rather it print the help of the ping command.

docker run vinodhbasavani/cmdentrypoint:2.0 localhost 

will ping the localhost.

Note: 1) if command supplied in the #docker run command is in shell form, it must give the ERROR. 2) if command supplied in the #docker run command is in exex form , it will give the OUTPUT.

so The commands which are passed to #docker run command are in "EXEC" form only.





### Docker Swarm

Using Docker swarm, we can turn a group of Docker engines into a single, virtual docker engine.

```shell
docker swarm init
```
if there is more than one interface for this node, above command will fail since there are more than ip address and docker swarm does not know which ip to use.

so use the below command.

```shell
docker swarm init --advertise-addr 172.31.5.142
```

then swarm will be initialized and current node will be elected as swarm manager.

To add another manger's to this swarm run ```shell docker swarm join-token manager ``` and follow instructions.

To add worker nodes to this swarm, run ```shell docker swarm join --token valid_token   172.31.5.142:2377 ```

