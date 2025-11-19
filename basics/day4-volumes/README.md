# Day 4 — Volumes & Persistence

## Objective
Understand how Docker handles data persistence using:
- Bind mounts (host folder ↔ container folder)
- Named volumes (Docker-managed storage)

Verify which data survives after containers are stopped and removed, and what is lost when no volume is used.

## What was done
- Created the `basics/day4-volumes` folder inside the project.
- Created a host folder `html-bind` and served it through an nginx container via a bind mount.
- Edited files on the host and saw the changes live in the running container.
- Stopped and removed the nginx container and confirmed that data persisted on the host.
- Created a named volume `demo-data` and wrote data into `/data` using a BusyBox container.
- Started another BusyBox container attached to the same volume and confirmed the data was still there.
- Demonstrated that data written only inside the container filesystem (without a volume) disappears after the container is removed.
- Cleaned up containers and volumes and committed the changes to Git.

## Commands used

1. Setup (run inside basics/day4-volumes)

mkdir html-bind
echo "Bind mount test" > html-bind/index.html

2. Bind mount with nginx

docker run -d --name web-bind -p 8082:80 -v "$(pwd)/html-bind:/usr/share/nginx/html" nginx

3. Validate bind mount

echo "Updated content" > html-bind/index.html

Then open in browser:
http://localhost:8082

4. Stop and remove nginx container and verify host data

docker stop web-bind
docker rm web-bind
cat html-bind/index.html

5. Create a fresh named volume

docker volume rm demo-data 2>/dev/null || true
docker volume create demo-data

6. Write persistent data into the volume (inside /data)

docker run --rm -it -v demo-data:/data busybox sh

Inside BusyBox, run:

echo "Hello volume" > /data/file.txt
ls /data
cat /data/file.txt
exit

7. Verify persistence from a second container

docker run --rm -it -v demo-data:/data busybox sh

Inside BusyBox, run:

ls /data
cat /data/file.txt
exit

8. Demonstrate ephemeral storage (no volume)

docker run --rm -it busybox sh

Inside BusyBox, run:

echo "temp" > /tmp/x.txt
ls /tmp
cat /tmp/x.txt
exit

Start another BusyBox container without volume:

docker run --rm -it busybox sh

Inside BusyBox, run:

ls /tmp
cat /tmp/x.txt   (this should fail: file no longer exists)
exit

9. Cleanup and Git commit

docker stop web-bind 2>/dev/null || true
docker rm web-bind 2>/dev/null || true
docker volume rm demo-data 2>/dev/null || true

cd ~/docker-portfolio
git add basics/day4-volumes
git commit -m "Day 4: Data persistence with volumes"

## Command explanations

mkdir html-bind  
- Creates a folder on the host that will be mounted into the nginx container.

echo "Bind mount test" > html-bind/index.html  
- Creates a simple text/HTML file that nginx will serve.

docker run -d --name web-bind -p 8082:80 -v "$(pwd)/html-bind:/usr/share/nginx/html" nginx  
- Runs an nginx container in the background.
- web-bind is the container name.
- -p 8082:80 maps host port 8082 to container port 80.
- -v "$(pwd)/html-bind:/usr/share/nginx/html" bind-mounts the host folder into nginx’s web root so host files are served by the container.

echo "Updated content" > html-bind/index.html  
- Updates the file on the host. Because of the bind mount, reloading the browser shows the new content immediately.

docker stop web-bind / docker rm web-bind  
- Stops and removes the nginx container. The host folder and its files remain.

cat html-bind/index.html  
- Confirms that the file and its content still exist on the host after the container is gone.

docker volume rm demo-data 2>/dev/null || true  
- Tries to remove the named volume if it exists; errors are ignored so the command can be run safely multiple times.

docker volume create demo-data  
- Creates a Docker-managed named volume called demo-data.

docker run --rm -it -v demo-data:/data busybox sh  
- Runs a temporary BusyBox container with an interactive shell.
- The demo-data volume is mounted at /data inside the container.
- --rm ensures the container is removed automatically when it exits.

echo "Hello volume" > /data/file.txt  
- Writes data into the mounted volume path /data. This is what makes the data persistent.

ls /data and cat /data/file.txt  
- Lists the files in the volume and shows the content of file.txt to confirm it was written.

Running a second container with the same option -v demo-data:/data  
- Attaches another container to the same volume. Seeing file.txt and its content here confirms persistence across containers.

docker run --rm -it busybox sh (without -v)  
- Runs a container that uses only its own internal filesystem (no volume attached).

echo "temp" > /tmp/x.txt  
- Writes data into /tmp inside that container. Once the container exits, this filesystem is destroyed.

In the next BusyBox container, ls /tmp and cat /tmp/x.txt  
- Shows that the file created in the previous container no longer exists, proving that container-only storage is ephemeral.

docker stop / docker rm / docker volume rm  
- Clean-up commands to stop and remove containers and delete the named volume after testing.

git add basics/day4-volumes  
- Stages the new Day 4 folder and its files for commit.

git commit -m "Day 4: Data persistence with volumes"  
- Saves the Day 4 work in the git history with a clear message.

## Errors encountered and solutions

1) Data not present in /data in the second container  
- Cause: file was never written into /data in the first container, or it was written into another path like /tmp.  
- Fix: always write persistent data into the mounted path (/data) using a command like:
  echo "Hello volume" > /data/file.txt

2) Cannot remove volume: "volume is in use"  
- Cause: at least one container is still using demo-data (running or stopped).  
- Fix: stop and remove containers that reference the volume, then run:
  docker volume rm demo-data

3) Docker tries to pull an image named "8082:80"  
- Cause: -p is missing or misplaced, so Docker treats 8082:80 as IMAGE:TAG.  
- Fix: use the correct syntax:
  docker run -d --name web-bind -p 8082:80 -v "$(pwd)/html-bind:/usr/share/nginx/html" nginx

## Files

- basics/day4-volumes/README.md
- basics/day4-volumes/html-bind/index.html

## Notes

- Bind mounts are best when you want to edit files directly on the host and see changes immediately in the container (development workflow).
- Named volumes are best when you want Docker to manage where the data is stored, while keeping it persistent across container recreations.
- Data that lives only inside the container filesystem (without bind mounts or volumes) is temporary and is lost when the container is removed.

