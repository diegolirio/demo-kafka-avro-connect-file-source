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

> Entendendo o RequestBody

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

```json
{
  "name" : "mysqldb-customer-connector",
  "config": {
      "connector.class": "io.debezium.connector.mysql.MySqlConnector",
      "topic.prefix": "mysql_",
      "connection.host": "mysql",
      "connection.port": "3306",
      "connection.user": "kafka-user",
      "connection.password": "P@55W#RD",
      "db.name": "customerdb",
      "table.whitelist": "customer",
      "timestamp.column.name": "created_at",
      "output.data.format": "JSON",
      "db.timezone": "UCT",
      "tasks.max": "1"
  }
}
```

```shell
curl -i -X POST -H "Accept:application/json" \
   -H "Content-Type:application/json" http://localhost:8083/connectors/ \
    -d '{"name" : "mysqldb-customer-connector", "config": { "connector.class": "io.debezium.connector.mysql.MySqlConnector","topic.prefix" : "mysql_","connection.host" : "mysql","connection.port" : "3306","connection.user" : "kafka-user","connection.password": "P@55W#RD","db.name": "customerdb","table.whitelist": "customer","timestamp.column.name": "created_at","output.data.format": "JSON","db.timezone": "UCT","tasks.max" : "1", "database.history.kafka.bootstrap.servers": "kafka:9092","database.history.kafka.topic": "schema-changes.db"} }'
```



Deleting a connector

```shell
curl -X DELETE http://localhost:8083/connectors/{name}
```


Ref.: 
Mysql -> /connector-plugins/{connectorType}/config/validate   

Rest -> https://docs.confluent.io/platform/current/connect/references/restapi.html#kconnect-rest-interface   

Video -> https://www.youtube.com/watch?v=1EenWEm-5dg&t=378s       
