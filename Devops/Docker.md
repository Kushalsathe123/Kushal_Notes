# Docker — Detailed Study Notes

---

## 1. What is Docker?

Docker is a platform that packages your application and all its dependencies into a **container** — a lightweight, isolated, portable unit that runs consistently across any environment (local machine, staging, production, cloud).

**The core problem it solves:** "It works on my machine" — Docker eliminates environment inconsistencies by bundling the app with everything it needs.

**Container vs VM:**

| | Container | Virtual Machine |
|---|---|---|
| OS | Shares host OS kernel | Has its own OS |
| Size | MBs | GBs |
| Startup | Seconds | Minutes |
| Isolation | Process-level | Full hardware |
| Use case | Microservices, apps | Full OS isolation |

---

## 2. Core Concepts

| Concept | Description |
|---|---|
| **Image** | Read-only blueprint/template for a container. Built from a Dockerfile. |
| **Container** | Running instance of an image. Ephemeral by default — data lost on stop unless using volumes. |
| **Dockerfile** | Script of instructions to build an image layer by layer. |
| **Docker Hub** | Public registry to store and pull images (like GitHub for images). |
| **Volume** | Persistent storage mounted into a container — survives container restarts. |
| **Network** | Communication channel between containers or between container and host. |
| **Registry** | Storage for Docker images (Docker Hub, Azure Container Registry, ECR). |
| **Layer** | Each Dockerfile instruction creates a read-only layer. Layers are cached and reused. |

---

## 3. Dockerfile — Deep Dive

A Dockerfile is a sequence of instructions. Docker builds each instruction as a separate **layer**, cached for reuse.

### Full Dockerfile Reference

```dockerfile
# Base image — always start here
FROM ubuntu:22.04

# Set a label (metadata)
LABEL maintainer="kushal@example.com"

# Set environment variables
ENV APP_ENV=production \
    PORT=80

# Set working directory inside the container
WORKDIR /app

# Copy dependency files first (layer cache optimization)
COPY package.json package-lock.json ./

# Run commands during build (installs dependencies)
RUN npm install --production

# Copy rest of source code
COPY . .

# Expose port (documentation only — does NOT publish the port)
EXPOSE 80

# Default command to run when container starts
CMD ["node", "server.js"]
```

### `CMD` vs `ENTRYPOINT` — Key Difference

This is a common interview question.

| | `CMD` | `ENTRYPOINT` |
|---|---|---|
| Purpose | Default command — can be overridden | Fixed command — always runs |
| Override | `docker run myapp node other.js` replaces CMD | `docker run myapp arg1` appends to ENTRYPOINT |
| Use case | Flexible defaults | Executables that must always run |

**Combined pattern (best practice):**

```dockerfile
ENTRYPOINT ["dotnet"]   # always runs dotnet
CMD ["MyApp.dll"]       # default arg — can be overridden
```

Running `docker run myapp OtherApp.dll` would execute `dotnet OtherApp.dll`.

### Shell form vs Exec form

```dockerfile
# Shell form — runs via /bin/sh -c (supports variable expansion)
CMD node server.js

# Exec form — runs directly, no shell (preferred — signals work correctly)
CMD ["node", "server.js"]
```

Always prefer **exec form** for `CMD` and `ENTRYPOINT` — it handles OS signals (SIGTERM) properly during `docker stop`.

---

## 4. Multi-Stage Builds

Multi-stage builds produce smaller, production-ready images by separating build-time tools from runtime.

### Why it matters
Without multi-stage: your image includes compilers, SDKs, dev tools → bloated image.  
With multi-stage: only the compiled output is copied into a minimal runtime image.

### ASP.NET Core Example

```dockerfile
# Stage 1 — Build
FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
WORKDIR /app
COPY *.csproj ./
RUN dotnet restore
COPY . ./
RUN dotnet publish -c Release -o out

# Stage 2 — Runtime (only this goes to production)
FROM mcr.microsoft.com/dotnet/aspnet:8.0
WORKDIR /app
COPY --from=build /app/out .
EXPOSE 80
ENTRYPOINT ["dotnet", "MyApp.dll"]
```

The final image only contains the ASP.NET runtime + your compiled DLL — no SDK, no source code.

**Result:** Image goes from ~800MB (SDK) → ~200MB (runtime only).

### Node.js Multi-Stage Example

```dockerfile
FROM node:20 AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

FROM node:20-alpine AS production
WORKDIR /app
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/node_modules ./node_modules
EXPOSE 3000
CMD ["node", "dist/index.js"]
```

---

## 5. Key Docker Commands

### Image Management

