

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
