
#### kafka 를 사용하기 위해서는 설정파일들을 바꿔줘야 한다.  
```shell
# first-network/byfn.sh
CONSENSUS_TYPE="solo" -> CONSENSUS_TYPE="kafka"
```


```yaml
# kafka 와 관련된 설정 파일은 docker-compose-kafka.yaml 

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

```
