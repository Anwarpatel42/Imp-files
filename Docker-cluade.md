# Complete Docker Guide: Commands, Examples, and Deep Dive

## Table of Contents
1. Docker Basics
2. Core Concepts
3. Installation and Setup
4. Essential Commands
5. Working with Images
6. Working with Containers
7. Networking
8. Volumes and Data Persistence
9. Docker Compose
10. Best Practices

---

## 1. Docker Basics

Docker is a containerization platform that packages applications with all their dependencies into isolated containers. These containers are lightweight, portable, and run consistently across different environments.

### Why Docker?
- **Consistency**: "Works on my machine" problem solved
- **Isolation**: Applications don't interfere with each other
- **Efficiency**: Containers use fewer resources than VMs
- **Scalability**: Easy to deploy multiple instances
- **DevOps**: Streamlines development and deployment workflows

---

## 2. Core Concepts

### Images
A Docker image is a lightweight, standalone, executable package containing everything needed to run an application (code, runtime, libraries, dependencies). Think of it as a blueprint or template.

### Containers
A container is a running instance of an image. Multiple containers can run from the same image, each isolated from others.

### Docker Daemon
The background service that manages images and containers.

### Docker Client
The command-line tool used to interact with the Docker daemon.

### Registry
A centralized repository storing images (Docker Hub is the default public registry).

### Layers
Docker images are composed of multiple read-only layers stacked on top of each other. Each layer represents changes from the previous one.

---

## 3. Installation and Setup

### macOS
```bash
# Install Docker Desktop from https://www.docker.com/products/docker-desktop
# Or use Homebrew
brew install --cask docker
```

### Linux (Ubuntu)
```bash
# Update package manager
sudo apt-get update

# Install Docker
sudo apt-get install docker.io

# Add user to docker group (avoid using sudo)
sudo usermod -aG docker $USER

# Verify installation
docker --version
```

### Windows
```bash
# Download Docker Desktop from Docker's website
# Or use Windows Package Manager
winget install Docker.DockerDesktop
```

### Verify Installation
```bash
docker run hello-world
```

---

## 4. Essential Commands

### General Information
```bash
# Check Docker version
docker --version

# Display system-wide information
docker info

# Show help for any command
docker <command> --help

# View Docker daemon logs
docker logs
```

---

## 5. Working with Images

### Pulling Images
```bash
# Pull latest version of an image
docker pull ubuntu

# Pull specific version (tag)
docker pull ubuntu:22.04

# Pull from custom registry
docker pull myregistry.azurecr.io/myimage:latest
```

### Listing Images
```bash
# List all local images
docker images

# List images with more detail
docker images --no-trunc

# Filter images
docker images --filter "reference=ubuntu*"

# Show only image IDs
docker images -q
```

### Searching Images
```bash
# Search Docker Hub for images
docker search nginx

# Search with filters
docker search --filter is-official=true ubuntu
```

### Building Images

#### Using Dockerfile
```dockerfile
# Dockerfile example
FROM ubuntu:22.04

WORKDIR /app

RUN apt-get update && apt-get install -y \
    python3 \
    python3-pip

COPY requirements.txt .
RUN pip3 install -r requirements.txt

COPY . .

EXPOSE 5000

CMD ["python3", "app.py"]
```

```bash
# Build image from Dockerfile
docker build -t myapp:1.0 .

# Build with specific Dockerfile
docker build -f Dockerfile.prod -t myapp:prod .

# Build without cache
docker build --no-cache -t myapp:latest .

# Build with build arguments
docker build --build-arg NODE_ENV=production -t myapp:prod .
```

### Image Information
```bash
# Show image layers and history
docker history myimage:latest

# Inspect image details
docker inspect myimage:latest

# Show image size
docker images --human-readable myimage
```

### Tagging Images
```bash
# Create a tag for an image
docker tag myapp:1.0 myapp:latest

# Tag for registry push
docker tag myapp:1.0 myregistry.com/myapp:1.0
```

### Removing Images
```bash
# Remove single image
docker rmi myimage:latest

# Remove multiple images
docker rmi image1:tag image2:tag

# Force remove
docker rmi -f myimage:latest

# Remove unused images
docker image prune

# Remove all dangling images
docker image prune -a
```

### Pushing Images
```bash
# Push to Docker Hub
docker push username/myapp:1.0

# Push to private registry
docker push myregistry.com/myapp:1.0
```

---

## 6. Working with Containers

### Running Containers

#### Basic Run
```bash
# Run container in foreground (interactive)
docker run ubuntu:22.04 echo "Hello from container"

# Run with name
docker run --name my-container ubuntu:22.04

# Run in detached mode (background)
docker run -d --name web-server nginx
```

#### Port Mapping
```bash
# Map single port
docker run -p 8080:80 nginx

# Map multiple ports
docker run -p 8080:80 -p 8443:443 nginx

# Map to random port
docker run -p 80 nginx

# Map specific IP address
docker run -p 192.168.1.100:8080:80 nginx
```

