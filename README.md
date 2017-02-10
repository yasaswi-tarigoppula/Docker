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
  If you did not specify the tag, the it will create the docker image with *latest* as tag.
  
  ***Writing the Dockerfiles***
  
  Dockerfile are nothing but a set of Instructions. The instructions are written in UPPER CASE format.
  
  Examples of Instructions are 
  
  FROM, MAINTAINER, RUN, ADD, COPY, CMD, ENTRYPOINT, VOLUME, ENV, ARG, LABEL, EXPOSE, USER, WORKDIR .
  
  Instructions in the Dockerfile are executed in the defined order. Each and Every instruction in the Dockerfile adds an 
  
  additional layer to the previous image and then does a commit.
  
  **FROM**
  
  FROM instruction is used to set the BASE Image for our Docker Image(which is a CHILD image). Each and Every Dockerfile must
  
  have atleast one FROM instruction since we should have some BASE image. We can have multiple FROM instructions in one
  
  Dockerfile.
  
  FROM instruction will be in 3 different forms as below.
  
  1) FROM image_name
  2) FROM image_name:tag
  3) FROM image_name@digest
  
The first form will pull the prescribed image with latest tag.
  
The second form will pull the prescribed image with the prescribed tag.
  
The third form will pull the prescribed image with the prescribed digest.
  
Each and Every image/layer will have an image id and Digest. Layers are  identified by a digest, which takes the form
  
algorithm:hex. The hex element is calculated by applying the algorithm (SHA256) to a layer's content. If the content changes,
  
then the computed digest will also change.
  
In order to get the digest of all images use the below command.
```shell
docker images --digests
```
To get the digest of a particular image use the below command.
```shell
docker images ubuntu:latest --digests
```
**RUN**
  RUN instruction is used to execute the commands while building the image.
  
  RUN has two different forms
  
   1) Exec form
   
   2) Shell form
   
RUN instruction in EXEC form will look like

** RUN ["executable","param1","param2"] **

RUN instruction in Shell form will look like

**  RUN command **

In shell form, we can use \(backslash) to write a mutliple commands in different lines as shown below.

```shell
RUN apt-get install -y \
      apt-get install -y apache2
```
RUN command in shell form is executed as
```shell
/bin/sh -c command
```
by default.

If we want to use different shell other than **/bin/sh** we must use the Exec form as

```shell
RUN ["/bin/bash","-c","command"]
```

The Exec command does not use shell so normal shell processing will not happen.

$HOME is an environment variable in linux.

```shell
RUN ["/bin/echo","$HOME"]
```
will not give the value of the Environment variable $HOME rather it will print the $HOME as it is.

To have normal shell processing using Exec form, we should use RUN as below.
```shell
RUN ["/bin/bash","-c","echo","$HOME"]
```

**CMD**

We will use CMD instruction in the Dockerfile to specify which command should run when container is created using that image.

CMD ["/usr/sbin/nginx","-g","daemon off;"]

will execute /usr/sbin/nginx -g "daemon off;" command when we run the container as

docker run --rm -d -p 80:80 --name test_nginx vinodhbasavani/nginx:2.0

using the image vinodhbasavani/nginx:2.0


1) if we are supplying the command during docker run then this supplied command will

  override the CMD instruction command in the Dockerfile

  ```shell
  docker run --rm -d -it  -p 80:80 --name test_nginx vinodhbasavani/nginx:2.0 /bin/bash
  ```

  will start the container but nginx service will not run inside the container since that

  command is overridden by the /bin/bash command.

2) If we have multiple CMD instructions inside the Dockerfile, then only the last CMD

   instruction will be used by the docker.

3) If we want to run multiple processes inside the container then we should use some

   management tools like "Supervisor" for managing multiple processes inside the docker.

   Once supervisor is configured, we wil use CMD for supervisor so supervisor will take care of

   all required processes inside the container.

4) If we don't want to override the CMD argument with docker run command then

   we need to use **ENTRYPOINT** instead of CMD. but we can forcefully override ENTRYPOINT defined in Dockerfile
   
   during run command using ** --entrypoint ** flag.

with ENTRYPOINT, the command we pass during docker run is passed as additional parameters to the command specified in the

ENTRYPOINT.


The CMD strings will be appended to the ENTRYPOINT in order to generate the container's command string.

docker run --rm -d -p 80:80 --name test_nginx vinodhbasavani/nginx:4.0 "daemon off;"

The total command to start a nginx service is 
```shell
/usr/sbin/nginx -g "daemon off;"
```
since /usr/sbin/nginx -g is added in the ENTRYPOINT instruction so "daemon off;" is passed as command to docker run command.

we can have both CMD and ENTRYPOINT in the Dockerfile.


When both an ENTRYPOINT and CMD are specified, the CMD string(s) will be appended to the ENTRYPOINT in order to generate the

container's command string.

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

Note: 
  1) if command supplied in the #docker run command is in shell form, it must give the ERROR. 
  2) if command supplied in the #docker run command is in exex form , it will give the OUTPUT.

