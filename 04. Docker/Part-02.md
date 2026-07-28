# Docker Tutorial for Beginners: A Comprehensive Guide

## 1. The "Why": Solving the Dependency Problem
At its core, Docker solves the classic "it works on my machine" problem. When software runs on your computer but fails on a server or a co-worker's machine, hidden system dependencies (OS packages, environment configurations, library versions) are usually to blame. 

Docker packages your application along with *all* its dependencies into an isolated, portable unit called a **container**.

### Architecture: Virtual Machines vs. Docker Containers
Containers are fundamentally different from Virtual Machines (VMs). 

```mermaid
graph TD
  subgraph Virtual Machines
    direction TB
    A1[App A] --> B1[Guest OS]
    A2[App B] --> B2[Guest OS]
    B1 --> C[Hypervisor]
    B2 --> C
    C --> D[Host OS]
  end

  subgraph Docker Containers
    direction TB
    E1[App A + Dependencies] --> F[Docker Engine]
    E2[App B + Dependencies] --> F
    F --> D
  end

  classDef vm fill:#f9d0c4,stroke:#333,stroke-width:1px;
  classDef container fill:#d4f1f4,stroke:#333,stroke-width:1px;
  class A1,A2,B1,B2,C vm;
  class E1,E2,F container;
```

| Feature | Virtual Machines (VMs) | Docker Containers |
| :--- | :--- | :--- |
| **Isolation Level** | Emulates entire guest OS & virtual hardware | Shares host OS kernel, isolates processes |
| **Size** | Heavy (Gigabytes) | Lightweight (Megabytes) |
| **Boot Time** | Slow (Minutes) | Near-instant (Under a second) |
| **Performance** | Overhead from hypervisor | Near-native performance |

---

## 2. Core Concepts & CLI Fundamentals

### Images vs. Containers
* **Image:** A read-only template or "recipe" specifying the file system, packages, environment variables, and startup commands.
* **Container:** A running, writable instance of an image. You can launch dozens of identical containers from a single image simultaneously.

```mermaid
graph LR
  Image[(Docker Image<br/>Read-Only Template)] 
  Image -->|docker run| C1[Container 1]
  Image -->|docker run| C2[Container 2]
  Image -->|docker run| C3[Container 3]
```

### Essential CLI Commands
| Command | Flag / Argument | Description |
| :--- | :--- | :--- |
| `docker run` | `-d` | **Detached mode:** Runs the container in the background. |
| `docker run` | `-p 8080:80` | **Port mapping:** Connects host port `8080` to container port `80`. |
| `docker ps` | *(none)* | Lists currently running containers. |
| `docker container prune` | *(none)* | Removes all stopped containers to free up space. |
| `docker exec` | `-it <id> bash` | Opens an interactive shell inside a live container for debugging. |

### Image Tags, Digests & Base Distros
* **Tag Risk:** Tags like `nginx:1.27` are mutable pointers (they can be updated). For production stability, **pin images by SHA-256 digest** (`image@sha256:...`).
* **Base Distro Sizing:** Choosing the right base image drastically reduces attack surface and build time.

| Base Image | Approx. Size | Best Use Case |
| :--- | :--- | :--- |
| `python` | ~1 GB | Local development, when you need all build tools. |
| `python:3.12-slim` | ~100 MB | **Recommended for production.** Debian barebones, good compatibility. |
| `python:3.12-alpine` | ~55 MB | Ultra-minimal environments (note: uses `musl` libc, which can occasionally cause C-extension compatibility issues). |

### Persistent Storage: Volumes vs. Bind Mounts
Container filesystems are ephemeral (they reset on exit). Persistent data requires mounts:

| Type | How it Works | Ideal Use Case |
| :--- | :--- | :--- |
| **Bind Mounts** | Maps a specific host folder (e.g., `./mydata`) directly into the container. | **Local Development:** Code edits update live inside the container. |
| **Volumes** | Managed directly by the Docker daemon (e.g., `my-vol:/data`). | **Production Databases:** Isolates files from host interference and works seamlessly across cloud environments. |

