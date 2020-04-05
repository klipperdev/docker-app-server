Documentation
=============

This document contains information on how to build and upload the docker image.

## 1) Installing the tools

- Install [Git](https://git-scm.com)
- Install [Amazon AWS CLI](https://aws.amazon.com/cli)
- Install [Docker Toolbox](https://www.docker.com/products/docker-toolbox)
- If you want to move the image of virtual machine:
  - Add the environment variable `MACHINE_STORAGE_PATH` with value `D:\Docker\machine` for example
- If your workspace isn't in the path `C:\Users`:
  - Launch the Docker Quickstart Terminal for the first initialization
  - Close the terminal and shutdown the VM in Virtualbox
  - In Virtualbox, add the shared folder:
    - `D:\Www` to `d/Wwww` with the automatic mounting
  - Close Virtualbox

## 2) Build the docker image

- Clone the repository `git@github.com:klipperdev/docker-app-server.git` with Git
- Launch the Docker Quickstart Terminal
- Move the working directory in the folder of this repository (ex. `cd /d/Git/docker/klipper/docker-app-server`)
- Create the docker image __*__:
  - `docker build -t <ecr-repository> .`
- Get the docker login for the Amazon AWS ECR __*__:
  - `aws ecr get-login --no-include-email --region <ecr-region>`
- Run the docker login command that was returned in the previous step __*__
- Tag the docker image __*__:
  - `docker tag <ecr-repository>:latest <ecr-repository-uri>:latest`
- Push the docker image in the Amazon AWS ECR __*__:
  - `docker push <ecr-repository-uri>:latest`

> **Notes:**
> - __*__ All commands are listed in the selected ECR repository of the Amazon AWS Console (button `Display push commands`)
> - `<ecr-region>` must be replaced by the name of the selected region of ECR repository (defined in the URI of the ECR repository)
> - `<ecr-repository>` must be replaced by the name of the selected ECR repository
> - `<ecr-repository-uri>` must be replaced by the URI of the selected ECR repository

## 3) Use the docker image in Docker Compose

Use the image directly in the docker compose file:

```yaml
# docker-compose.yml
version: '3.2'
services:
    app:
      container_name: app
      working_dir: /var/www/html
      image: <ecr-repository-uri>:latest
      volumes:
        - .:/var/www/html
```

Or with the Dockerfile:

```yaml
# ./docker-compose.yml
version: '3.2'
services:
    app:
      container_name: app
      working_dir: /var/www/html
      build:
        context: .
        dockerfile: docker/app/Dockerfile
      volumes:
        - .:/var/www/html
```

```
# ./docker/back/Dockerfile
FROM <ecr-repository-uri>:latest

# Your dockerfile actions
```

> **Notes:**
> - `<ecr-repository-uri>` must be replaced by the URI of the selected ECR repository
