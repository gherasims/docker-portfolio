# Day 3 — Images Deep Dive

## Objective
Build, tag, inspect, and manage Docker images.  
Understand image metadata, layers, sizes, and apply proper versioning practices.

## What was done
- Created the `day3-images_deep_dive` folder inside the project.
- Wrote a Dockerfile that builds a simple Ubuntu-based image with a custom script.
- Built the image using `docker build`.
- Ran the image using `docker run` to validate behavior.
- Created versioned and registry-style tags.
- Inspected metadata and layer history.
- Checked image sizes and overall Docker disk usage.
- Practiced cleanup operations for unused and dangling images.

## Commands used
docker build -t day3-demo:latest .  
docker run --rm day3-demo:latest  
docker tag day3-demo:latest day3-demo:v1  
docker tag day3-demo:v1 gherast/day3-demo:v1  
docker push gherast/day3-demo:v1  
docker image inspect day3-demo:latest  
docker image inspect day3-demo:latest --format '{{.Config.Cmd}}'  
docker image inspect day3-demo:latest --format '{{.Created}}'  
docker image inspect day3-demo:latest --format '{{.RootFS.Layers}}'  
docker image history day3-demo:latest  
docker image ls day3-demo  
docker system df  
docker image rm day3-demo:v1  
docker image prune -f  

## Files
- `Dockerfile` — builds the custom Ubuntu-based image and embeds the `run.sh` script.

## Errors encountered and fixes
- **Error:** `failed to read dockerfile: open Dockerfile: no such file or directory`  
  **Cause:** No Dockerfile in the current folder.  
  **Fix:** Create a file named exactly `Dockerfile` in the directory where the build is executed.

- **Error:** `exec: ".": executable file not found in $PATH`  
  **Cause:** Running `docker run day3-demo .` with a trailing dot interpreted as a command.  
  **Fix:** Use `docker run --rm day3-demo:latest` without `.`.

- **Error:** `denied: requested access to the resource is denied`  
  **Cause:** Not logged in or tag not matching Docker Hub username.  
  **Fix:** `docker login` and tag as `gherast/...` before pushing.

- **Error:** `failed to solve: failed to compute cache key`  
  **Cause:** Invalid COPY path or missing file in build context.  
  **Fix:** Ensure all referenced files exist inside the build directory.

## Notes
This folder represents the third step in learning Docker: understanding image creation, tagging, inspection, size analysis, and proper cleanup—core skills used in every CI/CD pipeline.

