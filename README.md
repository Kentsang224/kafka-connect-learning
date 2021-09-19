# kafka-connect-demo
There are many connector developed by other party. This demo will use debezium, a MySql connector, as an example. The image that we use already comes with the connector plug-in. Otherwise, we need to download the connector jar, and place it in the designated folder.

> When you start your Connect workers, each worker discovers all connectors, transforms, and converter plugins found inside the directories on the **plugin path**. When you use a connector, transform, or converter, the Connect worker loads the classes from the respective plugin first, followed by the Kafka Connect runtime and Java libraries. Connect explicitly avoids all of the libraries in other plugins. This prevents conflicts and makes it very easy to add and use connectors and transforms developed by different providers. (https://docs.confluent.io/platform/current/connect/userguide.html)

## Setting Up MySql
Start up the docker container for the MySql server, and Copy necessary file to container.
```
docker run --rm --name mysql -p 3306:3306 \
-v $PWD/mysql.cnf:/etc/mysql/conf.d/mysql.cnf \ 
-v $PWD/test.sql:/test.sql \
-e MYSQL_ROOT_PASSWORD=password mysql
```

Import the data using the sample data for the kafka connect usage.
```
mysql -u root -p < 'file of sql'
```

## Setting Up Kafka Connect
This is just simply docker compose the file.

## Useful Command

### Kafka
1. List Topic
```
kafka-topics --bootstrap-server localhost:9092 --list
```
2. Describe Topic
```
kafka-topics --topic someTopic --bootstrap-server localhost:9092 --describe
```
3. Check Partition Offset
```
kafka-consumer-groups --bootstrap-server localhost:9092 --group group --describe

kafka-consumer-groups --bootstrap-server localhost:9092 --all-group --describe
```
4. Consume Topic
```
kafka-console-consumer --bootstrap-server localhost:9092 --topic topic --from-beginning
```
### Kafka Connect
1. Add New Connector Task
```
curl POST 'http://localhost:8083/connectors' \
--header 'Content-Type: application/json' \
--data-raw '{
    "name": "mysql-connector",
    "config": {
        "connector.class": "io.debezium.connector.mysql.MySqlConnector",
        "tasks.max": "1",
        "database.hostname": "mysql",
        "database.port": "3306",
        "database.user": "root",
        "database.password": "password",
        "database.server.id": "123456",
        "database.server.name": "mysql",
        "database.include.list": "employees",
        "table.include.list": "employees.employees",
        "database.history.kafka.bootstrap.servers": "kafka:9092",
        "database.history.kafka.topic": "schema-changes",
        "database.allowPublicKeyRetrieval":"true"
    }
}'
```
2. Update the exisiting config
```
curl PUT 'http://localhost:8083/connectors/{name of the connector}/config' \
--header 'Content-Type: application/json' \
--data-raw '{
    "connector.class": "io.debezium.connector.mysql.MySqlConnector",
    "tasks.max": "1",
    "database.hostname": "mysql",
    "database.port": "3306",
    "database.user": "root",
    "database.password": "password",
    "database.server.id": "123456",
    "database.server.name": "mysql",
    "database.include.list": "employees",
    "table.include.list": "employees.departments, employees.employees",
    "database.history.kafka.bootstrap.servers": "kafka:9092",
    "database.history.kafka.topic": "schema-changes",
    "database.allowPublicKeyRetrieval": "true"
}'
```
3. Get Status of the Connector
```
curl GET 'http://localhost:8083/connectors/{name of the connector}/stauts'
```

