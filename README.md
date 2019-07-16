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

# Construct NDN topology
docker exec consumer nfdc face create udp://router
docker exec consumer nfdc route add /icn2020 udp://router
docker exec router nfdc face create udp://producer
docker exec router nfdc route add /icn2020 udp://producer
# Launch NDN apps at producer and consumer
docker exec -d producer ndnpingserver icn2020/producer
docker exec consumer ndnpingserver -c 10 icn2020/producer
# Check the ping result...
```