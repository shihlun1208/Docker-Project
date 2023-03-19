# Docker-Cheatsheet
First of all, thanks to these great cheat sheets. 
* [`wsargent/docker-cheat-sheet`](https://github.com/wsargent/docker-cheat-sheet)
* [`drminnaar/cheatsheets`](https://github.com/drminnaar/cheatsheets)
## Dockerfile
[The configuration file](https://docs.docker.com/engine/reference/builder/). Sets up a Docker container when you run `docker build` on it. Vastly preferable to `docker commit`.  

### Instructions

* [.dockerignore](https://docs.docker.com/engine/reference/builder/#dockerignore-file)
* [FROM](https://docs.docker.com/engine/reference/builder/#from) Sets the Base Image for subsequent instructions.
* [MAINTAINER (deprecated - use LABEL instead)](https://docs.docker.com/engine/reference/builder/#maintainer-deprecated) Set the Author field of the generated images.
* [RUN](https://docs.docker.com/engine/reference/builder/#run) execute any commands in a new layer on top of the current image and commit the results.
* [CMD](https://docs.docker.com/engine/reference/builder/#cmd) provide defaults for an executing container.
* [EXPOSE](https://docs.docker.com/engine/reference/builder/#expose) informs Docker that the container listens on the specified network ports at runtime.  NOTE: does not actually make ports accessible.
* [ENV](https://docs.docker.com/engine/reference/builder/#env) sets environment variable.
* [ADD](https://docs.docker.com/engine/reference/builder/#add) copies new files, directories or remote file to container.  Invalidates caches. Avoid `ADD` and use `COPY` instead.
* [COPY](https://docs.docker.com/engine/reference/builder/#copy) copies new files or directories to container.  By default this copies as root regardless of the USER/WORKDIR settings.  Use `--chown=<user>:<group>` to give ownership to another user/group.  (Same for `ADD`.)
* [ENTRYPOINT](https://docs.docker.com/engine/reference/builder/#entrypoint) configures a container that will run as an executable.
* [VOLUME](https://docs.docker.com/engine/reference/builder/#volume) creates a mount point for externally mounted volumes or other containers.
* [USER](https://docs.docker.com/engine/reference/builder/#user) sets the user name for following RUN / CMD / ENTRYPOINT commands.
* [WORKDIR](https://docs.docker.com/engine/reference/builder/#workdir) sets the working directory.
* [ARG](https://docs.docker.com/engine/reference/builder/#arg) defines a build-time variable.
* [ONBUILD](https://docs.docker.com/engine/reference/builder/#onbuild) adds a trigger instruction when the image is used as the base for another build.
* [STOPSIGNAL](https://docs.docker.com/engine/reference/builder/#stopsignal) sets the system call signal that will be sent to the container to exit.
* [LABEL](https://docs.docker.com/config/labels-custom-metadata/) apply key/value metadata to your images, containers, or daemons.
* [SHELL](https://docs.docker.com/engine/reference/builder/#shell) override default shell is used by docker to run commands.
* [HEALTHCHECK](https://docs.docker.com/engine/reference/builder/#healthcheck) tells docker how to test a container to check that it is still working.

## Docker Commands

### Docker info
Command | Description
--- | ---
docker version | [provides full description of docker version](https://docs.docker.com/engine/reference/commandline/version/)
docker -v | provides a short description of docker version
docker info | [display system wide information](https://docs.docker.com/engine/reference/commandline/info/)
docker info --format '{{.DriverStatus}}' | display 'DriverStatus' fragment from docker information
docker info --format '{{json .DriverStatus}}' | display 'DriverStatus' fragment from docker information in JSON format
docker version --format '{{.Server.Version}}' | display the server version

### [Images](https://docs.docker.com/engine/reference/commandline/images/)
Command | Description
--- | ---
docker images | shows all images
docker rmi $(docker images -q) | remove all images
docker rmi -f [image-name] | force remove an image by name
docker image rm [image-name] | remove image by name

### [Start a container](https://docs.docker.com/engine/reference/builder/#run)
Command | Description
--- | ---
docker run --name [container-name] [image-name]    |  Specific container name
docker run --name [container-name] -d [image-name] |  Specific container name, return container ID
docker run --name [container-name] -it [image-name]|  Specific container name, access container shell
winpty docker run -it {Image name}                 |  (Git Bash)
docker container run --rm [image-name]             |  Run container based on specified imaged and immediately remove it once it stops
docker run -it -v [volume-name]:[container-mount-path] [image-name] | Run container and bind mount volume

### [Containers](https://docs.docker.com/engine/reference/commandline/ps/)
Command | Description
--- | ---
docker ps -a                                          | List all containers
docker ps --filter status=running                     | Check container status which is running
docker stop $(docker ps --filter status=running -q)   | Stop container which is running
docker stop $(docker ps -aq)                          | Stop all running containers
docker rm $(docker ps -aq)                            | Remove all containers
docker rm [container-ID]                              | Remove container by ID
docker rm [container-name]                            | Remove container by container name
docker exec -u 0 -it [container-ID] /bin/bash         | Run a existing container, access container shell with user root

### [Volumes](https://docs.docker.com/engine/reference/commandline/volume_create/)
Command | Description
--- | ---
docker volume create [volume-name]   | Create volume
docker volume inspect [volume-name]  | Display detailed information on one or more volumes
docker volume rm [volume-name]       | Remove one or more volumes