#Construir imagen
docker build . -t node_server

#Ver capas de la imagen
docker history

#Inspeccionar capas de la imagen
dive node_server

#Ejecutar imagen
docker run node_server
docker run -p 8080:8080 node_server