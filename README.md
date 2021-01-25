# Docker Hello World (with NodeJS)

Based on <https://nodejs.org/en/docs/guides/nodejs-docker-webapp>

## Demo Setup

- `docker build -t node-web-app .`
- `docker run -d --name nwa --restart unless-stopped -e PORT=80 -e CRASHTEST=true -p 8080:80 node-web-app`
- goto `http://localhost:80`
- `docker ps`, the app will crash after 10 seconds and restart automatically, but also start after reboot.
- If running VS Code get recommended extensions (optional)

## General Usage

- Optional: For local development `npm install`
- Build image `docker build -t node-web-app .`
  - `-t` stands for `--tag` [more](https://docs.docker.com/engine/reference/commandline/build/)
- List image `docker images`
- Run image `docker run -d --name nwa -p 8080:8080 node-web-app`, name is optional, but can be used as ID without having to look it up later. It can only be used once
  - add `-e PORT=8081` or `--env` to overwrite the PORT environment variable from the dockerfile
  - `-d` stands for `--detach` and runs the container in the background
  - `-p` stands for `--publish` to publish the containers ports
  - `--restart unless-stopped` to restart the container on reboot or failure etc., but not manual stop. [more](https://docs.docker.com/config/containers/start-containers-automatically/)
- `docker update <flags> <container id>` to update settings
- Get outputs
  - Get container id `docker ps`
  - Print outputs `docker logs <container id>`
- Exec inside container `docker exec -it <container id> /bin/bash`, return with `exit`
- Server exposed outside container at `http://localhost:8080`
- Stop container `docker stop <container id>`
- Start container `docker start <container id>`
- Remove container `docker rm <container id>`
- Stop, remove, rebuild and rerun to update changes
- Remove _all_ containers, images and cache `docker system prune -a`, confirm with `y`

## Docker Caching and Layers

- For `ADD` or `COPY` docker auto-detects if there has been file changes since the last cached build. If yes it will rebuild from there on, otherwise skip until the next possible change and check again.
- For example `npm install` is not needed to run again, if the `package*.json` files didn't change. So putting `COPY package*.json ./` infront, instead of all the files (`COPY . .`) will allow docker to detect, that nothing relevant for `npm install` changed, even if the source code did.
- If possible it thus makes sense to put the least likely to change commands first
- more [here](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)

## Volumes

- using Windows and wsl volumes can probably be found in `\\wsl$\docker-desktop-data\version-pack-data\community\docker\volumes\` (type in win-explorer)
- `docker volume create <volume-name>`
- `docker run <volume-name>:<folder in image to equal volume> <image>`
