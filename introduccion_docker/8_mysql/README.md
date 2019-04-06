# RUN Container
docker run --name mysql_test -e MYSQL_ROOT_PASSWORD=my-secret-pw -d mysql:5.7
docker exec -it mysql_test /bin/sh
mysql -u root -p

# Attach with explorer
docker run --link mysql_test:db -p 8080:8080 adminer