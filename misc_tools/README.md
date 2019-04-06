# Start docker daemon
/bin/systemctl start docker.service

# Docker daemon status
/bin/systemctl status docker.service

# Stop all containers
docker stop $(docker ps -a -q)

# Delete all containers
docker rm $(docker ps -a -q)

# Delete all images
docker rmi $(docker images -q)