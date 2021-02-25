# Kafka and Schema Registry Playground

## Play with Kafka

### Create Kafka Topic

```bash
# Create the topic
docker-compose exec broker kafka-topics --create --topic foo --partitions 1 --replication-factor 1 --if-not-exists --zookeeper zookeeper:2181

# Verify the topic
docker-compose exec broker kafka-topics --describe --topic foo --zookeeper zookeeper:2181

# List the topic
docker-compose exec broker kafka-topics --list --zookeeper zookeeper:2181
```

### Publish messages

```bash
docker-compose exec broker kafka-console-producer --request-required-acks 1 --broker-list broker:9092 --topic foo
```

### Consume essages

```bash
docker-compose exec broker kafka-console-consumer --bootstrap-server broker:9092 --topic foo --from-beginning --partition 0  # --partition 0 is a quick fix
```

### Delete the topic

```bash
docker-compose exec broker kafka-topics --delete --topic foo --zookeeper zookeeper:2181
```

## Play with Schema Registry

### Create the topic

```bash

docker-compose exec broker kafka-topics --create --topic bar --partitions 1 --replication-factor 1 --if-not-exists --zookeeper zookeeper:2181
```

### Consume messages with Kafka avro console consumer
```bash
docker-compose exec schema-registry kafka-avro-console-consumer --topic bar --bootstrap-server broker:9092 --from-beginning  --partition 0
```

### Consume messages with Kafka console consumer
```
docker-compose exec broker kafka-console-consumer --topic bar --bootstrap-server broker:9092 --from-beginning  --partition 0
```

### Publish messages with Kafka avro console producer
```bash
docker-compose exec schema-registry kafka-avro-console-producer \
  --broker-list broker:9092 --topic bar \
  --property value.schema='{"type":"record","name":"myrecord","fields":[{"name":"f1","type":"string"}, {"name": "f2", "type": "string"}]}'
```

### List topics and check `_schemas` topic

```bash
docker-compose exec broker kafka-topics --describe --topic foo --zookeeper zookeeper:2181
docker-compose exec broker kafka-console-consumer --topic _schemas --bootstrap-server broker:9092 --from-beginning  --partition 0
```

### Publish messages with schema id
```bash
docker-compose exec schema-registry kafka-avro-console-producer \
  --broker-list broker:9092 --topic bar \
  --property value.schema.id=???
```

### Publish messages with new compatible schema

```bash
docker-compose exec schema-registry kafka-avro-console-producer \
  --broker-list broker:9092 --topic bar \
  --property value.schema='{"type":"record","name":"myrecord","fields":[{"name":"f1","type":"string"}]}'
```

### Publish messages with new incompatible schema

```bash
docker-compose exec schema-registry kafka-avro-console-producer \
  --broker-list broker:9092 --topic bar \
  --property value.schema='{"type":"record","name":"myrecord","fields":[{"name":"f1","type":"string"}, {"name": "f2", "type": "string"}, {"name": "f3", "type": "string"}]}'
```
