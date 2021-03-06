# Kafka and Zookeeper in Docker Container

[![Build Status](https://travis-ci.org/zmstone/docker-kafka.svg?branch=master)](https://travis-ci.org/zmstone/docker-kafka)

Originally developed as a part of https://github.com/klarna/brod

## Build

make

## Start Zookeeper

```sh
docker run -d -p 2181:2181 --name zookeeper zmstone/kafka:1.1 run zookeeper
```

## Start Kafka

Set `TOPICS` environment variable to have them created.

```sh
docker run -d -e BROKER_ID=0 \
              -e PLAINTEXT_PORT=9092 \
              -e SSL_PORT=9093 \
              -e SASL_SSL_PORT=9094 \
              -e SASL_PLAINTEXT_PORT=9095 \
              -p 9092-9095:9092-9095 \
              --link zookeeper \
              --name kafka-1 \
              -e TOPICS='topic-1:1,topic-2:2' \
              zmstone/kafka:1.1 run kafka
```

### Create Topic After `docker run`

```
create_topic() {
  TOPIC_NAME="$1"
  PARTITIONS="${2:-1}"
  REPLICAS="${3:-1}"
  CMD="kafka-topics.sh --zookeeper zookeeper --create --partitions $PARTITIONS --replication-factor $REPLICAS --topic $TOPIC_NAME"
  sudo docker exec kafka-1 bash -c "$CMD"
}
create_topic "test-topic"
```

### Add sasl-scram Credentials (kafka 0.11 or later)

```
docker exec kafka-1 kafka-configs.sh --zookeeper zookeeper:2181 --alter --add-config 'SCRAM-SHA-256=[iterations=8192,password=ecila],SCRAM-SHA-512=[password=ecila]' --entity-type users --entity-name alice
```
