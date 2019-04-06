docker build --tag default_apache .

docker-compose up
docker-compose scale web=3
