# docker-nfd
This repository provides simple dockernized NFD with NDN toolsets.
This repository only focuses on testing the NDN connectivity with standard toolsets and uses the ppa-version of NFD/NDN toolsets.
If you prefer customized NDN forwarder, you should modify dockerfile to the source-based installation. 

# Instructions
Build docker image
```
cd build
docker build -t <tag:version> .
```
Run NDN containers (example: linear topology with three nodes)
```
# Construct a docker network
docker network create ndn-network
# Run containers under that network
docker run -it --cap-add=NET_ADMIN --name router   --network ndn-network -d=true <tag:version>
docker run -it --cap-add=NET_ADMIN --name consumer --network ndn-network -d=true <tag:version>
docker run -it --cap-add=NET_ADMIN --name producer --network ndn-network -d=true <tag:version>

# Launch NFD
for node in consumer router producer; do
    docker exec -d ${node} nfd-start
done

# Construct NDN topology
docker exec -d consumer nfdc face create udp://router
docker exec -d consumer nfdc route add /icn2020 udp://router
docker exec -d router nfdc face create udp://producer
docker exec -d router nfdc route add /icn2020 udp://producer

# Launch NDN apps at producer and consumer
docker exec -d producer ndnpingserver icn2020/producer
docker exec consumer ndnping -c 10 icn2020/producer
# Check the ping result...
```

## for JP user
Docker上でNDN(NFD)を試すのにご利用ください.
