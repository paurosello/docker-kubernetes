# Crear contenedor docker torrent
docker create \
  --name=transmission \
  -e PUID=1000 \
  -e PGID=1000 \
  -e TZ=Europe/London \
  -p 9091:9091 \
  -p 51413:51413 \
  -p 51413:51413/udp \
  --mount type=bind,source="$(pwd)"/downloads,target=/downloads \
  --mount type=bind,source="$(pwd)"/watch,target=/watch \
  --restart unless-stopped \
  linuxserver/transmission

# Iniciar torrent
docker start transmission
http://localhost:9091


# Crear contenedor lidarr
docker create \
  --name=lidarr \
  -e PUID=1000 \
  -e PGID=1000 \
  -e TZ=Europe/London \
  -p 8686:8686 \
  --mount type=bind,source="$(pwd)"/watch,target=/music \
  --mount type=bind,source="$(pwd)"/downloads,target=/downloads \
  --restart unless-stopped \
  linuxserver/lidarr

# Iniciar sonarr
docker start lidarr
http://localhost:8686


# Crear contenedor airsonic