# Day 2 — Container Lifecycle

Commands practiced:

- `docker run -d --name NAME -p HOST:CONTAINER IMAGE`  
  - `-d`: run in background  
  - `-p`: map host port to container port  

- `docker run -v HOST_DIR:CONTAINER_DIR IMAGE`  
  - `-v`: bind-mount local directory into container  

- `docker run -e NAME=VALUE IMAGE`  
  - `-e`: set environment variables inside container  

- `docker logs CONTAINER`
  - inspect container stdout/stderr for debugging  

Exercise:
- Started a container with an invalid command so it failed.
- Used `docker logs` to see the error.
- Re-ran the container with a correct command and port mapping.
:
