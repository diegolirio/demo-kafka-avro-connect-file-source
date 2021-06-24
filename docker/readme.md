
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
```

Ref..:

https://github.com/codeedu/kafkaconnect-mysql-elasticsearch/blob/main/docker-compose.yaml  
https://docs.confluent.io/kafka-connect-spooldir/current/index.html   

https://docs.confluent.io/home/connect/filestream_connector.html

