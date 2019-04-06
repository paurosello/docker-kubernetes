#Construir imagen
docker build . -t volumes_test

#Crear Volumen
docker volume create --name test-vol

#Ejecutar imagen
docker run -it --name test-vol-container -v /data volumes_test /bin/bash
docker inspect -f "{{json .Mounts}}" test-vol-container | jq .
screen ~/Library/Containers/com.docker.docker/Data/com.docker.driver.amd64-linux/tty 

#Bind mount
docker run -v /tmp:/data volumes_test ls /data