# Docker

ubuntu:trusty docker image will contains all the default commands that will come
with the ubuntu operating system.


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

