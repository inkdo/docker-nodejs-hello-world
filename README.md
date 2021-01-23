# Docker NodeJS Hello World

<https://nodejs.org/en/docs/guides/nodejs-docker-webapp/>

## Setup

- `npm install`
- Build image `docker build -t node-web-app .`
- List image `docker images`
- Run image `docker run -p 49160:8080 -d node-web-app`
- Get outputs
  - Get container id `docker ps`
  - Print outputs `docker logs <container id>`
- Exec inside container `docker exec -it <container id> /bin/bash`, return with `exit`
- Server exposed outside container at `http://localhost:49160`
- Stop container `docker stop <container id>`
- Stop, rebuild and rerun to update changes
