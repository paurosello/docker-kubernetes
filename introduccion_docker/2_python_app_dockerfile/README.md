#Construir imagen
docker build . -t python_server

#Ver capas de la imagen
docker history

#Inspeccionar capas de la imagen
dive python_server

#Ejecutar imagen
docker run python_server
docker run -p 5000:5000 python_server