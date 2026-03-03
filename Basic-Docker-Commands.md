---
layout: default
title: Docker Basic Commands
---

# Docker Basic Commands

## Objective

To understand and practice commonly used Docker commands for managing images and containers.

This document provides practical command usage along with explanations and best practices for working efficiently with Docker.

---

## Commands and Explanation

---

### 1. Check Docker Version

```bash
docker version
```

This command displays detailed information about the Docker client and server versions installed on your system.

It helps verify:

- Docker installation status
- Client-server compatibility
- API version
- Build details

**Output:**

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

**Tip:**  
If the client and server versions are incompatible, Docker may not function properly.

---

### 2. Display System Information

```bash
docker info
```

This command provides system-wide information including:

- Number of containers
- Number of images
- Storage driver
- Logging driver
- Cgroup configuration
- Network drivers
- Plugins installed

It is useful for troubleshooting and verifying Docker runtime configuration.

**Output:**

```bash
Client: Docker Engine - Community
 Version:    29.2.1
 Context:    default
 Debug Mode: false
 Plugins:
  buildx: Docker Buildx (Docker Inc.)
    Version:  v0.30.1
    Path:     /usr/libexec/docker/cli-plugins/docker-buildx
  compose: Docker Compose (Docker Inc.)
    Version:  v5.0.2
    Path:     /usr/libexec/docker/cli-plugins/docker-compose

Server:
 Containers: 0
  Running: 0
  Paused: 0
  Stopped: 0
 Images: 1
 Server Version: 29.2.1
 Storage Driver: overlayfs
  driver-type: io.containerd.snapshotter.v1
 Logging Driver: json-file
 Cgroup Driver: systemd
 Cgroup Version: 2
 Plugins:
  Volume: local
  Network: bridge host ipvlan macvlan null overlay
  Log: awslogs fluentd gcplogs gelf journald json-file local splunk syslog
 CDI spec directories:
```

**Best Practice:**  
Use `docker info` when diagnosing performance or networking issues.

---

### 3. List Docker Images

```bash
docker images
```

This command lists all Docker images available locally along with:

- Repository name
- Tag
- Image ID
- Size

**Output:**

```bash
IMAGE           ID             DISK USAGE   CONTENT SIZE   EXTRA
ubuntu:latest   d1e2e92c075e        119MB         31.7MB
```

**Note:**  
Images consume disk space. Remove unused images periodically to free storage.

---

### 4. Pull Ubuntu Image

```bash
docker pull ubuntu
```

This command downloads the latest Ubuntu image from Docker Hub to your local system.

If no tag is specified, Docker uses the `latest` tag by default.

**Output:**

```bash
Using default tag: latest
latest: Pulling from library/ubuntu
01d7766a2e4a: Pull complete
fd8cda969ed2: Download complete
Digest: sha256:d1e2e92c075e5ca139d51a140fff46f84315c0fdce203eab2807c7e495eff4f9
Status: Downloaded newer image for ubuntu:latest
docker.io/library/ubuntu:latest
```

**Best Practice:**  
Always specify image versions in production environments instead of using `latest`.

Example:

```bash
docker pull ubuntu:22.04
```

---

### 5. Remove Docker Image

```bash
docker rmi ubuntu
```

This command removes the Ubuntu image from your local system.

**Output:**

```bash
Untagged: ubuntu:latest
Deleted: sha256:d1e2e92c075e5ca139d51a140fff46f84315c0fdce203eab2807c7e495eff4f9
```

**Important:**  
You cannot remove an image if a container is using it. Stop and remove dependent containers first.

---

### 6. Run a Container

```bash
docker run ubuntu
```

This command creates and starts a container from the Ubuntu image.

If the image is not available locally, Docker automatically pulls it.

**Output:**

```bash
Unable to find image 'ubuntu:latest' locally
latest: Pulling from library/ubuntu
01d7766a2e4a: Pull complete
fd8cda969ed2: Download complete
Digest: sha256:d1e2e92c075e5ca139d51a140fff46f84315c0fdce203eab2807c7e495eff4f9
Status: Downloaded newer image for ubuntu:latest
```

**Common Options:**

```bash
docker run -it ubuntu bash
docker run -d nginx
docker run -p 8080:80 nginx
```

---

### 7. List Containers

```bash
docker ps -a
```

This command shows all containers, including:

- Running containers
- Stopped containers
- Exited containers

**Output:**

```bash
CONTAINER ID   IMAGE     COMMAND       CREATED          STATUS                      PORTS     NAMES
6a5c3eba97f3   ubuntu    "/bin/bash"   27 seconds ago   Exited (0) 26 seconds ago             cranky_chatelet
```

Use:

```bash
docker ps
```

to display only running containers.

---

### 8. Start a Container

```bash
docker start container_name
```

This command starts an existing stopped container.

**Example:**

```bash
docker start cranky_chatelet
```

**Output:**

```bash
cranky_chatelet
```

---

### 9. Stop a Container

```bash
docker stop container_name
```

This command gracefully stops a running container.

**Example:**

```bash
docker stop cranky_chatelet
```

**Output:**

```bash
cranky_chatelet
```

---

### 10. Restart a Container

```bash
docker restart container_name
```

This command restarts a running or stopped container.

**Example:**

```bash
docker restart cranky_chatelet
```

**Output:**

```bash
cranky_chatelet
```

---

### 11. Remove a Container

```bash
docker rm container_name
```

This command deletes a stopped container from the system.

**Example:**

```bash
docker rm cranky_chatelet
```

**Output:**

```bash
docker rm cranky_chatelet
cranky_chatelet
```

To force remove a running container:

```bash
docker rm -f container_name
```

---

## Additional Useful Commands

### Remove All Stopped Containers

```bash
docker container prune
```

### Remove All Unused Images

```bash
docker image prune
```

### Remove Everything Unused

```bash
docker system prune
```

---

## Conclusion

These Docker commands form the foundation for working with containers and images.

By mastering these commands, you can:

- Manage container lifecycle efficiently
- Optimize system resources
- Troubleshoot Docker environments
- Deploy applications effectively

Understanding these basics is essential before moving to advanced topics like Docker Networking, Volumes, Compose, and Swarm.

---
End of File