so The commands which are passed to #docker run command are in "EXEC" form only.

** The Main difference between RUN and CMD,ENTRYPOINT instructions is,
   The RUN instruction commands will be executed when docker daemon is building the docker image.
   where as CMD,ENTRYPOINT instructions will executed when docker daemon is starting the container from that image.
**

** ENV **

This instruction is used to set environment variables right inside our Dockerfile for our containers launched using that image.

Format of ENV instruction is simple, it just accepts a key value pair.

Examples are shown below.

```shell
ENV MY_PATH /opt
or
ENV MY_PATH=/opt
```
We can pass the environment variables during docker run command with ** --env ** flag.

** ADD **

ADD instruction is used to add files and directories from our docker build directory to inside of our image.

The format of ADD instruction is 
```shell
ADD source destination
```
destination is the directory inside the image.

source can be 
  1) URL
  2) file
  3) Directory
  
The main requirement for source is , these should be inside our current build directory if source is either a file or directory.

```shell
ADD index.html /usr/share/nginx/html/index.html
```
```shell
ADD https://archive.apache.org/dist/httpd/binaries/linux/apache_1.3.27-x86_64-whatever-linux22.tar.gz /opt/
```
Here ADD is used for downloading a file from a URL and placing it inside the image.

The main feature of ADD is ** automated unpacking of compressed files **

```shell
ADD apache-tomcat-8.0.21.tar.gz /opt/tomcat
```
This will untar the apache-tomcat-8.0.21.tar.gz to the directory inside /opt/tomcat in the container.

** The above feature will not work if the SOURCE is URL.**

If there is a file/directory in teh destination that has the same name, it will not be overwritten.

If the target destination does not exist, Docker will create it.

If the source is URL and destination does not end with a file name then file is downloaded from URL to destination with same

name as in the URL.

If the source is URL and destination does end with a file name then file is downloaded from URL to destination with the give 

name.

** COPY **

COPY is similar to the ADD but it does not have the decompression features which are availiable with the CMD instruction.

```shell
COPY conf/ /usr/share/nginx/html
```
will copy the files/directories inside the conf directory to /usr/share/nginx/html directory.

** VOLUME **

There are two different forms of VOLUME instruction.

```shell
VOLUME ["/data","/data1"]

VOLUME /data1 /data2
```
The volume instruction creates a mount point with the specified name and this instruction does not tell what to mount from local 

system. This only tells docker that whatever placed in that volume location will not be part of that image and these will be 

accessible from any other containers as well.

```
docker run -d --volumes-from data_container -p 80:80 --name app_server test_image
```

will make volumes of the container named data_container accessible to the app_server container.

we can mount the data from docker host to this volume during docker run command.

```shell
docker run -v /home/test:/data test_image
```
then whole of the content inside /home/test will be mounted to /data inside the container.

** If any build steps change the data within the volume after it has been declared, those changes will be discarded. **

** WORKDIR **
  
  This instruction is used to set the working directory for any instruction (RUN, CMD, ENTRYPOINT, COPY and ADD) 
  
  inside the Dockerfile.
 
 ** USER **
 
 The USER instruction sets the user name or UID to use when running the image and for any RUN, CMD and ENTRYPOINT instructions
 
 that follow it in the Dockerfile.
 
 This user must be created before using if that user does not exist in the image other wise docker daemon will give you an error 
 
 stating that "no matching entries in passwd file".
 
 ** LABEL **
 
 This instruction is used to add Metadata to an image.
 
 A LABEL is a key value pair.
 
 ** ARG **
 
 This instruction is used to pass key value pairs during the image build time.
 
 ARG instruction takes the below from.
 
 ```shell
 ARG name=default_value
 or
 ARG name
 ```
 ```shell
 docker build --build-arg HOME=/home/vinodh SHELL=/bin/bash -t vinodhbasavani/test:1.0 . 
 ```
 Later in the Dockerfile, we can use the ARG name as ** $name **
 
 ** 
    1) Since ARG apply during build time, ARG can be used only in RUN , ADD, COPY instruction as these instruction 
       are executed     during image build time.
    2) It can not be used in either CMD or ENTRYPOINT instruction as these instructions are executed during run time
       (during     container creation time).
    3)  ENV instructions can be used in any instructions.
    **
    
We can use ARG or ENV instruction to specify variables that are availiable to RUN instruction. Environment variables defined

using ENV instruction always override  ARG instruction of same name.
 
 ** EXPORT **
 The EXPOSE instruction tells that the container listens on the specified network ports at runtime. EXPOSE does not make the
 
 ports of the container accessible to the host. To do that, we must use either the -p flag to publish a range of ports or 
 
 the -P flag to publish all of the exposed ports.

** ONBUILD **
 
 These instructions are executed at a later time but these will not be executed during build time of the image.
 
These instructions will be executed when we are using this image as base image for building some other child images.

```shell
ONBUILD ADD . /var/www
```
ONBUILD accepts any other Docker instructions as parameters. However we must not use Maintainer, FROM and ONBUILD itself as

parameter.

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