```mermaid
graph TD
  subgraph Host Machine
    HM[Host File System]
    HD[Host Directory ./mydata]
    VD[Docker Volume my-vol]
  end

  subgraph Docker Engine
    C1[Container A]
    C2[Container B]
  end

  HD -.->|Bind Mount: Direct 1-to-1 mapping| C1
  VD -.->|Volume: Managed by Docker, shared safely| C2
  VD -.->|Volume: Can be shared across multiple containers| C1

  classDef host fill:#f9f9f9,stroke:#333,stroke-width:2px;
  classDef docker fill:#e1f5fe,stroke:#01579b,stroke-width:2px;
  class HM,HD,VD host;
  class C1,C2 docker;
```

---

## 3. Custom Dockerfiles & Image Optimization

When built-in registry images aren't enough, you write a **Dockerfile** to create your own.

```dockerfile
FROM python:3.12-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
EXPOSE 8000
CMD ["uvicorn", "main:app", "--host", "0.0.0.0"]
```

### How Layer Caching Works
Every instruction (`FROM`, `RUN`, `COPY`) creates an **immutable layer** (an image diff). Docker caches these layers to speed up subsequent builds.

```mermaid
graph TD
  A[1. FROM python:3.12-slim] -->|Cached| B[2. COPY requirements.txt .]
  B -->|Cached| C[3. RUN pip install ...]
  C -->|Cache Invalidated if source changes| D[4. COPY . .]
  D -->|Invalidated| E[5. CMD uvicorn ...]
  
  style C fill:#d4f1f4,stroke:#333,stroke-width:2px
  style D fill:#f9d0c4,stroke:#333,stroke-width:2px
```

> **Cache Ordering Trick:** Place slowly changing files first (`requirements.txt`) and run `pip install` **BEFORE** copying your frequently changing application source code (`COPY . .`). This way, editing your source code doesn't force Docker to waste time reinstalling all dependencies.

> **Security Note:** Deleting a secret file (like an API key) in a later `RUN` step does **NOT** remove it from earlier layers in the image history. It remains extractable. Use Docker BuildKit secrets or pass them at runtime instead.

### Multi-Stage Builds
To prevent build tools (compilers, wheel build caches) from bloating production images, separate the build step from the runner step:

```mermaid
graph LR
  subgraph Stage 1: Builder
    B1[Base: python:3.12] --> B2[Install gcc, build-essential]
    B2 --> B3[Compile code / Build wheels]
  end

  subgraph Stage 2: Runner
    R1[Base: python:3.12-slim] --> R2[COPY --from=builder /app/dist /app]
    R2 --> R3[Run lightweight app]
  end

  B3 -.->|Copy ONLY artifacts| R2
```

---

## 4. Multi-Container Orchestration (Docker Compose)

Real apps consist of multiple connected services. **Docker Compose** lets you define and manage the entire stack in a single `docker-compose.yml` file.

### Full-Stack Architecture Example
The tutorial builds a production-like To-Do web app with the following interconnected services:

```mermaid
graph TD
  Client((User Browser)) -->|Port 80| Nginx[Nginx Frontend]
  Nginx -->|Port 8000| FastAPI[FastAPI Backend]
  FastAPI -->|Port 27017| MongoDB[(MongoDB Database)]
  MongoExpress[Mongo Express Admin] -->|Port 27017| MongoDB

  MongoDB -.->|Named Volume| Vol[(mongodb_data)]

  classDef service fill:#e1f5fe,stroke:#01579b,stroke-width:2px;
  classDef db fill:#fff3e0,stroke:#e65100,stroke-width:2px;
  class Nginx,FastAPI,MongoExpress service;
  class MongoDB,Vol db;
```

### Security & Environment Secrets
* Never hardcode database credentials in `docker-compose.yml`.
* Pass them securely via external `.env` files.
* Reference the `.env` file in your compose file and **always add `.env` to `.gitignore`** to keep passwords out of source control.

### Pushing to Docker Hub
Once your image is optimized and tested, you can share it with colleagues or deploy it to cloud servers:

```bash
# 1. Tag the image with your Docker Hub username
docker tag myapp:v1 docker.io/yourusername/myapp:v1

# 2. Authenticate (if not already logged in)
docker login

# 3. Push the image to the registry
docker push docker.io/yourusername/myapp:v1
```
