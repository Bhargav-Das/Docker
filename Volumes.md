---
layout: default
title: Docker Volumes
---

# Docker Volumes, Bind Mounts and tmpfs

---

# Overview

Docker containers are ephemeral by default. When a container is removed, its internal filesystem is deleted.

To persist data, Docker provides three main storage mechanisms:

- **Volumes**
- **Bind Mounts**
- **tmpfs Mounts**

Each serves a different purpose depending on performance, security, and persistence requirements.

---

# Docker Storage Architecture

When a container writes data:

- Without volume → stored in writable container layer
- With volume → stored in Docker-managed directory
- With bind mount → stored directly on host path
- With tmpfs → stored in memory (RAM)

Container writable layer is removed when container is deleted. Volumes prevent this data loss.

---

# Task 1: Create a Volume

## Method 1 – Explicit Creation

```bash
docker volume create myvolume
```

**Output:**

```bash
myvolume
```

---

## Verify Volume

```bash
docker volume ls
```

**Output:**

```bash
DRIVER    VOLUME NAME
local     myvolume
```

---

# Named vs Anonymous Volumes

- **Named Volume** → `myvolume`
- **Anonymous Volume** → Auto-generated name

Anonymous example:

```bash
docker run -v /data nginx
```

Docker creates a random volume name automatically.

---

# Task 2: Use Volume in Container

## Using -v Flag

```bash
docker run -d \
  --name mysql-container \
  -v myvolume:/var/lib/mysql \
  mysql
```

If image not present, Docker pulls it automatically.

---

## Using --mount Flag (Recommended)

```bash
docker run -d \
  --name mysql-container \
  --mount source=myvolume,target=/var/lib/mysql \
  mysql
```

### Why Prefer --mount?

- Clear syntax
- More configurable
- Required for advanced options

---

# Volume Lifecycle

- Volume exists independently of container
- Removing container does NOT remove volume
- Must remove volume manually

---

# Task 3: Inspect Volume

```bash
docker volume inspect myvolume
```

**Output:**

```json
[
    {
        "Driver": "local",
        "Mountpoint": "/var/lib/docker/volumes/myvolume/_data",
        "Name": "myvolume",
        "Scope": "local"
    }
]
```

### Important Fields

- Mountpoint → Actual storage path
- Driver → Storage driver
- Scope → Local or global

---

# Task 4: Remove Volume

```bash
docker volume rm myvolume
```

---

## Remove Unused Volumes

```bash
docker volume prune
```

Removes only unused volumes.

---

# Volume Backup and Restore

## Backup Volume

```bash
docker run --rm \
  -v myvolume:/volume \
  -v $(pwd):/backup \
  ubuntu \
  tar czf /backup/backup.tar.gz -C /volume .
```

## Restore Volume

```bash
docker run --rm \
  -v myvolume:/volume \
  -v $(pwd):/backup \
  ubuntu \
  tar xzf /backup/backup.tar.gz -C /volume
```

---

# Volume Use Cases

## Database Storage

- MySQL
- PostgreSQL
- MongoDB

Why use volume?

- Persistent
- Docker-managed
- Production-safe
- Data survives container restart

---

# Bind Mount

## Concept

Bind mount maps a specific host directory into container.

Host directly controls files.

---

## Create Bind Mount (-v)

```bash
docker run -d \
  --name nginx-container \
  -v /home/prateek/html:/usr/share/nginx/html \
  nginx
```

---

## Using --mount (Recommended)

```bash
docker run -d \
  --name nginx-container \
  --mount type=bind,source=/home/prateek/html,target=/usr/share/nginx/html \
  nginx
```

---

# Read-Only Bind Mount

```bash
docker run -d \
  --mount type=bind,source=/home/prateek/html,target=/usr/share/nginx/html,readonly \
  nginx
```

Prevents container from modifying host files.

---

# Remove Bind Mount

Bind mount is removed when container is deleted:

```bash
docker rm -f nginx-container
```

---

# Bind Mount Use Cases

## Development Environment

- Source code on host
- Container runs app
- Instant updates

Benefits:

- Live reload
- No rebuild required
- Faster development workflow

---

# tmpfs Mount

## Concept

tmpfs stores data in RAM only.

Characteristics:

- No persistence
- High performance
- Secure (not written to disk)

---

## Create tmpfs Mount

```bash
docker run -d \
  --name temp-container \
  --mount type=tmpfs,target=/app/cache \
  nginx
```

---

## Alternative Syntax

```bash
docker run -d \
  --tmpfs /app/cache \
  nginx
```

---

# tmpfs Advanced Options

```bash
docker run -d \
  --mount type=tmpfs,target=/app/cache,tmpfs-size=64m \
  nginx
```

Limits RAM usage to 64MB.

---

# tmpfs Use Cases

- Encryption keys
- Session data
- Temporary cache
- Sensitive information

---

# Differences: Volume vs Bind Mount vs tmpfs

| Feature         | Volume           | Bind Mount       | tmpfs        |
|-----------------|------------------|------------------|-------------|
| Managed by      | Docker           | Host OS          | Memory      |
| Storage Location| Docker directory | Host path        | RAM         |
| Persistence     | Yes              | Yes              | No          |
| Performance     | Good             | Good             | Very Fast   |
| Production Safe | Yes              | Risky (depends)  | Limited     |
| Dev Friendly    | Moderate         | Excellent        | No          |
| Security        | High             | Depends on host  | Very High   |

---

# Performance Considerations

- Volumes → Optimized for container usage
- Bind mounts → Depends on host filesystem
- tmpfs → Fastest but volatile

---

# Security Notes

- Avoid exposing sensitive host directories
- Use read-only mounts when possible
- Use tmpfs for secrets
- Limit mount permissions

---

# Advanced Volume Drivers

List drivers:

```bash
docker plugin ls
```

Examples:

- local
- NFS
- Cloud storage drivers
- Distributed storage

---

# Troubleshooting

Check volume path:

```bash
docker volume inspect myvolume
```

Check disk usage:

```bash
docker system df
```

Restart Docker if mount issues occur:

```bash
sudo systemctl restart docker
```

---

# Best Practices

- Use named volumes in production
- Avoid anonymous volumes in production
- Use bind mounts only in development
- Backup volumes regularly
- Monitor disk usage
- Use tmpfs for sensitive temporary data

---

# Storage Command Cheat Sheet

| Command | Purpose |
|----------|---------|
| docker volume create | Create volume |
| docker volume ls | List volumes |
| docker volume inspect | Inspect volume |
| docker volume rm | Remove volume |
| docker volume prune | Remove unused volumes |
| docker run -v | Mount volume |
| docker run --mount | Modern mount syntax |

---

# Conclusion

Docker provides flexible storage options:

- **Volumes** → Production-grade persistent storage
- **Bind mounts** → Development-friendly direct host mapping
- **tmpfs** → Fast and secure temporary storage

Choosing the correct storage method is essential for building scalable, secure, and reliable containerized applications.

---

End of File