```bash
docker build -t myapp:v1 .              # Build image from Dockerfile in current dir
docker build -t myapp:v1 -f MyDockerfile .  # Use specific Dockerfile
docker images                           # List all local images
docker pull nginx:latest                # Pull image from Docker Hub
docker push myrepo/myapp:v1             # Push image to registry
docker rmi myapp:v1                     # Remove image
docker image prune                      # Remove unused images
```

### Container Lifecycle

```bash
docker run myapp:v1                     # Run container (foreground)
docker run -d myapp:v1                  # Run in background (detached)
docker run -d -p 8080:80 myapp:v1       # Map host port 8080 → container port 80
docker run -d --name mycontainer myapp  # Give container a name
docker run --rm myapp:v1                # Auto-remove container when it exits
docker run -e DB_HOST=localhost myapp   # Pass environment variable
docker run -v /host/path:/container/path myapp  # Mount volume

docker ps                               # List running containers
docker ps -a                            # List all containers (including stopped)
docker stop mycontainer                 # Graceful stop (SIGTERM → waits → SIGKILL)
docker kill mycontainer                 # Immediate stop (SIGKILL)
docker start mycontainer                # Start a stopped container
docker restart mycontainer              # Restart container
docker rm mycontainer                   # Remove stopped container
docker rm -f mycontainer                # Force remove running container
```

### Debugging

```bash
docker logs mycontainer                 # View container logs
docker logs -f mycontainer              # Stream logs (follow)
docker logs --tail 100 mycontainer      # Last 100 lines
docker exec -it mycontainer /bin/bash   # Interactive shell inside container
docker exec mycontainer ls /app         # Run one-off command
docker inspect mycontainer              # Full JSON metadata
docker stats                            # Live resource usage (CPU, memory)
docker top mycontainer                  # Running processes inside container
docker cp mycontainer:/app/log.txt .    # Copy file from container to host
```

---

## 6. Volumes — Persistent Storage

By default containers are **stateless** — all data is lost when the container stops. Volumes solve this.

### Types of Storage

| Type | Command | Use Case |
|---|---|---|
| **Named Volume** | `-v mydata:/app/data` | Managed by Docker, best for databases |
| **Bind Mount** | `-v /host/path:/container/path` | Local dev — sync code changes live |
| **tmpfs Mount** | `--tmpfs /tmp` | In-memory, not persisted |

### Named Volume (production)

```bash
docker volume create mydata
docker run -d -v mydata:/var/lib/mysql mysql:8
docker volume ls
docker volume inspect mydata
docker volume rm mydata
```

### Bind Mount (development)

```bash
# Sync local ./src with /app/src inside container
docker run -d -v $(pwd)/src:/app/src -p 3000:3000 myapp:dev
```

Changes to local files are immediately reflected inside the container — no rebuild needed.

### Bind Mount vs Named Volume

| | Bind Mount | Named Volume |
|---|---|---|
| Location | Host filesystem path you control | Docker-managed location |
| Use case | Local development, config files | Database data, production persistence |
| Portability | Host path specific | Portable across environments |
| Performance | Slightly slower on Mac | Better I/O performance |

---

## 7. Networking

Docker containers are isolated — they need explicit network configuration to communicate.

### Network Modes

| Mode | Flag | Description |
|---|---|---|
| **bridge** | `--network bridge` | Default. Containers on same bridge can communicate by container name. |
| **host** | `--network host` | Container shares host's network stack. No port mapping needed. |
| **none** | `--network none` | Completely isolated — no network access. |
| **custom bridge** | `--network mynet` | User-defined bridge — better DNS (containers find each other by name). |

### Custom Bridge Network (recommended)

```bash
# Create a network
docker network create mynet

# Run containers on the same network
docker run -d --name db --network mynet mysql:8
docker run -d --name api --network mynet myapp:v1

# Now 'api' can reach 'db' using hostname 'db'
# Connection string: Server=db;Database=mydb;...

docker network ls
docker network inspect mynet
docker network rm mynet
```

**Why custom bridge over default bridge?** On the default bridge, containers can only communicate by IP. On a custom bridge, Docker provides automatic DNS — containers resolve each other by name.

---

## 8. Docker Compose

Compose lets you define and run multi-container applications with a single YAML file.

### Full Compose File Example (API + Database)

```yaml
version: '3.8'

services:
  api:
    build:
      context: ./api
      dockerfile: Dockerfile
    image: myapp-api:latest
    container_name: myapp-api
    ports:
      - "5000:80"
    environment:
      - ASPNETCORE_ENVIRONMENT=Production
      - ConnectionStrings__Default=Server=db;Database=mydb;User Id=sa;Password=YourPassword123!
    depends_on:
      db:
        condition: service_healthy    # Wait for db to be healthy, not just started
    networks:
      - app-network
    restart: unless-stopped

  db:
    image: mcr.microsoft.com/mssql/server:2022-latest
    container_name: myapp-db
    environment:
      SA_PASSWORD: "YourPassword123!"
      ACCEPT_EULA: "Y"
    ports:
      - "1433:1433"
    volumes:
      - db-data:/var/opt/mssql      # Persist database data
    healthcheck:
      test: ["CMD", "/opt/mssql-tools/bin/sqlcmd", "-S", "localhost", "-U", "sa", "-P", "YourPassword123!", "-Q", "SELECT 1"]
      interval: 10s
      timeout: 5s
      retries: 5
    networks:
      - app-network
    restart: unless-stopped

volumes:
  db-data:                          # Named volume for DB persistence

networks:
  app-network:
    driver: bridge
```

