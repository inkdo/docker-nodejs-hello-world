# Docker Hello World (with NodeJS)

Based on <https://nodejs.org/en/docs/guides/nodejs-docker-webapp>

## Demo Setup

- `docker build -t node-web-app .`
- `docker run -d --name nwa --restart unless-stopped -e PORT=80 -e CRASHTEST=true -p 8080:80 node-web-app`
- goto `http://localhost:80`
- `docker ps`, the app will crash after 10 seconds and restart automatically, but also start after reboot.
- If running VS Code get recommended extensions (optional)

## Other Repos/Learnings

- <https://github.com/picode7/minecraft-docker-hetzner>
- <https://github.com/picode7/deno-docker>

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

- Create `docker volume create <volume-name>`
- Run `docker run -v <volume-name>:<folder in image to equal volume> <image>`
- Remove `docker volume rm <volume-name>`
- List volumes `docker volume ls`
- Inspect volume `docker volume inspect <volume-name>`
- Disk location when using Windows+WSL for volumes pointing to `/var/lib/docker/`: `\\wsl$\docker-desktop-data\version-pack-data\community\docker\volumes\` (open in Windows-Explorer)
- Backup volumes <https://docs.docker.com/storage/volumes/#backup-restore-or-migrate-data-volumes>

## Docker Compose

- Create a `docker-compose.yml` and create the config you need
- Start `docker compose up -d` - `-d` for detached like in `docker run`
- Stop and remove `docker compose down`, to also remove volumes add `--volumes`

## Publishing Image

- Using GitHub Container Registry, publishing manually:
  - Following <https://docs.github.com/en/packages/guides/pushing-and-pulling-docker-images> (beta)
  - Create access token and login `echo $CR_PAT | docker login ghcr.io -u USERNAME --password-stdin`
  - Build `docker build -t ghcr.io/<githubname>/<packagename> .`
  - Push latest version `docker push ghcr.io/OWNER/IMAGE_NAME:latest .`
  - Push another version `docker push ghcr.io/OWNER/IMAGE_NAME:1.1 .`, you might to want to push the new version also to the latest tag
  - `docker run ghcr.io/OWNER/IMAGE_NAME` or to only download `docker pull ghcr.io/OWNER/IMAGE_NAME:latest`
  - Image size (not clear if it matches the size you might pay for on GitHub private `docker manifest inspect -v ghcr.io/OWNER/IMAGE_NAME | grep size | awk -F ':' '{sum+=$NF} END {print sum}' | numfmt --to=iec-i` [source](https://stackoverflow.com/a/55156181/4339170) (sums up the manifest you can also see on GitHub)

## GitHub Workflow for GitHub Container Registry

- See .github/workflows/deploy.yml
- Reference the GitHub Repo, add this to the Dockerfile: `LABEL org.opencontainers.image.source https://github.com/OWNER/REPO` or ` --label "org.opencontainers.image.source=https://github.com/OWNER/REPO"` in the `docker build` command
