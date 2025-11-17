# Day 2 — Container Lifecycle

## Objective
Run containers with port mapping, volume mounting, and environment variables.  
Simulate a container failure, inspect it with `docker logs`, fix it, and validate the workflow.

## What was done
- Created the `day2-containers` folder inside the project.
- Ran an Nginx container with port mapping using `-p`.
- Mounted a local folder into the container using `-v`.
- Passed environment variables using `-e`.
- Triggered a controlled container failure using an invalid command.
- Inspected the failure using `docker logs`.
- Removed the broken container and ran a fixed version successfully.

## Commands used
docker run -d --name web1 -p 8080:80 nginx  
docker run -d --name web2 -p 8081:80 -v "$(pwd)/html:/usr/share/nginx/html" nginx  
docker run --rm -e MY_VAR="Day2Rocks" alpine sh -c 'echo $MY_VAR'  
docker run --name broken-nginx nginx /bin/does-not-exist  
docker logs broken-nginx  
docker rm broken-nginx  
docker run -d --name fixed-nginx -p 8082:80 nginx  

## Files
- `html/index.html` — custom HTML file used for volume mounting.

## Errors encountered and fixes
- **Error:** `docker: invalid containerPort: 80-v`  
  **Cause:** Missing space before the line break and wrong path in the `docker run` command:  
  `-p 8081:80\` and `/user/share/nginx/html`.  
  **Fix:** Add a space before the backslash and correct the path:  
  `-p 8081:80 \` and `/usr/share/nginx/html`.

- **Error:** `The command 'docker' could not be found in this WSL 2 distro.`  
  **Cause:** Docker CLI inside WSL was pointing to the stub script instead of the real Docker Desktop CLI.  
  **Fix:** Enable WSL integration in Docker Desktop and prepend the real CLI path in `~/.bashrc`:  
  `export PATH="/mnt/wsl/docker-desktop/cli-tools/usr/bin:$PATH"`.

## Notes
This folder represents the second step in learning Docker: running containers with different runtime options (ports, volumes, environment variables) and performing basic debugging using `docker logs` on failing containers.

