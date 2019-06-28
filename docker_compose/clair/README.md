docker network create scanning

docker run -p 5432:5432 --network scanning -d --name db arminc/clair-db:2019-04-03

docker run -p 6060:6060 --network scanning --link db:postgres -d --name clair arminc/clair-local-scan:v2.0.6


docker run --net=scanning --rm --name=scanner --link=clair:clair -v '/var/run/docker.sock:/var/run/docker.sock'  objectiflibre/clair-scanner --clair="http://clair:6060" --ip="scanner" -t Medium <Image to scan>   