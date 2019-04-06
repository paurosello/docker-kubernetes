#Construir imagen
docker build . -t nginx_test

#Ver capas de la imagen
docker history

#Inspeccionar capas de la imagen
dive nginx_test

#Ejecutar imagen
docker run nginx_test
docker run -p 8080:80 nginx_test