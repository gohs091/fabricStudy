ㅁㅁ###### $() syntax is token level, so the meaning of the dollar sign depends on the token it's in  the expression $(command) is a modern synonym for `command` which stands for command substitution;  it means, run command and put its output here. so  echo "Today is $(date). A fine day."  will run the date command and include its output in the argument to echo  By contrast, $(variable} is just a disambiguation mechanism, so you can say ${var}text when you mean, the contents of the variable var, followed by text (as opposed to $vartext which means, the contents of the variable vartext ).
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
```shell
echo
echo " ____    _____      _      ____    _____ "
echo "/ ___|  |_   _|    / \    |  _ \  |_   _|"
echo "\___ \    | |     / _ \   | |_) |   | |  "
echo " ___) |   | |    / ___ \  |  _ <    | |  "
echo "|____/    |_|   /_/   \_\ |_| \_\   |_|  "
echo
echo "Build your first network (BYFN) end-to-end test"
echo
```

```shell

## Create channel
echo "Creating channel..."
createChannel

## Join all the peers to the channel
echo "Having all peers join the channel..."
joinChannel

## Set the anchor peers for each org in the channel
echo "Updating anchor peers for org1..."
updateAnchorPeers 0 1
echo "Updating anchor peers for org2..."
updateAnchorPeers 0 2

## Install chaincode on peer0.org1 and peer0.org2
echo "Installing chaincode on peer0.org1..."
installChaincode 0 1
echo "Install chaincode on peer0.org2..."
installChaincode 0 2

# Instantiate chaincode on peer0.org2
echo "Instantiating chaincode on peer0.org2..."
instantiateChaincode 0 2

# Query chaincode on peer0.org1
echo "Querying chaincode on peer0.org1..."
chaincodeQuery 0 1 100

# Invoke chaincode on peer0.org1 and peer0.org2
echo "Sending invoke transaction on peer0.org1 peer0.org2..."
chaincodeInvoke 0 1 0 2

## Install chaincode on peer1.org2
echo "Installing chaincode on peer1.org2..."
installChaincode 1 2

# Query on chaincode on peer1.org2, check if the result is 90
echo "Querying chaincode on peer1.org2..."
chaincodeQuery 1 2 90

```

```shell

#createChannel
peer channel create -o orderer.example.com:7050 -c $CHANNEL_NAME -f ./channel-artifacts/channel.tx >&log.txt
#joinChannel
for org in 1 2; do
  for peer in 0 1; do
    joinChannelWithRetry $peer $org
    
#Util.sh 에 있는 joinChannelWithRetry 

joinChannelWithRetry() {
  PEER=$1
  ORG=$2
  setGlobals $PEER $ORG 
  peer channel join -b $CHANNEL_NAME.block >&log.txt
}

setGlobals() {
  PEER=$1
  ORG=$2
  if [ $ORG -eq 1 ]; then
    CORE_PEER_LOCALMSPID="Org1MSP"
    CORE_PEER_TLS_ROOTCERT_FILE=$PEER0_ORG1_CA
    CORE_PEER_MSPCONFIGPATH=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/users/Admin@org1.example.com/msp
    if [ $PEER -eq 0 ]; then
      CORE_PEER_ADDRESS=peer0.org1.example.com:7051
    else
      CORE_PEER_ADDRESS=peer1.org1.example.com:8051
    fi
  elif [ $ORG -eq 2 ]; then
    CORE_PEER_LOCALMSPID="Org2MSP"
    CORE_PEER_TLS_ROOTCERT_FILE=$PEER0_ORG2_CA
    CORE_PEER_MSPCONFIGPATH=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org2.example.com/users/Admin@org2.example.com/msp
    if [ $PEER -eq 0 ]; then
      CORE_PEER_ADDRESS=peer0.org2.example.com:9051
    else
      CORE_PEER_ADDRESS=peer1.org2.example.com:10051
    fi

  elif [ $ORG -eq 3 ]; then
    CORE_PEER_LOCALMSPID="Org3MSP"
    CORE_PEER_TLS_ROOTCERT_FILE=$PEER0_ORG3_CA
    CORE_PEER_MSPCONFIGPATH=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org3.example.com/users/Admin@org3.example.com/msp
    if [ $PEER -eq 0 ]; then
      CORE_PEER_ADDRESS=peer0.org3.example.com:11051
    else
      CORE_PEER_ADDRESS=peer1.org3.example.com:12051
    fi
  else
    echo "================== ERROR !!! ORG Unknown =================="
  fi

  if [ "$VERBOSE" == "true" ]; then
    env | grep CORE
  fi
}

updateAnchorPeers() {
  PEER=$1
  ORG=$2
  setGlobals $PEER $ORG
  peer channel update -o orderer.example.com:7050 -c $CHANNEL_NAME -f ./channel-artifacts/${CORE_PEER_LOCALMSPID)anchors.tx >&log.txt
  }
  
installChaincode() {
  PEER=$1
  ORG=$2
  setGlobals $PEER $ORG
peer chaincode instlal -n mycc -v ${VERSION} -l ${LANGUAGE} -p ${CC_SRC_PATH} >&log.txt
}

instantiateChanicode() { 
  PEER=$1
  ORG=$2
  setGlobals $PEER $ORG
  peer chaincode instantiate -o orderer.example.com:7050 -C $CHANNEL_NAME -n mycc -l ${LANGUAGE} -v ${VERSION} -c '{"Args":["init","a","100","b","200"]}' -P "AND ('Org1MSP.peer','Org2MSP.peer')" >&log.txt
  }
  
  
 chaincodeQuery() {
 PEER=$1
 ORG=$2
 setGlobals $PEER $ORG
 peer chaincode query -C $CHANNEL_NAME -n mycc -c '{"Args":["query","a"]}' >&log.txt
 }
 
 chaincodeInvoke(){
 
  
 
  
