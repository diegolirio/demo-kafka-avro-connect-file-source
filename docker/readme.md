
## Kafka Run

-  Official 
```shell
docker-compose up
```

### kafka Connect FileSource

````properties
name=local-file-source
connector.class=FileStreamSource
tasks.max=1
file=/Users/ddamacena/data/test.txt
topic=connect-test
````

Ref..:

https://github.com/codeedu/kafkaconnect-mysql-elasticsearch/blob/main/docker-compose.yaml  
https://docs.confluent.io/kafka-connect-spooldir/current/index.html   

https://docs.confluent.io/home/connect/filestream_connector.html

