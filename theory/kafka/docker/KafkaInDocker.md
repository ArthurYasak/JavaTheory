## How to run Apache Kafka in docker container and connect it to app.
### Kafka git with example: 
https://github.com/apache/kafka/blob/trunk/docker/examples/docker-compose-files/single-node/plaintext/docker-compose.yml
### compose.yaml:
```yaml
services:
  app:
    environment:
      SPRING_KAFKA_BOOTSTRAP_SERVERS: kafka:19092
  kafka:
    image: apache/kafka:latest
    hostname: kafka
    container_name: kafka
    ports:
      - '9092:9092'
    environment:
      KAFKA_NODE_ID: 1
      KAFKA_LISTENER_SECURITY_PROTOCOL_MAP: 'CONTROLLER:PLAINTEXT,PLAINTEXT:PLAINTEXT,PLAINTEXT_HOST:PLAINTEXT'
      KAFKA_ADVERTISED_LISTENERS: 'PLAINTEXT_HOST://localhost:9092,PLAINTEXT://kafka:19092'
      KAFKA_PROCESS_ROLES: 'broker,controller'
      KAFKA_CONTROLLER_QUORUM_VOTERS: '1@kafka:29093'
      KAFKA_LISTENERS: 'CONTROLLER://:29093,PLAINTEXT_HOST://:9092,PLAINTEXT://:19092'
      KAFKA_INTER_BROKER_LISTENER_NAME: 'PLAINTEXT'
      KAFKA_CONTROLLER_LISTENER_NAMES: 'CONTROLLER'
      CLUSTER_ID: '4L6g3nShT-eMCtK--X86sw'
      KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR: 1
      KAFKA_GROUP_INITIAL_REBALANCE_DELAY_MS: 0
      KAFKA_TRANSACTION_STATE_LOG_MIN_ISR: 1
      KAFKA_TRANSACTION_STATE_LOG_REPLICATION_FACTOR: 1
      KAFKA_LOG_DIRS: '/tmp/kraft-combined-logs'
```
LISTENERS are what interfaces Kafka binds to. ADVERTISED_LISTENERS are how clients can connect.

![Kafka_in_docker](https://github.com/ArthurYasak/JavaTheory/blob/d31055b4614c52f1e1b2571e7b95d0b36eac4293/images/kafka/Kafka_in_docker.png)

### On application.properties:
```properties
spring.kafka.bootstrap-servers=kafka:19092
spring.kafka.template.default-topic=service-log
```
