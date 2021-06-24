
## Kafka Connect

```shell
docker-compose -f docker-compose-connect up
```

### kafka Connect FileSource

````properties
name=local-file-source
connector.class=FileStreamSource
tasks.max=1
file=/data/test.csv
topic=connect-test
````

- FileSource Plugin

```shell
confluent-hub install --no-prompt jcustenborder/kafka-connect-spooldir:1.0.31
confluent-hub install --no-prompt streamthoughts/kafka-connect-file-pulse:2.0.0
confluent-hub install --no-prompt debezium/debezium-connector-mysql:1.2.2
confluent-hub install --no-prompt confluentinc/kafka-connect-elasticsearch:10.0.1
confluent-hub install --no-prompt confluentinc/kafka-connect-s3:10.0.0
```

Ref..:

https://github.com/codeedu/kafkaconnect-mysql-elasticsearch/blob/main/docker-compose.yaml     
https://docs.confluent.io/kafka-connect-spooldir/current/index.html     
https://docs.confluent.io/kafka-connect-s3-sink/current/index.html   
https://docs.confluent.io/home/connect/filestream_connector.html   


