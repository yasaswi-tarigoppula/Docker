
We will use CMD instruction in the Dockerfile to specify which
command should run when container is created using that image.

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
