# Docker NodeJS Hello World

Based on <https://nodejs.org/en/docs/guides/nodejs-docker-webapp>

## Demo Setup

- `docker build -t node-web-app .`
- `docker run -d --name nwa --restart unless-stopped -e PORT=80 -e CRASHTEST=true -p 80:80 node-web-app`
- `docker ps`, the app will crash after 10 seconds and restart automatically, but also start after reboot.

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
