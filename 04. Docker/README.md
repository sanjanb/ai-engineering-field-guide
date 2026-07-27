# Technical Overview: Docker Crash Course for Beginners

This technical breakdown covers the concepts and practical workflows demonstrated in **NeuralNine's** . It details how to package, persist, containerize, and orchestrate applications using Docker and Docker Compose.

---

## 1. Core Concepts & Architecture

Docker isolates applications and their dependencies into standard units called **containers**. Unlike Traditional Virtual Machines (VMs), containers share the host operating system's kernel, making them significantly faster, lighter, and lower on resource overhead.

| Feature | Virtual Machines (VMs) | Docker Containers |
| --- | --- | --- |
| **Isolation Level** | Hardware-level virtualisation | OS-level virtualization |
| **Operating System** | Includes full Guest OS per VM | Shares host OS kernel |
| **Startup Time** | Minutes | Seconds |
| **Size** | Tens of Gigabytes (GBs) | Tens to hundreds of Megabytes (MBs) |
| **Hypervisor/Engine** | Hypervisor (e.g., VirtualBox, ESXi) | Docker Container Engine |

### Terminology Hierarchy

* **Registry (Docker Hub):** Public or private repository for storing and sharing container images.
* **Image:** A read-only template/recipe containing source code, runtime, libraries, environment variables, and config files. Built in immutable layers.
* **Container:** A runnable, isolated instance of a Docker image.
* **Volume:** A dedicated file/directory storage abstraction managed by Docker outside the container layer to ensure data persistence.

---

## 2. Essential CLI Commands

### Image & Container Operations

```bash
# Pull an image from Docker Hub
docker pull python:3.12-slim

# List local images
docker image ls

# Run a container (interactive/ephemeral run with auto-removal on exit)
docker run --rm python:3.12-slim python -c "print('Hello World')"

# Run container in detached mode (background)
docker run -d --name my-busybox busybox sleep 3600

# Inspect active/stopped containers
docker ps         # Running containers only
docker ps -a      # All containers (including exited)

# View logs & stop/cleanup
docker logs <container_id_or_name> [00:17:17]
docker stop <container_id_or_name> [00:17:43]
docker container prune              # Remove all stopped containers [00:18:08]

```

### Interactive Shell Access

To open an interactive shell inside a running container:

```bash
docker exec -it <container_id> /bin/sh

```

---

## 3. Data Persistence: Volumes vs. Bind Mounts

By default, files created inside a container are destroyed when the container is deleted. Docker provides two primary ways to persist data across container lifecycles:

### Option A: Managed Docker Volumes (Recommended for Production)

Docker manages where the data is stored on the host filesystem, isolated from host OS modifications.

```bash
# 1. Create a named volume
docker volume create my_data_vol

# 2. Mount volume to a container path
docker run -d -v my_data_vol:/data busybox sleep 3600

```

### Option B: Bind Mounts (Useful for Local Development)

Maps a specific host directory directly to a container path.

```bash
docker run -d -v ./local_dir:/app/data busybox sleep 3600

```

---

## 4. Containerizing a Single Application (`Dockerfile`)

A `Dockerfile` contains sequential instructions used to build a custom Docker image.

### Example: Flask Web Application

```dockerfile
# 1. Specify Base Image (Layer 0)
FROM python:3.12-slim

# 2. Set Working Directory inside the container
WORKDIR /app

# 3. Copy and install dependencies first (Optimizes layer caching)
COPY requirements.txt .
RUN pip3 install --no-cache-dir -r requirements.txt

# 4. Copy application source code
COPY . .

# 5. Metadata indicating target container port
EXPOSE 5000

# 6. Default Environment Variables
ENV DEBUG=false \
    PORT=5000 \
    LOG_FILE=/data/logs/app.log

# 7. Execution command when container starts
CMD ["python3", "app.py"]

```

### Build & Run Commands

```bash
# Build the image from current directory
docker build -t flask-app:v1 .

# Run container with Port Mapping (-p host_port:container_port)
docker run -d -p 5000:5000 --name running-flask flask-app:v1

```

---

## 5. Multi-Container Orchestration (`docker-compose.yaml`)

When building distributed systems (e.g., Frontend + Backend + Database), `docker-compose` allows defining and running multi-container applications with shared networking and storage.

### Example Architecture: React + FastAPI + PostgreSQL
```yaml
version: '3.8'

services:
  # 1. Database Service (Postgres Image from Docker Hub)
  db:
    image: postgres:14
    environment:
      POSTGRES_DB: shopping
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: postgresspassword
    volumes:
      - pgdata:/var/lib/postgresql/data

  # 2. Backend Service (FastAPI)
  backend:
    build: ./backend
    image: myuser/shopping-backend:latest
    ports:
      - "8000:8000"
    depends_on:
      - db
    volumes:
      - logs:/app/logs

  # 3. Frontend Service (React / Nginx)
  frontend:
    build: ./frontend
    image: myuser/shopping-frontend:latest
    ports:
      - "80:80"
    depends_on:
      - backend

# Persistent Named Volumes Definition
volumes:
  pgdata:
  logs:

```

### Orchestration Commands

```bash
# Build & start all services in foreground
docker compose up

# Start services in detached mode
docker compose up -d

# Stop and remove containers, networks, and ephemeral volumes
docker compose down

# Build images and push to Docker Hub registry [00:47:28]
docker compose build
docker login
docker compose push

```