### Compose Commands

```bash
docker-compose up                   # Start all services (foreground)
docker-compose up -d                # Start in background
docker-compose up --build           # Rebuild images before starting
docker-compose down                 # Stop and remove containers + networks
docker-compose down -v              # Also remove volumes
docker-compose logs -f api          # Stream logs for 'api' service
docker-compose ps                   # List service status
docker-compose exec api /bin/bash   # Shell into running service
docker-compose restart api          # Restart one service
docker-compose pull                 # Pull latest images
```

### `depends_on` — Important caveat

`depends_on` only waits for the container to **start**, not for the service inside to be **ready**. Use `healthcheck` + `condition: service_healthy` (as shown above) to wait for readiness.

---

## 9. `.dockerignore`

Like `.gitignore` — tells Docker what NOT to send to the build context. Smaller context = faster builds.

```
# .dockerignore
node_modules
.git
.env
*.log
dist
coverage
.DS_Store
**/*.test.ts
README.md
```

Without `.dockerignore`, `COPY . .` sends everything — including node_modules (hundreds of MBs) — to the Docker daemon unnecessarily.

---

## 10. Image Optimization Tips

1. **Use official slim/alpine base images** — `node:20-alpine` (50MB) vs `node:20` (1GB+)
2. **Order Dockerfile instructions by change frequency** — copy dependency files before source code so the `npm install` layer is cached
3. **Combine RUN commands** to minimize layers:
   ```dockerfile
   # Bad — 3 layers
   RUN apt-get update
   RUN apt-get install -y curl
   RUN rm -rf /var/lib/apt/lists/*

   # Good — 1 layer
   RUN apt-get update && apt-get install -y curl && rm -rf /var/lib/apt/lists/*
   ```
4. **Use multi-stage builds** to exclude build tools from final image
5. **Don't run as root** — add a non-root user:
   ```dockerfile
   RUN adduser --disabled-password appuser
   USER appuser
   ```

---

## 11. Environment Variables & Secrets

```bash
# Pass at runtime
docker run -e DB_HOST=localhost -e DB_PORT=5432 myapp

# Load from .env file
docker run --env-file .env myapp
```

In Compose:
```yaml
services:
  api:
    env_file:
      - .env
    environment:
      - NODE_ENV=production          # Override a specific var
```

**Never bake secrets into the image.** Use runtime environment variables, Azure Key Vault, or Docker Secrets (Swarm) instead.

---

## 12. Common Interview Q&A

**Q: What's the difference between `docker stop` and `docker kill`?**  
`docker stop` sends SIGTERM, waits 10s for graceful shutdown, then sends SIGKILL. `docker kill` sends SIGKILL immediately.

**Q: How do containers communicate with each other?**  
By placing them on the same Docker network. On a custom bridge network, containers resolve each other by service/container name via Docker's built-in DNS.

**Q: What happens to data when a container is deleted?**  
All data inside the container is lost unless it was stored in a volume or bind mount.

**Q: What is a Docker layer and why does it matter?**  
Each Dockerfile instruction creates a cached, read-only layer. Unchanged layers are reused across builds, making subsequent builds faster. This is why you copy `package.json` and run `npm install` before copying the rest of the source code.

**Q: How is Docker different from a VM?**  
Docker containers share the host OS kernel — they're process-level isolation. VMs have a full OS including kernel. Containers are faster, lighter, and start in seconds vs minutes.

**Q: What is Docker Compose used for?**  
Defining and running multi-container apps locally. It's not for production orchestration — Kubernetes handles that at scale.

---

## 13. Quick Reference Card

```
Build:    docker build -t name:tag .
Run:      docker run -d -p hostPort:containerPort name:tag
Shell in: docker exec -it <id> /bin/bash
Logs:     docker logs -f <id>
Stop:     docker stop <id>
Remove:   docker rm <id>  |  docker rmi image:tag
Compose:  docker-compose up -d  |  docker-compose down
Volumes:  docker volume create v  |  docker run -v v:/path image
Network:  docker network create net  |  docker run --network net image
Cleanup:  docker system prune -a  (removes all unused images, containers, networks)
```

---

*Docker notes for Kushal — May 2026*