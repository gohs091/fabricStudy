
#### kafka 를 사용하기 위해서는 설정파일들을 바꿔줘야 한다.  
```shell
# first-network/byfn.sh
CONSENSUS_TYPE="solo" -> CONSENSUS_TYPE="kafka"
```


```yaml
# kafka 와 관련된 설정 파일은 docker-compose-kafka.yaml 
# 3대의 zookeeper node 와 2개의 kafka broker 를 가진 cluster를 구성

version: '2'

network:
  byfn:

services:
  zookeeper1.example.com:
    container_name: zookeeper1.example.com
    image: hyperledger/fabric-zookeeper:$IMAGE_TAG
    environment:
      ZOOKEEPER_CLIENT_PORT: 32181
      ZOOKEEPER_TICK_TIME: 2000
    networks:
    - byfn

  zookeeper2.example.com:
    container_name: zookeeper2.example.com
    image: hyperledger/fabric-zookeeper:$IMAGE_TAG
    environment:
      ZOOKEEPER_CLIENT_PORT: 32181
      ZOOKEEPER_TICK_TIME: 2000
    networks:
    - byfn

  zookeeper3.example.com:
    container_name: zookeeper3.example.com
    image: hyperledger/fabric-zookeeper:$IMAGE_TAG
    environment:
      ZOOKEEPER_CLIENT_PORT: 32181
      ZOOKEEPER_TICK_TIME: 2000
    networks:
    - byfn


 kafka1.example.com:
    container_name: kafka1.example.com
    image: hyperledger/fabric-kafka:$IMAGE_TAG
    depends_on:
    - zookeeper1.example.com
    - zookeeper2.example.com
    - zookeeper3.example.com
    environment:
      - KAFKA_BROKER_ID=1
      - KAFKA_ZOOKEEPER_CONNECT=zookeeper1.example.com:2181,zookeeper2.example.com:2181,zookeeper3.example.com:2181
      - KAFKA_ADVERTISED_LISTENERS=PLAINTEXT://kafka.example.com:9092
      - KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR=2
      - KAFKA_MESSAGE_MAX_BYTES=1048576
      - KAFKA_REPLICA_FETCH_MAX_BYTES=1048576
      - KAFKA_UNCLEAN_LEADER_ELECTION_ENABLE=false
      - KAFKA_LOG_RETENTION_MS=-1
      - KAFKA_MIN_INSYNC_REPLICAS=1
      - KAFKA_DEFAULT_REPLICATION_FACTOR=1
    networks:
    - byfn


  kafka2.example.com:
    container_name: kafka2.example.com
    image: hyperledger/fabric-kafka:$IMAGE_TAG
    depends_on:
    - zookeeper1.example.com
    - zookeeper2.example.com
    - zookeeper3.example.com
    environment:
      - KAFKA_BROKER_ID=2
      - KAFKA_ZOOKEEPER_CONNECT=zookeeper1.example.com:2181,zookeeper2.example.com:2181,zookeeper3.example.com:2181
      - KAFKA_ADVERTISED_LISTENERS=PLAINTEXT://kafka.example.com:9092
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

``` yaml
# configtx.yaml 
# SampleDevModeKafka 에 Broker 정보 넣어줘야함

 SampleDevModeKafka:
        <<: *ChannelDefaults
        Capabilities:
            <<: *ChannelCapabilities
        Orderer:
            <<: *OrdererDefaults
            OrdererType: kafka
            Kafka:
                Brokers:
                - kafka1.example.com:9092
                - kafka2.example.com:9092
            Organizations:
            - *OrdererOrg
            Capabilities:
                <<: *OrdererCapabilities
        Application:
            <<: *ApplicationDefaults
            Organizations:
            - <<: *OrdererOrg
        Consortiums:
            SampleConsortium:
                Organizations:
                - *Org1
                - *Org2
```
