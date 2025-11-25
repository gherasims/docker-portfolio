# Day 6 — Docker Compose

## Objective
Build a Web + Database stack using `docker compose`, validate service orchestration, break one service intentionally, fix it, and ensure normal operation.

## What Was Done
- Created the folder `basics/day6-compose/`
- Built a `docker-compose.yml` with two services:  
  - `web` using `nginx:alpine`  
  - `db` using `postgres:16-alpine`
- Exposed the web container on port `8080`
- Attached a persistent named volume (`db_data`) to Postgres
- Launched both services with `docker compose up -d`
- Verified container status with `docker compose ps`
- Inspected the Compose-generated network
- Inspected service logs (`docker compose logs web` / `docker compose logs db`)
- Removed the database volume to simulate fresh initialization
- Broke the database configuration (removed `POSTGRES_PASSWORD`)
- Recreated the stack and validated expected failure
- Fixed configuration, recreated the stack, confirmed both containers running

## Commands Used
# Folder setup
mkdir -p basics/day6-compose
cd basics/day6-compose

# Run stack
docker compose up -d

# Stop and remove stack (keep volume)
docker compose down

# Stop and remove stack + remove volume
docker compose down -v

# Check container status
docker compose ps

# Check logs
docker compose logs web
docker compose logs db

# Inspect network
docker network ls | grep day6

# Inspect container details
docker inspect day6-db

# Volume operations
docker volume ls
docker volume rm day6-compose_db_data


## Key Files
- `docker-compose.yml`  
Defines both services, environment variables, ports, and volumes.

## Errors Encountered and Solutions
**Error:** Postgres did not fail when removing `POSTGRES_PASSWORD`.  
**Cause:** The volume already contained initialized database data.  
**Fix:** Remove the named volume:
docker compose down
docker volume rm day6-compose_db_data
docker compose up -d

**Error:** Compose complained about `additional properties 'volume' not allowed`.  
**Cause:** Incorrect YAML root key (`volume:` instead of `volumes:`).  
**Fix:** Correct the key:
```yaml
volumes:
  db_data:

## Git Commands
cd ~/docker-portfolio
git status
git add basics/day6-compose/docker-compose.yml
git commit -m "Day 6: Web + DB stack with Compose"
git push


# Day 6 – Test Questions

## 1.
In one sentence: what problem does `docker compose` solve compared to running `docker run` manually?

## 2.
What is the default file name Compose loads?
- a) Dockerfile  
- b) docker-compose.yml  
- c) compose.yml  
- d) both b and c  

## 3.
Which command starts all services in detached mode?
- a) docker compose start  
- b) docker compose run -d  
- c) docker compose up -d  
- d) docker up -d  

## 4.
Write the image used for each service:
- web → ?  
- db  → ?

## 5.
Write the full URL used to access Nginx from the host.

## 6.
What does `depends_on` do?
- a) Restart db if web crashes  
- b) Ensure db starts before web  
- c) Make web expose db ports  
- d) Share environment variables  

## 7.
What is the name of the volume used for the database?

## 8.
Why do we declare `volumes: db_data:` at the root?
- a) Expose volume to internet  
- b) Declare named volume  
- c) Mount host folder  
- d) Increase memory  

## 9.
Which exact environment variable must be removed to break Postgres initialization?

## 10.
Why doesn’t changing env vars break an already-initialized Postgres volume?

## 11.
Write the commands to:
- a) list all volumes  
- b) remove the `day6-compose_db_data` volume  

## 12.
Explain what this does:
docker network ls | grep day6
```

