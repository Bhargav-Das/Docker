---
layout: default
title: Docker Volumes
---

# Docker Volumes, Bind Mounts and tmpfs

---

## Task 1: Create a Volume

### Method 1 – Explicit Creation

```bash
docker volume create myvolume
```

**Output:**

```bash
myvolume
```

---

### Verify

```bash
docker volume ls
```

**Output:**

```bash
DRIVER    VOLUME NAME
local     myvolume
```

---

## Task 2: Use Volume in Container

### Using -v flag

```bash
docker run -d \
  --name mysql-container \
  -v myvolume:/var/lib/mysql \
  mysql
```

**Output:**

```bash
Unable to find image 'mysql:latest' locally
latest: Pulling from library/mysql
357926362d31: Pull complete
4a629f1008ff: Pull complete
74a8e4bbd9fe: Pull complete
f3702ac323ca: Pull complete
658630c937c6: Pull complete
c89a19cdad3b: Pull complete
00af5f73db53: Pull complete
ab1b8ea72826: Pull complete
8314ff001dec: Pull complete
61941d436e88: Pull complete
b222ef59b124: Download complete
1dc8eadaef55: Download complete
Digest: sha256:e5dc14f6e01c3e577e669337d2855c6d1561b30d8ef2c592e63e4e8a9a52650a
Status: Downloaded newer image for mysql:latest
21b262c732e37f3f29e4ebaf9d9559637b29129224cafea65cc5fa84ed5a7231

```

---

### Using --mount flag (Recommended Modern Syntax)

```bash
docker run -d \
  --name mysql-container \
  --mount source=myvolume,target=/var/lib/mysql \
  mysql
```

**Output:**

```bash
88acfe876c397a41d2bbbfa239be6d5c5efe7be3997679d83f291502eb2d267f
```

---

## Task 3: Inspect Volume

```bash
docker volume inspect myvolume
```

**Output:**

```bash
[
    {
        "CreatedAt": "2026-03-02T16:17:46Z",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/myvolume/_data",
        "Name": "myvolume",
        "Options": null,
        "Scope": "local"
    }
]
```

### Shows:

* Mountpoint
* Driver
* Metadata

---

## Task 4: Remove Volume

```bash
docker volume rm myvolume
```

**Output:**

```bash
myvolume
```

---

### Remove unused volumes

```bash
docker volume prune
```

**Output:**

```bash
WARNING! This will remove anonymous local volumes not used by at least one container.
Are you sure you want to continue? [y/N] y
Total reclaimed space: 0B
```

---

## Volume Use Case

### Use Case 1: Database Storage

* MySQL
* PostgreSQL
* MongoDB

### Why?

* Docker-managed
* Safe
* Portable
* Production-ready

---

# Bind Mount

## Concept

Bind mount maps a host directory directly into a container.

Host controls data.

---

## Task 1: Create Bind Mount

### Using -v

```bash
docker run -d \
  --name nginx-container \
  -v /home/prateek/html:/usr/share/nginx/html \
  nginx
```

**Output:**

```bash
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
1ad233904a11: Pull complete
b47f187216b6: Pull complete
206356c42440: Pull complete
df0b66c867e4: Pull complete
eedda9fd8786: Pull complete
35ff83c394d6: Pull complete
17d0911eaf62: Pull complete
c13150b54a53: Download complete
0019bbecc572: Download complete
Digest: sha256:0236ee02dcbce00b9bd83e0f5fbc51069e7e1161bd59d99885b3ae1734f3392e
Status: Downloaded newer image for nginx:latest
238974fefc0ad851366b2d8dc4d455f01ab016dbba5872ae5dabe218f56e758a
```

---

### Using --mount

```bash
docker run -d \
  --name nginx-container \
  --mount type=bind,source=/home/prateek/html,target=/usr/share/nginx/html \
  nginx
```

**Output:**

```bash
27fd8e0eb70638830f2535a39dd1a36c3a2b0d20b675494cf318155c8e77d572
```

---

## Task 2: Remove Bind Mount

Bind mount is removed when container is removed:

```bash
docker rm -f nginx-container
```

**Output:**

```bash
nginx-container
```

No separate delete like volume.

---

## Bind Mount Use Case

### Use Case 1: Development Environment

Example:

* Node.js app
* Source code on host
* Container runs app

### Benefits:

* Live code updates
* No rebuild required
* Fast development

---

# tmpfs Mount

## Concept

tmpfs stores data in RAM only.

* No persistence
* Deleted when container stops
* Very fast

---

## Task: Create tmpfs Mount

```bash
docker run -d \
  --name temp-container \
  --mount type=tmpfs,target=/app/cache \
  nginx
```

**Output:**

```bash
4bcd2c611b159b204c5816e49df63b20be774eebb3d61df96f72559f919b1503
```

---

### Alternative

```bash
docker run -d \
  --tmpfs /app/cache \
  nginx
```

**Output:**

```bash
d324874563c9ffbd786ae4bb4092cb9df027ab7f373d2cc7f88df58dfbde973b
```

---

## tmpfs Use Case

### Use Case 1: Sensitive Temporary Data

Example:

* Encryption keys
* Session tokens
* Temporary cache

### Why?

* Not written to disk
* More secure
* High speed

---

# Differences: Volume vs Bind Mount vs tmpfs

| Feature         | Volume           | Bind Mount    | tmpfs     |
| --------------- | ---------------- | ------------- | --------- |
| Managed by      | Docker           | Host OS       | Memory    |
| Location        | Docker directory | Any host path | RAM       |
| Persistence     | Yes              | Yes           | No        |
| Performance     | Good             | Good          | Very Fast |
| Production safe | Yes              | Risky         | Limited   |
| Dev friendly    | Moderate         | Excellent     | No        |

---

## Conclusion

Docker provides multiple storage options:

* Volumes for production-safe persistence
* Bind mounts for development flexibility
* tmpfs for fast, temporary, and secure data handling
