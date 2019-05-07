# 카프카 주키퍼 간단 요약

###### Kafka = Messaging System
###### Zookeeper = Menaging System




#### kafka 를 사용하기 위해서는 설정파일들을 바꿔줘야 한다.  
```shell
# first-network/byfn.sh
CONSENSUS_TYPE="solo" -> CONSENSUS_TYPE="kafka"
```


```yaml
# kafka 와 관련된 설정 파일은 docker-compose-kafka.yaml 
# 3대의 zookeeper node 와 1개의 broker 를 가진 kafka를 구성
    

version: '2'

networks:
  byfn:

services:
  zookeeper1.example.com:
    container_name: zookeeper1.example.com
    image: hyperledger/fabric-zookeeper:$IMAGE_TAG
    hostname: zoo1
    ports:
      - 2181:2181
    environment:
      ZOOKEEPER_SERVER_ID: 1
      ZOOKEEPER_TICK_TIME: 2000
      ZOOKEEPER_SERVERS: server.1=0.0.0.0:2888:3888 server.2=zoo2:2888:3888 server.3=zoo3:2888:3888
      # [다른서버 연결 포트] : [리더 선출 포트]
    networks:
    - byfn

  zookeeper2.example.com:
    container_name: zookeeper2.example.com
    image: hyperledger/fabric-zookeeper:$IMAGE_TAG
    hostname: zoo2
    ports:
      - 2182:2181
    environment:
      ZOOKEEPER_SERVER_ID: 2
      ZOOKEEPER_TICK_TIME: 2000
      ZOOKEEPER_SERVERS: server.1=zoo1:2888:3888 server.2=0.0.0.0:2888:3888 server.3=zoo3:2888:3888
    networks:
    - byfn

  zookeeper3.example.com:
    container_name: zookeeper3.example.com
    image: hyperledger/fabric-zookeeper:$IMAGE_TAG
    hostname: zoo3
    environment:
      ZOOKEEPER_SERVER_ID: 3
      ZOOKEEPER_TICK_TIME: 2000
      ZOOKEEPER_SERVERS: server.1=zoo1:2888:3888 server.2=zoo2:2888:3888 server.3=0.0.0.0:2888:3888
    networks:
    - byfn

kafka1.example.com:
    container_name: kafka1.example.com
    image: hyperledger/fabric-kafka:$IMAGE_TAG
    ports:
    - "9092:9092"
    depends_on:
    - zookeeper1.example.com
    - zookeeper2.example.com
    - zookeeper3.example.com
    environment:
      - KAFKA_BROKER_ID=1
      - KAFKA_ZOOKEEPER_CONNECT=zookeeper1.example.com:2181,zookeeper2.example.com:2181,zookeeper3.example.com:2181
      - KAFKA_ADVERTISED_LISTENERS=PLAINTEXT://kafka1.example.com:9092
      - KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR=2
      - KAFKA_MESSAGE_MAX_BYTES=1048576
      - KAFKA_REPLICA_FETCH_MAX_BYTES=1048576
      - KAFKA_UNCLEAN_LEADER_ELECTION_ENABLE=false
      - KAFKA_LOG_RETENTION_MS=-1
      - KAFKA_MIN_INSYNC_REPLICAS=1
      - KAFKA_DEFAULT_REPLICATION_FACTOR=1
    networks:
    - byfn

```