#### Volume Mounting
```bash
# Mount local directory to container
docker run -v /host/path:/container/path nginx

# Read-only mount
docker run -v /host/path:/container/path:ro nginx

# Anonymous volume
docker run -v /data myimage
```

#### Environment Variables
```bash
# Set environment variable
docker run -e DATABASE_URL=postgresql://db:5432 myapp

# Set multiple variables
docker run -e VAR1=value1 -e VAR2=value2 myapp

# Load from file
docker run --env-file .env myapp
```

#### Resource Limits
```bash
# Limit memory to 512MB
docker run -m 512m myapp

# Limit CPU to 1 core
docker run --cpus 1 myapp

# Limit memory and swap
docker run -m 512m --memory-swap 1g myapp
```

#### Advanced Options
```bash
# Run with privileged mode
docker run --privileged myimage

# Expose port
docker run --expose 3000 myapp

# Add hostname
docker run -h myhost myimage

# Run as specific user
docker run --user 1000:1000 myimage

# Restart policy
docker run --restart always myapp
docker run --restart on-failure:5 myapp
```

### Listing Containers
```bash
# List running containers
docker ps

# List all containers (including stopped)
docker ps -a

# List with verbose output
docker ps -a --no-trunc

# Filter containers
docker ps --filter "status=exited"
docker ps --filter "name=web"

# Show only container IDs
docker ps -q
```

### Inspecting Containers
```bash
# Get detailed information
docker inspect my-container

# Extract specific info
docker inspect --format='{{.State.Running}}' my-container
docker inspect --format='{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' my-container

# View container logs
docker logs my-container

# Follow logs in real-time
docker logs -f my-container

# Show last 100 lines
docker logs --tail 100 my-container

# Show logs with timestamps
docker logs --timestamps my-container
```

### Managing Containers

#### Start/Stop/Restart
```bash
# Start stopped container
docker start my-container

# Stop running container
docker stop my-container

# Forcefully stop container
docker kill my-container

# Restart container
docker restart my-container

# Pause container processes
docker pause my-container

# Unpause container
docker unpause my-container
```

#### Executing Commands
```bash
# Execute command in running container
docker exec my-container ls -la

# Execute interactive command
docker exec -it my-container bash

# Execute as specific user
docker exec --user root my-container apt-get update

# Detached execution
docker exec -d my-container background-task
```

#### Copying Files
```bash
# Copy from host to container
docker cp /host/file my-container:/container/path

# Copy from container to host
docker cp my-container:/container/file /host/path

# Copy directory
docker cp /host/dir my-container:/container/path
```

#### Removing Containers
```bash
# Remove stopped container
docker rm my-container

# Remove multiple containers
docker rm container1 container2

# Force remove running container
docker rm -f my-container

# Remove all stopped containers
docker container prune

# Remove containers matching filter
docker rm $(docker ps -a -q --filter "status=exited")
```

### Container Statistics
```bash
# Show live resource usage
docker stats

# Show stats for specific container
docker stats my-container

# Disable streaming
docker stats --no-stream
```

---

## 7. Networking

### Network Types
- **bridge**: Default, containers on same bridge can communicate
- **host**: Container shares host network
- **overlay**: For swarm mode, multi-host communication
- **macvlan**: Assign MAC address to container
- **none**: No network access

### Managing Networks
```bash
# List networks
docker network ls

# Create bridge network
docker network create my-network

# Create with options
docker network create --driver bridge --subnet 172.20.0.0/16 my-network

# Connect container to network
docker network connect my-network my-container

# Disconnect container from network
docker network disconnect my-network my-container

# Inspect network
docker network inspect my-network

# Remove network
docker network rm my-network

# Remove unused networks
docker network prune
```

### Running Containers on Network
```bash
# Run container on specific network
docker run --network my-network --name app nginx

# Container service discovery
docker run --network my-network --name web nginx
docker run --network my-network -e DB_HOST=web myapp

# Expose port to network
docker run --network my-network -p 8080:80 nginx
```

---

## 8. Volumes and Data Persistence

### Volume Types
- **Named volumes**: Managed by Docker, stored on host
- **Bind mounts**: Direct host directory mapping
- **tmpfs mounts**: Temporary in-memory storage

### Managing Volumes
```bash
# Create named volume
docker volume create my-volume

# List volumes
docker volume ls

# Inspect volume
docker volume inspect my-volume

# Remove volume
docker volume rm my-volume

# Remove unused volumes
docker volume prune
```

### Using Volumes
```bash
# Mount named volume
docker run -v my-volume:/data myapp

# Mount bind directory
docker run -v /host/data:/container/data myapp

# Mount with read-only
docker run -v my-volume:/data:ro myapp

# Mount multiple volumes
docker run -v vol1:/data1 -v vol2:/data2 myapp
```

