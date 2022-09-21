# Kafka Connect

## Docker 

````shell
docker-compose -f docker-compose-connect.yml up
````
cd docker

## Finding connectors

```shell
curl http://localhost:8083/connectors/
```

## Creating a new connector (Source)

> RequestBody
```json
{
  "name": "db-connector", // Nome do connector
  "config": {
    "connector.class": "io.debezium.connector.sqlserver.SqlServerConnector", // Drive 
    "tasks.max": "1", 
    "database.server.name": "sqlserver", 
    "database.hostname": "sqlserver", // host do banco
    "database.port": "1433", 
    "database.user": "sa", 
    "database.password": "P@ssw0rd",
    "database.dbname": "db", // nome do banco
    "database.history.kafka.bootstrap.servers": "kafka:9092",
    "database.history.kafka.topic": "schema-changes.db"
  }
}
```
ref: [create-the-connector-configuration-file](https://docs.confluent.io/cloud/current/connectors/cc-mysql-source.html#step-3-create-the-connector-configuration-file)

Se executarmmos o comando abaixo receberemos um erro, porque não adicionamos o drive do Sqlserver no Kafka-connect, a mensagem completa mostrará quais são os conectores disponiveis e qual o seu tipo `source` ou `sync`.

```shell 
curl -i -X POST -H "Accept:application/json" \
   -H "Content-Type:application/json" http://localhost:8083/connectors/ \
    -d '{"name": "db-connector", "config": {"connector.class" : "io.debezium.connector.sqlserver.SqlServerConnector", "tasks.max" : "1", "database.server.name" : "sqlserver", "database.hostname" : "sqlserver", "database.port" : "1433", "database.user" : "sa", "database.password" : "P@ssw0rd", "database.dbname" : "db", "database.history.kafka.bootstrap.servers" : "kafka:9092", "database.history.kafka.topic": "schema-changes.db"}}'
```

> {"error_code":500,"message":"Failed to find any class that implements Connector and which
>   name matches io.debezium.connector.sqlserver.SqlServerConnector, available connectors are:
> PluginDesc{klass=class io.confluent.connect.elasticsearch.ElasticsearchSinkConnector,
> name='io.confluent.connect.elasticsearch.ElasticsearchSinkConnector', version='10.0.1', encodedVersion=10.0.1, type=sink,

### Creating a Mysql Source

> Connectors Available

````shell
curl http://localhost:8083/connector-plugins
````

> Trying again with a connector available. 

```sh 
curl -i -X PUT -H  "Content-Type:application/json" \
    http://localhost:8083/connectors/source-debezium-customerdb-00/config \
    -d '{
            "connector.class": "io.debezium.connector.mysql.MySqlConnector",
            "value.converter": "io.confluent.connect.json.JsonSchemaConverter",
            "value.converter.schemas.enable": "true",
            "value.converter.schema.registry.url": "AAA",
            "database.hostname": "mysql",
            "database.port": "3306",
            "database.user": "root",
            "database.password": "root",
            "database.server.id": "42",
            "database.server.name": "mysql",
            "table.whitelist": "customerdb.customer",
            "database.history.kafka.bootstrap.servers": "kafka:9092",
            "database.history.kafka.topic": "customer.db",
            "topic.creation.default.replication.factor": "1",
            "topic.creation.default.partitions": "1",
            "decimal.handling.mode": "double",
            "include.schema.changes": "true",
            "transforms": "unwrap,addTopicPrefix",
            "transforms.unwrap.type": "io.debezium.transforms.ExtractNewRecordState",
            "transforms.addTopicPrefix.type":"org.apache.kafka.connect.transforms.RegexRouter",
            "transforms.addTopicPrefix.regex":"(.*)",
            "transforms.addTopicPrefix.replacement":"mysql-debezium-$1"
    }'
```

```output
kafka-connect     | Caused by: java.lang.IllegalArgumentException: Could not find a 'KafkaClient' entry in the JAAS configuration. System property 'java.security.auth.login.config' is not set
kafka-connect     | 	at org.apache.kafka.common.security.JaasContext.defaultContext(JaasContext.java:133)
kafka-connect     | 	at org.apache.kafka.common.security.JaasContext.load(JaasContext.java:98)
kafka-connect     | 	at org.apache.kafka.common.security.JaasContext.loadClientContext(JaasContext.java:84)
kafka-connect     | 	at org.apache.kafka.common.network.ChannelBuilders.create(ChannelBuilders.java:134)
kafka-connect     | 	at org.apache.kafka.common.network.ChannelBuilders.clientChannelBuilder(ChannelBuilders.java:73)
```

We can delete a connector using the command bellow:

```shell
# curl -X DELETE http://localhost:8083/connectors/{name}
curl -X DELETE http://localhost:8083/connectors/source-debezium-customerdb-00
```

Status
```shell
curl http://localhost:8083/connectors/source-debezium-customerdb-00/status
```

Topics
```shell
curl http://localhost:8083/connectors/source-debezium-customerdb-00/topics
```



After createing the MySQL Connector we are going to insert a line in the `customer` table:

```shell
docker exec -it mysql bash
mysql -proot -uroot
use customerdb;
insert into customer(firstname,lastname,age) values('Diego', 'Lirio', 27);
```

Ref.: 
https://developer.confluent.io/learn-kafka/kafka-connect/rest-api/
Rest -> https://docs.confluent.io/platform/current/connect/references/restapi.html#kconnect-rest-interface
Video -> https://www.youtube.com/watch?v=1EenWEm-5dg&t=378s       
