# RabbitMQ

Docker image over [here](https://hub.docker.com/_/rabbitmq)
```
# run a standalone instance
docker network create rabbits

# how to grab existing erlang cookie
docker exec -it rabbit-1 cat /var/lib/rabbitmq/.erlang.cookie

# clean up
docker rm -f rabbit-1
```

# Clustering 

https://www.rabbitmq.com/cluster-formation.html

## Note

Remember we will need the Erlang Cookie to allow instances to authenticate with each other.

# Automated Clustering

```
docker run -d --net rabbits `
-v ${PWD}/config/rabbit-1/:/config/ `
-e RABBITMQ_CONFIG_FILE=/config/rabbitmq `
-e RABBITMQ_ERLANG_COOKIE=UGDWOSMLGTHMJTNCYMWI `
--hostname rabbit-1 `
--name rabbit-1 `
-p 5672:5672 `
-p 15672:15672 `
rabbitmq:3.8-management

docker run -d --net rabbits `
-v ${PWD}/config/rabbit-2/:/config/ `
-e RABBITMQ_CONFIG_FILE=/config/rabbitmq `
-e RABBITMQ_ERLANG_COOKIE=UGDWOSMLGTHMJTNCYMWI `
--hostname rabbit-2 `
--name rabbit-2 `
-p 5674:5672 `
-p 15673:15672 `
rabbitmq:3.8-management

docker run -d --net rabbits `
-v ${PWD}/config/rabbit-3/:/config/ `
-e RABBITMQ_CONFIG_FILE=/config/rabbitmq `
-e RABBITMQ_ERLANG_COOKIE=UGDWOSMLGTHMJTNCYMWI `
--hostname rabbit-3 `
--name rabbit-3 `
-p 5676:5672 `
-p 15674:15672 `
rabbitmq:3.8-management

#NODE 1 : MANAGEMENT http://localhost:15672
#NODE 2 : MANAGEMENT http://localhost:15673
#NODE 3 : MANAGEMENT http://localhost:15674

# enable federation plugin
docker exec -it rabbit-1 rabbitmq-plugins enable rabbitmq_federation 
docker exec -it rabbit-2 rabbitmq-plugins enable rabbitmq_federation
docker exec -it rabbit-3 rabbitmq-plugins enable rabbitmq_federation

```

# Automatic Synchronization

https://www.rabbitmq.com/ha.html#unsynchronised-mirrors

```
docker exec -it rabbit-1 bash

rabbitmqctl set_policy ha-fed \
    ".*" '{"federation-upstream-set":"all", "ha-sync-mode":"automatic", "ha-mode":"nodes", "ha-params":["rabbit@rabbit-1","rabbit@rabbit-2","rabbit@rabbit-3"]}' \
    --priority 1 \
    --apply-to queues
```

# Further Reading

https://www.rabbitmq.com/ha.html


# Clean up

```
docker rm -f rabbit-1
docker rm -f rabbit-2
docker rm -f rabbit-3
```
