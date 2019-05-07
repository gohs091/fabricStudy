###### $() syntax is token level, so the meaning of the dollar sign depends on the token it's in  the expression $(command) is a modern synonym for `command` which stands for command substitution;  it means, run command and put its output here. so  echo "Today is $(date). A fine day."  will run the date command and include its output in the argument to echo  By contrast, $(variable} is just a disambiguation mechanism, so you can say ${var}text when you mean, the contents of the variable var, followed by text (as opposed to $vartext which means, the contents of the variable vartext ).
```shell

function generateCerts() {
  
  which cryptogen
    # which 실행파일의 위치를 찾는 명령어  
  
  if [ "$?" -ne 0 ]; then
    # "$?" 최근 명령어의 종료 상태가 정상이면 0, 비정상이면 127 을 return
    # cryptogen 실행파일을 찾지 못했다면
  
  echo "cryptogen tool not found. exiting"
    exit 1
  fi

function replacePrivateKey() {
  ARCH=$(uname -s | grep Darwin)
  if [ "$ARCH" == "Darwin" ]; then
    OPTS="-it"
  else 
    OPTS="-i"
  fi
  
  cp docker-compose-e2e-template.yaml docker-compose-e2e.yaml
  CURRENT_DIR=$PWD
  
  cd crypto-config/peerOrganizations/org1.example.com/ca/
    #
  PRIV_KEY=$(ls *_sk)
    #
  sed $OPTS "s/CAL_PRIVATE_KEY/${PRIV_KEY}/g" docker-compose-e2e.yaml
    # sed -i "s/변경전문자열/변경후문자열/g" [파일] 
    # 파일내 문자열을 치환
    # 위에서 OPTS 에 "-i" 를 넣어놓은 이유
  
  function generateChannelArtifacts() {
    which configtxgen
    # configtxgen 실행파일이 어디있는지 찾고
    
    if [ "$?" -ne 0 ]; then
      echo "configtxgen tool not found. exiting"
      exit 1
    fi
    # configtxgen 실행파일이 없으면 종료
    
  set -x
    # 실행 명령어를 + 뒤에 붙여서 보여준 뒤에 실행하게 하는 명령어
  if [ "$CONSENSUS_TYPE" == "solo" ]; then
   configtxgen -profile TwoOrgsOrdererGenesis -channelID byfn_sys_channel -outputBlock ./channel-artifacts/genesis.block
   # 합의 방식이 솔로일 때 configtxgen 을 이용해서 genesis.block 생성
   
  elif [ "$CONSENSUS_TYPE" == "kafka" ]; then 
   configtxgen -profile SampleDevModeKafka -channelID byfn-sys-channel -outputBlock ./channel-artifacts/genesis.block
    # 합의 방식이 kafka일 때 configtxgen 을 이용해서 genesis.block 생성
  elif [ "$CONSENSUS_TYPE" == "etcdraft" ]; then
   configtxgen -profile SampleMultiNodeEtcdRaft -channelID byfn-sys-channel -outputBlock ./channel-artifacts/genesis.block
    # 합의 방식이 raft일 때 configtxgen 을 이용해서 genesis.block 생성
  else
   set +x
   echo "unrecognized CONSENSUS_TYPE='$CONSENSUS_TYPE' .exiting"
   exit 1
 fi
 res=$?
 # 마지막으로 실행한 명령 즉 configtxgen 을 잘 수행했는지를 판단
 set+x
 if [ $res -ne 0 ]; then
   echo "Failed to generate orderer genesis block..."
   exit 1
 fi
 
 # Next
 # Generating channel configuration transaction channel.tx
 
 set -x
 configtxgen -profile TwoOrgsChannel -outputCreateChannelTx ./channel-artifacts/channel.tx -channelID $CHANNEL_NAME
 res=$?
 set +x
 if [ $res -ne 0 ]; then
   echo "Failed to generate channel configuration transaction..."
   exit 1
 fi
 
 # Next
 # Generating anchor peer update for Org1MSP
 
 set -x
 configtxgen -profile TwoOrgsChannel -outputAnchorPeersUpdate ./channel-artifacts/Org1MSPanchors.tx -channelID $CHANNEL_NAME -asOrg Org1MSP
 res=$?
 set +x
 if [ $res -ne 0 ]; then
  echo "Failed to generate anchor peer update for Org1MSP..."
  exit 1
 fi
 
 # 계속 같은 패턴 

 다음으로는 
 
 function networkUp() {
 checkPrereqs
  # generate artifacts if they don't exist
 if [ ! -d "crypto-config" ]; then
   generateCerts
   replacePrivateKey
   generateChannelArtifacts
 fi
```

