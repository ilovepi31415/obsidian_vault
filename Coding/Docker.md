---
tags:
  - DevOps
---
[Topic 5 Link](https://gitlab.au-computing.org/andrews-university/courses/fall2026-cptr320/topics/topic-05-containers-images-layers-and-registries)

Dockerfile -> Images  -> Containers

## Dockerfiles

A `Dockerfile` is a template for building new images, often running in stages that are separated from each other

### Dockerfile Commands

`FROM` - Picks a base image to add layers on top of. Each stage has its own file system

`COPY <src> <dest>` - Copies files from `src` on the local machine to `dest` on the image
- Using `COPY --from` will use an older stage's files so you don't have to copy the setup

`WORKDIR` - The working directory **inside the image**

`EXPOSE` - Chooses a port the container listens on

`RUN` - Executes a command and saves results to a layer (for installing dependencies)

`ENV` - Sets an environment variable for the image

`CMD` - Chooses a command that runs once a container is opened from the image

### Example Dockerfile

```Dockerfile
# Stage 1
FROM node:22-alpine AS builder
WORKDIR /ui
COPY package.json package-lock.json ./
RUN npm ci
COPY tsconfig.json vite.config.ts index.html ./
COPY src/ ./src/
RUN npm run build

# Stage 2 - 
FROM nginxinc/nginx-unprivileged:1.29-alpine
EXPOSE 8080
COPY  docker/nginx.conf /etc/nginx/conf.d/default.conf
COPY --from=builder ui/dist /usr/share/nginx/html
```

## Docker Commands

`docker build --platform linux/amd64 -t <name> .` - Builds an image from the working directory's Dockerfile
- The name is typically `name:version` or `name:hash`
- `--platform linux/amd64` - Makes it work even if the command is run on Mac

`docker image ls <name?>` - Lists docker images (optional search feature on the names)

`docker ps` - Lists open docker containers

`docker exec` - Run a command **inside the container**

`docker run -d --name ui -p 8080:8080 ui`
- `-d` - Runs detached (it doesn't take up the kernel)
- `--name` - Choose the container to run
- `-p <local>:<container>`- Choose the ports to forward to (ex `8000:8080`)
- `--rm` - Deletes the container once it stops running

`docker tag <src> <dest>` - adds `dest` as a possible name for the container at `src`

### Other Useful Commands

`git rev-parse --short HEAD` - Get the commit ID of HEAD, useful for tagging containers