### Volume Drivers
```bash
# Create volume with specific driver
docker volume create --driver local my-volume

# Use NFS driver
docker volume create --driver local --opt type=nfs --opt o=addr=nfs-server,vers=4,soft,timeo=180,bg,tcp --opt device=:/export/data my-nfs-volume
```

---

## 9. Docker Compose

Docker Compose allows defining multi-container applications using YAML.

### Docker Compose File Structure
```yaml
version: '3.9'

services:
  web:
    build: .
    ports:
      - "8080:5000"
    environment:
      - DATABASE_URL=postgresql://db:5432/mydb
    volumes:
      - ./app:/app
    depends_on:
      - db
    networks:
      - app-network

  db:
    image: postgres:15
    environment:
      - POSTGRES_PASSWORD=secret
      - POSTGRES_DB=mydb
    volumes:
      - db-volume:/var/lib/postgresql/data
    networks:
      - app-network

volumes:
  db-volume:

networks:
  app-network:
    driver: bridge
```

### Docker Compose Commands
```bash
# Start services
docker-compose up

# Start in detached mode
docker-compose up -d

# Build images
docker-compose build

# Rebuild without cache
docker-compose build --no-cache

# View running services
docker-compose ps

# View service logs
docker-compose logs

# Follow logs
docker-compose logs -f

# Follow specific service logs
docker-compose logs -f web

# Execute command in service
docker-compose exec web bash

# Stop services
docker-compose stop

# Start services
docker-compose start

# Restart services
docker-compose restart

# Remove containers
docker-compose down

# Remove containers and volumes
docker-compose down -v

# Remove containers, volumes, and images
docker-compose down -v --rmi all
```

---

## 10. Best Practices

### Image Best Practices

**Use specific base image tags**
```dockerfile
# ❌ Avoid
FROM node

# ✅ Better
FROM node:18-alpine
```

**Minimize layers and use multi-stage builds**
```dockerfile
# Multi-stage build
FROM node:18 AS builder
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

FROM node:18-alpine
WORKDIR /app
COPY --from=builder /app/dist ./dist
COPY package*.json ./
RUN npm install --only=production
CMD ["node", "dist/index.js"]
```

**Use .dockerignore**
```
node_modules
npm-debug.log
.git
.gitignore
.env
.DS_Store
```

**Don't run as root**
```dockerfile
RUN useradd -m -u 1000 appuser
USER appuser
```

**Keep images small**
```dockerfile
# Use alpine for smaller images
FROM python:3.11-alpine

# Clean up package manager cache
RUN apt-get update && apt-get install -y curl && rm -rf /var/lib/apt/lists/*
```

### Container Best Practices
- One process per container
- Use health checks
- Set resource limits
- Use restart policies
- Implement proper logging
- Use environment variables for configuration
- Keep containers stateless when possible

### Security Best Practices
- Keep images updated
- Use non-root users
- Scan images for vulnerabilities
- Don't hardcode secrets (use environment variables or secrets management)
- Use private registries for sensitive images
- Limit container capabilities

### Example: Production-Ready Dockerfile
```dockerfile
FROM node:18-alpine

WORKDIR /app

# Create non-root user
RUN addgroup -g 1001 -S nodejs
RUN adduser -S nodejs -u 1001

# Install dependencies
COPY package*.json ./
RUN npm ci --only=production

# Copy application
COPY . .
RUN chown -R nodejs:nodejs /app

USER nodejs

# Health check
HEALTHCHECK --interval=30s --timeout=3s --start-period=40s --retries=3 \
  CMD node healthcheck.js

EXPOSE 3000

CMD ["node", "server.js"]
```

---

## Useful Command Combinations

```bash
# Stop all containers
docker stop $(docker ps -q)

# Remove all containers
docker rm $(docker ps -a -q)

# Remove all images
docker rmi $(docker images -q)

# View resource usage of all containers
docker stats --all

# Get IP address of container
docker inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' container-name

# Execute command in all running containers
docker ps -q | xargs -I {} docker exec {} command

# Backup volume
docker run --rm -v my-volume:/data -v /host/backup:/backup alpine tar czf /backup/volume-backup.tar.gz -C /data .

# Restore volume
docker run --rm -v my-volume:/data -v /host/backup:/backup alpine tar xzf /backup/volume-backup.tar.gz -C /data
```

---

## Troubleshooting Commands

```bash
# Check Docker daemon status
docker ps

# View Docker logs
docker logs container-name

# Inspect container configuration
docker inspect container-name

# Check network connectivity
docker exec my-container ping google.com

# View resource consumption
docker stats

# Debug image layers
docker history image-name

# Test image build
docker build -t test-image . && docker run test-image
```

---

This guide covers Docker fundamentals through advanced usage. Start with basic commands and gradually explore more complex scenarios as your expertise grows!
