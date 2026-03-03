---
layout: default
title: Docker Basic Commands
---

# Docker Basic Commands

## Objective

To understand and practice commonly used Docker commands for managing images and containers.

This guide explains essential Docker CLI commands along with additional options, best practices, troubleshooting tips, and practical examples.

---

# Docker Architecture Overview

Docker follows a client-server architecture:

- **Docker Client** → Sends commands
- **Docker Daemon** → Executes container operations
- **Docker Images** → Blueprints for containers
- **Docker Containers** → Running instances of images
- **Docker Registry** → Stores images (Docker Hub)

Workflow:

```
Image → Container → Running Process
```

---

## Commands and Explanation

---

## 1. Check Docker Version

```bash
docker version
```

Displays Docker client and server version details.

### Why This Is Important

- Confirms Docker is installed
- Verifies client-server compatibility
- Useful for debugging version mismatches

### Sample Output

```bash
Client: Docker Engine - Community
 Version:           29.2.1
 API version:       1.53
 Go version:        go1.25.6
 Git commit:        a5c7197
 Built:             Mon Feb  2 17:17:26 2026
 OS/Arch:           linux/amd64
 Context:           default
```

---

## 2. Display System Information

```bash
docker info
```

Shows detailed runtime configuration including:

- Containers count
- Images count
- Storage driver
- Logging driver
- Cgroup details
- Network drivers
- Security options

### When to Use

- Troubleshooting Docker startup issues
- Checking disk usage
- Verifying storage driver

---

## 3. List Docker Images

```bash
docker images
```

Lists all images stored locally.

Alternative:

```bash
docker image ls
```

### Output Fields

- Repository
- Tag
- Image ID
- Created time
- Size

### Best Practice

Remove unused images regularly:

```bash
docker image prune
```

---

## 4. Pull Ubuntu Image

```bash
docker pull ubuntu
```

Downloads image from Docker Hub.

### Pull Specific Version

```bash
docker pull ubuntu:22.04
```

### Why Avoid `latest` in Production

- Version may change
- Can break deployments
- Not predictable

---

## 5. Remove Docker Image

```bash
docker rmi ubuntu
```

Removes image from local system.

### Force Remove

```bash
docker rmi -f ubuntu
```

### Note

An image cannot be deleted if a container is using it.

---

## 6. Run a Container

```bash
docker run ubuntu
```

Creates and starts a container.

### Common Variations

Interactive mode:

```bash
docker run -it ubuntu bash
```

Detached mode:

```bash
docker run -d nginx
```

Port mapping:

```bash
docker run -d -p 8080:80 nginx
```

Set environment variable:

```bash
docker run -e APP_ENV=prod nginx
```

Mount volume:

```bash
docker run -v myvolume:/data nginx
```

---

## 7. List Containers

```bash
docker ps -a
```

Shows all containers (running and stopped).

Only running:

```bash
docker ps
```

Only container IDs:

```bash
docker ps -q
```

---

## 8. Start a Container

```bash
docker start container_name
```

Starts previously stopped container.

---

## 9. Stop a Container

```bash
docker stop container_name
```

Gracefully stops container.

### Force Stop

```bash
docker kill container_name
```

---

## 10. Restart a Container

```bash
docker restart container_name
```

Stops and starts container in one command.

---

## 11. Remove a Container

```bash
docker rm container_name
```

Deletes a stopped container.

### Force Remove Running Container

```bash
docker rm -f container_name
```

---

# Container Lifecycle

Container states:

1. Created
2. Running
3. Paused
4. Stopped
5. Removed

View status:

```bash
docker ps -a
```

---

# Resource Monitoring

### View Container Logs

```bash
docker logs container_name
```

Follow logs:

```bash
docker logs -f container_name
```

### View Resource Usage

```bash
docker stats
```

---

# Image and Container Cleanup

Remove stopped containers:

```bash
docker container prune
```

Remove unused images:

```bash
docker image prune
```

Remove unused volumes:

```bash
docker volume prune
```

Remove everything unused:

```bash
docker system prune
```

---

# Docker Command Categories

| Category | Command Example |
|-----------|----------------|
| Image Management | docker pull |
| Container Lifecycle | docker run |
| Monitoring | docker logs |
| Cleanup | docker system prune |
| Networking | docker network ls |
| Storage | docker volume ls |

---

# Common Mistakes

- Using `latest` tag in production
- Not cleaning unused containers
- Forgetting port mapping
- Not specifying restart policies
- Running containers as root unnecessarily

---

# Best Practices

- Always specify image versions
- Use named containers
- Use restart policies
- Monitor logs regularly
- Clean unused resources
- Avoid exposing unnecessary ports

Example restart policy:

```bash
docker run --restart unless-stopped nginx
```

---

# Quick Command Reference

| Command | Purpose |
|----------|---------|
| docker version | Show version |
| docker info | System info |
| docker images | List images |
| docker pull | Download image |
| docker run | Create container |
| docker ps | List containers |
| docker stop | Stop container |
| docker start | Start container |
| docker restart | Restart container |
| docker rm | Remove container |

---

# Conclusion

Docker basic commands form the foundation of container management.

Mastering these commands enables you to:

- Deploy applications quickly
- Manage container lifecycle efficiently
- Monitor performance
- Optimize system resources
- Prepare for advanced Docker concepts like Networking, Volumes, Compose, and Swarm

Understanding these basics is essential for DevOps and cloud-native development.

---

End of File
