# Day 1 — Basic Dockerfile and First Image

## Objective
Create a simple Dockerfile, build the image, run a container, intentionally break the build, and fix it.

## What Was Done
- Created the `day1-basic-image` folder.
- Wrote a minimal Dockerfile using Ubuntu.
- Built the image with `docker build`.
- Ran the container using `docker run`.
- Broke the build using an invalid COPY path.
- Fixed the error and rebuilt the image successfully.

## Docker Concepts Covered
- **FROM** — sets the base image.
- **RUN** — executes commands during build.
- **CMD** — default command when the container runs.
- **docker build -t name .** — builds an image from the current folder.
- **docker run -it image** — runs the container in interactive mode.

## Linux Commands Used
- **mkdir -p** — creates nested folders.
- **cd** — change directory.
- **vim Dockerfile** — open a file for editing.
- **ls -l** — list files with details.

## Commands Executed
mkdir -p basics/day1-basic-image  
cd basics/day1-basic-image  
vim Dockerfile  
docker build -t day1-basic-image .  
docker run -it day1-basic-image  

## Errors Encountered (Short + Fix)
- **permission denied on /var/run/docker.sock**  
  **Fix:** Added user to `docker` group → `sudo usermod -aG docker $USER`, logged out/in.
- **COPY failed: file not found**  
  **Fix:** Removed or corrected invalid file path.
- **Git: Author identity unknown**  
  **Fix:** Set global name/email → `git config --global user.name` / `user.email`.
- **GitHub push denied (403)**  
  **Fix:** Generated a Personal Access Token with repo permissions and used it as password.

## Files in This Folder
- `Dockerfile`
- `README.md`

## Notes
This day establishes the basic lifecycle: write → build → run → break → fix → commit.

