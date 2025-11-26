# Day 7 — Incident Simulation

## Objective
Design, trigger, diagnose, and fix multiple controlled Docker failures.  
Core skills trained: fault injection, log analysis, inspect-driven debugging, recovery actions, and operational validation.

## What was done
- Created the `day7-incident` folder inside the project.
- Simulated **three independent incidents**:
  - Wrong image tag (broken image reference).
  - Missing environment variables (Postgres fatal start error).
  - Incorrect bind mount (silent functional failure).
- Used `docker ps`, `docker ps -a`, `docker logs`, `docker inspect` to identify the root causes.
- Removed faulty containers and redeployed corrected versions.
- Documented the RCA and technical learnings in `INCIDENT.md`.

---

## Incident 1 — Wrong Image Tag

### Fault Injection
A container was started using a non-existent image tag:

```
docker run -d --name web-bad-tag -p 8087:80 nginx:broken
```

**Why it fails:**  
The tag `broken` does not exist. Docker attempts to pull it and the operation fails.

### Commands used

**List all containers including failures**
```
docker ps -a
```
Shows the container with status `Exited`.

**View logs**
```
docker logs web-bad-tag
```
Error encountered:
- `manifest unknown`
- `pull access denied`
- image not found

**Inspect container**
```
docker inspect web-bad-tag
```
The `State` section confirms the pull failure.

### Fix
```
docker rm web-bad-tag
docker run -d --name web-fixed -p 8087:80 nginx:latest
```

### Result
Container stays in `Up` state and is accessible at:  
`http://localhost:8087`

---

## Incident 2 — Missing Environment Variable

### Fault Injection
A Postgres container was started without the required password:

```
docker run -d --name db-missing-env -p 5433:5432 postgres
```

**Why it fails:**  
Postgres requires `POSTGRES_PASSWORD`. Without it, it terminates immediately.

### Commands used

**List containers**
```
docker ps -a
```

**Check logs**
```
docker logs db-missing-env
```
Error encountered:
- `FATAL: database files are incompatible`
- `no password specified`

**Inspect env values**
```
docker inspect db-missing-env
```
`.Config.Env` contains no password variable.

### Fix
```
docker rm db-missing-env
docker run -d --name db-fixed -p 5433:5432 \
  -e POSTGRES_PASSWORD=12345 \
  -e POSTGRES_USER=admin \
  postgres
```

### Result
Container remains stable in `Up` status.

---

## Incident 3 — Incorrect Bind Mount

### Fault Injection
Nginx launched with an invalid local folder:

```
docker run -d --name web-bad-volume -p 8088:80 \
  -v $(pwd)/htmll:/usr/share/nginx/html \
  nginx
```

**Why it fails:**  
`htmll` does not exist. Docker creates an empty folder and nginx serves an empty directory.

### Commands used

**Open browser**
```
http://localhost:8088
```
Shows default nginx page instead of user file.

**Check logs**
```
docker logs web-bad-volume
```
No critical errors (silent failure expected).

**Inspect mount configuration**
```
docker inspect web-bad-volume
```
`.Mounts.Source` reveals the misspelled path.

### Fix
```
docker rm -f web-bad-volume
docker run -d --name web-fixed-volume -p 8088:80 \
  -v $(pwd)/html:/usr/share/nginx/html \
  nginx
```

### Result
Browser shows the expected file content.

---

## Commands Used (Summary)
```
docker run
docker ps
docker ps -a
docker logs <name>
docker inspect <name>
docker rm <name>
docker rm -f <name>
docker run -d
docker run -p HOST:CONTAINER
docker run -e KEY=VALUE
docker run -v HOST:CONTAINER
```

---

## Errors Encountered
- Tag not found: `manifest unknown`, `pull access denied`
- Postgres fatal start error: `no password specified`
- Silent application failure due to incorrect bind path

---

## Fixes (Short)
- Corrected the image tag.
- Provided mandatory Postgres environment variables.
- Corrected the host directory path for the bind mount.

---

## Notes
This folder demonstrates practical operational debugging using:
- log-driven investigation  
- inspect-driven validation  
- structured RCA  
- corrective deployments  
- functional validation via browser and CLI

This completes the full incident workflow for Docker fundamentals.