```shell
fucntion checkPrereqs() {
 LOCAL_VERSION=$(configtxlator version | sed -ne 's/ Version: //p')
 DOCKER_IMAGE_VERSION=$(docker run --rm hyperledger/fabric-tools:$IMAGETAG peer version | sed -ne 's/ Version: //p' | head -l)
 
 if [ "$LOCAL_VERSION" != "$DOCKER_IMAGE_VERSION" ]; then
 echo " Local fabric binaries and docker images are out of sync"
 fi
 
 for UNSUPPORTED_VERSION in $BLACKLISTED_VERSIONS; do
  echo "$LOCAL_VERSION" | grep -q $UNSUPPORTED_VERSION
  if [ $? -eq 0 ]; then 
   echo "ERROR! Local Fabric binary version of $LOCAL_VERSION does not match this newer version of BYFN and is unsupported."
   exit 1
  fi
  
  echo "DOCKER_IMAGE_VERSION" | grep -q $UNSUPPORTED_VERSION
  if [ $? -eq 0 ]; then
    echo "ERROR! Fabric Docker image version of $DOCKER_IMAGE_VERSION does not match this newer version of BYFN
    exit 1
  fi
done
}
```

```shell
if [ "${IF_COUCHDB}" == "couchdb" ]; then
    if [ "$CONSENSUS_TYPE" == "kafka" ]; then
      IMAGE_TAG=$IMAGETAG docker-compose -f $COMPOSE_FILE -f $COMPOSE_FILE_KAFKA -f $COMPOSE_FILE_COUCH up -d 2>&1
      docker ps -a
    elif  [ "$CONSENSUS_TYPE" == "etcdraft" ]; then
      IMAGE_TAG=$IMAGETAG docker-compose -f $COMPOSE_FILE -f $COMPOSE_FILE_RAFT2 -f $COMPOSE_FILE_COUCH up -d 2>&1
      docker ps -a
    else
      IMAGE_TAG=$IMAGETAG docker-compose -f $COMPOSE_FILE -f $COMPOSE_FILE_COUCH up -d 2>&1
      docker ps -a
    fi
  else
    if [ "$CONSENSUS_TYPE" == "kafka" ]; then
      IMAGE_TAG=$IMAGETAG docker-compose -f $COMPOSE_FILE -f $COMPOSE_FILE_KAFKA up -d 2>&1
      docker ps -a
    elif  [ "$CONSENSUS_TYPE" == "etcdraft" ]; then
      IMAGE_TAG=$IMAGETAG docker-compose -f $COMPOSE_FILE -f $COMPOSE_FILE_RAFT2 up -d 2>&1
      docker ps -a
    else
      IMAGE_TAG=$IMAGETAG docker-compose -f $COMPOSE_FILE up -d 2>&1
      docker ps -a
    fi
  fi



#compose 파일을 실행시키고 

docker exec cli scripts/script.sh
#cli로 들어가서 스크립트를 실행시킨다.
```

# scripts/script.sh 에 들어가면 자주 보던 문구를 볼 수 있다.

echo
echo " ____    _____      _      ____    _____ "
echo "/ ___|  |_   _|    / \    |  _ \  |_   _|"
echo "\___ \    | |     / _ \   | |_) |   | |  "
echo " ___) |   | |    / ___ \  |  _ <    | |  "
echo "|____/    |_|   /_/   \_\ |_| \_\   |_|  "
echo
echo "Build your first network (BYFN) end-to-end test"
echo


