# kafka-connect-demo


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


