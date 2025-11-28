# CRUD-DD Task MEAN App — Containerized & Deployed

## Overview
This repository contains a MEAN stack application (MongoDB, Express, Angular, Node.js) with Docker, Docker Compose, and a CI/CD pipeline using GitHub Actions. The app is deployed to an Ubuntu VM and exposed via Nginx on port 80.

## Repo structure
- `frontend/` — Angular project
- `backend/` — Node/Express backend
- `docker-compose.yml` — Compose file to run services (mongo, backend, frontend, nginx)
- `backend/Dockerfile` — Backend image
- `frontend/Dockerfile` — Frontend image (build + serve via nginx)
- `docker/nginx/nginx.conf` — Nginx reverse-proxy configuration
- `.github/workflows/ci-cd.yml` — GitHub Actions CI/CD pipeline

## Prerequisites
- Docker Hub account
- Ubuntu VM with a public IP (AWS/Azure/GCP or similar)
- SSH access from CI (add ssh private key as GitHub secret)
- GitHub repository and secrets configured (see below)

## GitHub Secrets
Set these repository secrets:
- `DOCKERHUB_USERNAME`
- `DOCKERHUB_TOKEN`
- `VM_HOST` — VM public IP
- `VM_USER` — SSH username (e.g., ubuntu)
- `VM_SSH_PORT` — SSH port (default 22)
- `SSH_PRIVATE_KEY` — private key (no passphrase recommended)
- `VM_DEPLOY_DIR` — e.g., `/home/ubuntu/app`

## Deploy steps (summary)
1. Push code to `main`.
2. Workflow builds and pushes images to Docker Hub.
3. Action SSHs into VM, copies `docker-compose.yml` and nginx conf, then runs:
   ```bash
   docker-compose pull
   docker-compose up -d --remove-orphans

---

- GitHub Actions run page showing workflow steps: build & push, deploy SSH step.
- Docker Hub page showing your repo and tags pushed (`crud-dd-frontend`, `crud-dd-backend`).
- VM terminal (or SSH session screenshot) showing:
  - `docker-compose pull`
  - `docker-compose up -d`
  - `docker ps` with containers: `crud-frontend`, `crud-backend`, `crud-nginx`, `mongo`.
- Browser screenshot of app UI (visit `http://<VM_IP>/`).
- Nginx config / docker-compose.yml file open in the VM.
- Optional: logs `docker-compose logs -f`.

---

# Quick troubleshooting tips

- If backend fails to connect to MongoDB, check `MONGO_URI` env var and whether `mongo` service is healthy.
- If frontend shows blank page:
  - Ensure Angular build produced files in `dist/`.
  - Confirm frontend image serves files (open container logs).
  - Confirm Nginx proxies to frontend container correctly.
- If CI fails to SSH to VM: ensure `SSH_PRIVATE_KEY` secret matches authorized key on VM and username/host are correct.

---

# Final notes & next steps

1. Copy and paste the Dockerfiles, `docker-compose.yml`, `nginx.conf`, and GitHub Actions file into your project.
2. Adjust any path or port differences (I left placeholders marked `selvamecsoft`, `dd_db`, etc.). Replace them with real values.
3. Add GitHub secrets and push to `main`. The workflow will run and deploy automatically.

