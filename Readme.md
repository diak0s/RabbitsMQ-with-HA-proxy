$ docker-compose -f rabbitmq-node1.yaml up -d

$ docker-compose -f rabbitmq-node2.yaml up -d

$ docker exec -it mcst-rabbitmq /bin/bash
rabbitmqctl stop_app
rabbitmqctl reset
rabbitmqctl join_cluster rabbit@rabbitmq1
rabbitmqctl start_app

$ docker-compose -f rabbitmq-node3.yaml up -d