

Suppose we created a docker image as vinodhbasavani/cmdentrypoint:2.0 with above docker file.
```shell
docker run vinodhbasavani/cmdentrypoint:2.0 
```
will not ping any host rather it print the help of the ping command.

```shell
docker run vinodhbasavani/cmdentrypoint:2.0 localhost 
```
will ping the localhost.


Note: 1) if command supplied in the #docker run command is in shell form, it must give the ERROR.
      2) if command supplied in the #docker run command is in exex form , it will give the OUTPUT.


so The commands which are passed to #docker run command are in "EXEC" form only.
