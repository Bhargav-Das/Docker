---
layout: default
title: Docker Compose
---

# Docker Compose

## 1. Concept: Docker Compose vs Docker Run

Docker Compose is used to define and manage applications that require multiple containers. Instead of running containers one by one, everything is described in a single YAML file (`docker-compose.yml`) and started together.

Docker Compose follows a **declarative configuration model**, meaning you define the desired state once, and Docker ensures it runs as defined.

---

## Docker Run

`docker run` is used to launch a single container. All configurations like ports, volumes, environment variables, restart policies, and networking must be provided manually every time.

### Example

```bash
docker run -d \
  --name my-nginx \
  -p 8080:80 \
  -v ./html:/usr/share/nginx/html \
  -e NGINX_HOST=localhost \
  --restart unless-stopped \
  nginx:alpine
```

### Limitations of docker run

- Manual configuration each time  
- Difficult to manage multi-container applications  
- Hard to scale  
- No centralized configuration file  

---

## Docker Compose

Docker Compose allows you to define multiple services in a YAML file and manage them with a single command.

```yaml
version: 3.8

services:
  nginx:
    image: nginx:alpine
    container_name: my-nginx
    ports:
      - "8080:80"
    volumes:
      - ./html:/usr/share/nginx/html
    environment:
      - NGINX_HOST=localhost
    restart: unless-stopped
```

Start with:

```bash
docker compose up -d
```

---

## Advantages of Docker Compose

- Single command to start complete application  
- Same configuration works on all systems  
- Easier to manage complex setups  
- Built-in lifecycle management  
- Supports dependency handling  
- Allows scaling of services  
- Automatic network creation  
- Cleaner DevOps workflow  

---

## Mapping: docker run vs docker-compose

| docker run | docker-compose.yml | Purpose |
|-----------|------------------|--------|
| -p 8080:80 | ports | Port mapping |
| -v ./data:/app | volumes | Storage |
| -e KEY=value | environment | Environment variables |
| --name app | container_name | Naming |
| --network net | networks | Networking |
| --restart always | restart | Restart policy |
| -d | compose up -d | Detached mode |
| --link db | links | Legacy linking |
| N/A | depends_on | Startup order |
| -w /app | working_dir | Working directory |
| --user 1000 | user | Run as user |

---

# 2. Practical: WordPress + MySQL

This example demonstrates deploying a WordPress application connected to a MySQL database using Docker Compose.

---

## docker-compose.yml

```yaml
version: '3.8'

services:
  mysql:
    image: mysql:5.7
    container_name: mysql
    environment:
      MYSQL_ROOT_PASSWORD: secret
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wpuser
      MYSQL_PASSWORD: wppass
    volumes:
      - mysql_data:/var/lib/mysql
    networks:
      - wordpress-network

  wordpress:
    image: wordpress:latest
    container_name: wordpress
    ports:
      - "8080:80"
    environment:
      WORDPRESS_DB_HOST: mysql
      WORDPRESS_DB_USER: wpuser
      WORDPRESS_DB_PASSWORD: wppass
      WORDPRESS_DB_NAME: wordpress
    volumes:
      - wp_content:/var/www/html/wp-content
    depends_on:
      - mysql
    networks:
      - wordpress-network

volumes:
  mysql_data:
  wp_content:

networks:
  wordpress-network:
```

---

## Explanation of Configuration

- `services` → Defines application containers  
- `volumes` → Ensures persistent data storage  
- `networks` → Enables communication between services  
- `depends_on` → Controls startup order  

Docker automatically creates the network and connects both containers.

---

# Commands

## Create File

```bash
bhargav@DESKTOP-QK0KB4I:/mnt/d/ccvt/sem 6/devops$ nano docker-compose.yml
```

---

## Start Containers

```bash
bhargav@DESKTOP-QK0KB4I:/mnt/d/ccvt/sem 6/devops$ docker compose up -d
```

---

## Stop and Remove

```bash
bhargav@DESKTOP-QK0KB4I:/mnt/d/ccvt/sem 6/devops$ docker compose down
```

---

## Scale Service

```bash
bhargav@DESKTOP-QK0KB4I:/mnt/d/ccvt/sem 6/devops$ docker compose up --scale wordpress=3 -d
```

---

# Port Conflict Issue

If port 8080 is already in use:

```
failed to bind host port 8080
```

### Fix

- Change port mapping (e.g., 9090:80)  
- Stop conflicting service using that port  
- Check running services:

```bash
sudo lsof -i :8080
```

---

# Access Application

```
http://localhost:8080
```

If port changed:

```
http://localhost:9090
```

---

# Scaling Issue Explanation

If you use:

```yaml
container_name: wordpress
```

Scaling will fail because container names must be unique.

### Warning Example

```bash
WARNING: The "wordpress" service is using the custom container name "wordpress".
Docker requires each container to have a unique name.
```

### Solution

Remove `container_name` from the service definition.

---

# Tear Down

```bash
bhargav@DESKTOP-QK0KB4I:/mnt/d/ccvt/sem 6/devops$ docker compose down
```

Expected Output:

```bash
✔ Container wordpress Removed
✔ Container mysql Removed
✔ Network devops_wordpress-network Removed
```

---

# Docker Compose CLI Cheat Sheet

| Command | Description |
|--------|------------|
| docker compose up -d | Launch services in background |
| docker compose down | Stop and remove all resources |
| docker compose logs -f | View live logs |
| docker compose ps | Check running containers |
| docker compose up -d --scale svc=3 | Scale a service |
| docker compose restart | Restart services |
| docker compose exec <svc> bash | Open shell inside container |
| docker compose pull | Download images |
| docker compose build | Build images |
| docker compose stop | Stop containers without removing |

---

# Additional Best Practices

- Always specify image versions in production  
- Use `.env` file for sensitive credentials  
- Avoid using `latest` tag  
- Remove unused networks and volumes regularly  
- Use restart policies (`restart: unless-stopped`)  
- Use healthchecks in production  

Example Healthcheck:

```yaml
healthcheck:
  test: ["CMD", "curl", "-f", "http://localhost"]
  interval: 30s
  timeout: 10s
  retries: 3
```

---

# Using .env File

Create `.env` file:

```
MYSQL_ROOT_PASSWORD=secret
MYSQL_USER=wpuser
MYSQL_PASSWORD=wppass
```

Then reference inside compose:

```yaml
environment:
  MYSQL_ROOT_PASSWORD: ${MYSQL_ROOT_PASSWORD}
```

---

# Summary

Docker Compose simplifies multi-container application deployment by:

- Centralizing configuration  
- Automating networking  
- Managing dependencies  
- Supporting scaling  
- Improving DevOps workflows  

It is highly recommended for local development, staging, and production environments.

---

End of File
