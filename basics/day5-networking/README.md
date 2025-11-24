# Day 5 — Networking

## Objective
Create a user-defined Docker network, attach two containers to it, and validate container-to-container communication using DNS-based name resolution.

## What was done
- Created the `day5-networking` folder inside the project.
- Created a custom Docker bridge network (`day5-net`).
- Launched an Nginx container (`web1`) attached to that network.
- Launched a temporary Alpine container (`client1`) on the same network.
- Validated communication:
  - `ping web1` (ICMP)
  - `curl http://web1` (HTTP request)
- Inspected the network to confirm attached containers.
- Stopped and removed all created resources.

## Commands used
docker network create day5-net  
docker network ls  
docker run -d --name web1 --network day5-net nginx  
docker ps  
docker run --rm -it --name client1 --network day5-net alpine sh  
ping -c 3 web1  
apk update  
apk add curl  
curl http://web1  
exit  
docker network inspect day5-net  
docker stop web1  
docker rm web1  
docker network rm day5-net  

## Errors encountered and quick fixes
- **Error:** `ping: bad address 'web1'`  
  **Cause:** Containers not on the same network or `web1` not running.  
  **Fix:** Ensure both containers use `--network day5-net` and verify with `docker ps`.

- **Error:** `Could not resolve host: web1` during `curl`  
  **Cause:** Missing DNS resolution because `web1` isn’t attached to the network.  
  **Fix:** Restart `web1` with `--network day5-net`.

- **Error:** Network removal fails (`network in use`)  
  **Cause:** Containers still attached.  
  **Fix:** Stop/remove containers first, then remove the network.

## Notes
This folder covers Docker’s name-based service discovery, container isolation, network inspection, and practical troubleshooting for communication failures in user-defined networks.


