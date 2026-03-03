---
layout: default
title: Docker Compose
---

# Docker Compose

## 1. Concept: Docker Compose vs Docker Run

Docker Compose is used to define and manage applications that require multiple containers. Instead of running containers one by one, everything is described in a single YAML file (`docker-compose.yml`) and started together.

### Docker Run

`docker run` is used to launch a single container. All configurations like ports, volumes, environment variables, etc., must be provided manually every time.

Example:

```bash
docker run -d \
  --name my-nginx \
  -p 8080:80 \
  -v ./html:/usr/share/nginx/html \
  -e NGINX_HOST=localhost \
  --restart unless-stopped \
  nginx:alpine
```

### Docker Compose

Docker Compose follows a declarative approach where you define the configuration once in a file.

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

---

## Advantages of Docker Compose

- Single command to start complete application  
- Same configuration works on all systems  
- Easier to manage complex setups  
- Built-in lifecycle management  
- Supports dependency handling  
- Allows scaling of services  

---

## Mapping: docker run vs docker-compose

| docker run | docker-compose.yml | Purpose |
|-----------|------------------|--------|
| -p 8080:80 | ports | Port mapping |
| -v ./data:/app | volumes | Storage |
| -e KEY=value | environment | Env variables |
| --name app | container_name | Naming |
| --network net | networks | Networking |
| --restart always | restart | Restart policy |
| -d | compose up -d | Detached mode |
| --link db | links | Legacy linking |
| N/A | depends_on | Startup order |
| -w /app | working_dir | Working directory |
| --user 1000 | user | Run as user |

---

## 2. Practical: WordPress + MySQL

### docker-compose.yml

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

## Commands

### Create File

```bash
nano docker-compose.yml
```

---

### Start Containers

```bash
docker compose up -d
```

---

### Stop and Remove

```bash
docker compose down
```

---

### Scale Service

```bash
docker compose up --scale wordpress=3 -d
```

---

## Port Conflict Issue

Error occurs when a port is already in use:

```
failed to bind host port 8080
```

### Fix

- Change port (e.g., 9090:80)  
- OR stop conflicting service  

---

## Access Application

```
http://localhost:9090
```

---

## 3. Docker Compose Commands Cheat Sheet

| Command | Description |
|--------|------------|
| docker compose up -d | Start services |
| docker compose down | Stop & remove |
| docker compose logs -f | View logs |
| docker compose ps | List containers |
| docker compose restart | Restart services |
| docker compose exec svc bash | Open shell |
| docker compose pull | Download images |
| docker compose build | Build images |
| docker compose stop | Stop containers |

---

## WSL Command Execution

```bash
aryan_1234@Asher:/mnt/d/ccvt/sem 6/devops$ nano docker-compose.yml
aryan_1234@Asher:/mnt/d/ccvt/sem 6/devops$ docker compose up -d
WARN[0000] /mnt/d/ccvt/sem 6/devops/docker-compose.yml: the attribute `version` is obsolete, it will be ignored
Error: failed to bind port 8080
```

```bash
aryan_1234@Asher:/mnt/d/ccvt/sem 6/devops$ docker compose down
```

```bash
aryan_1234@Asher:/mnt/d/ccvt/sem 6/devops$ docker compose up -d
```

```bash
aryan_1234@Asher:/mnt/d/ccvt/sem 6/devops$ docker compose up --scale wordpress=3 -d
WARNING: container name conflict
```

```bash
aryan_1234@Asher:/mnt/d/ccvt/sem 6/devops$ docker compose down
```
## Attempt Service Scaling

```bash
docker compose up --scale wordpress=3 -d
```

**Command Description:**  
This command tries to create multiple instances (replicas) of a service. It is commonly used for scaling applications for load handling or testing distributed setups.

---

### Warning Observed

```bash
docker compose up --scale wordpress=3 -d
WARN[0000] /mnt/d/ccvt/sem 6/devops/docker-compose.yml: the attribute `version` is obsolete, it will be ignored, please remove it to avoid potential confusion
[+] up 1/1
 ✔ Container mysql Running                                              0.0s
WARNING: The "wordpress" service is using the custom container name "wordpress". Docker requires each container to have a unique name. Remove the custom name to scale the service
```

**Explanation:**  
The issue occurs because the `container_name: wordpress` field forces all replicas to use the same name. Since container names must be unique, Docker cannot create multiple instances.  
To fix this, remove the `container_name` field so Docker can auto-generate unique names.

---

## Tear Down

```bash
docker compose down
```

This command stops and removes all containers, networks, and related resources created by the compose file.

**Expected Output:**
```bash
✔ Container wordpress Removed 1.5s
✔ Container mysql Removed 1.8s
✔ Network devops_wordpress-network Removed 0.3s
```

---

## Docker Compose CLI Cheat Sheet

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

## WSL Command Execution

```bash
aryan_1234@Asher:/mnt/d/ccvt/sem 6/devops$ nano docker-compose.yml
aryan_1234@Asher:/mnt/d/ccvt/sem 6/devops$ docker compose up -d
WARN[0000] /mnt/d/ccvt/sem 6/devops/docker-compose.yml: the attribute `version` is obsolete, it will be ignored, please remove it to avoid potential confusion
[+] up 3/3
 ✔ Network devops_wordpress-network Created                             0.1s
 ✔ Container mysql                  Created                             0.2s
 ✔ Container wordpress              Created                             0.1s
Error response from daemon: failed to set up container networking: driver failed programming external connectivity on endpoint wordpress (426628fa36705fa2a82e5b8ec5b3cc75293e1500dd2d2ccc1caa2d259ff2a041): failed to bind host port 0.0.0.0:8080/tcp: address already in use

aryan_1234@Asher:/mnt/d/ccvt/sem 6/devops$ nano docker-compose.yml

aryan_1234@Asher:/mnt/d/ccvt/sem 6/devops$ docker compose down
WARN[0000] /mnt/d/ccvt/sem 6/devops/docker-compose.yml: the attribute `version` is obsolete, it will be ignored, please remove it to avoid potential confusion
[+] down 3/3
 ✔ Container wordpress              Removed                             0.1s
 ✔ Container mysql                  Removed                             2.2s
 ✔ Network devops_wordpress-network Removed                             0.3s

aryan_1234@Asher:/mnt/d/ccvt/sem 6/devops$ docker compose up -d
WARN[0000] /mnt/d/ccvt/sem 6/devops/docker-compose.yml: the attribute `version` is obsolete, it will be ignored, please remove it to avoid potential confusion
[+] up 3/3
 ✔ Network devops_wordpress-network Created                             0.0s
 ✔ Container mysql                  Created                             0.1s
 ✔ Container wordpress              Created                             0.1s

aryan_1234@Asher:/mnt/d/ccvt/sem 6/devops$ docker compose up --scale wordpress=3 -d
WARN[0000] /mnt/d/ccvt/sem 6/devops/docker-compose.yml: the attribute `version` is obsolete, it will be ignored, please remove it to avoid potential confusion
[+] up 1/1
 ✔ Container mysql Running                                              0.0s
WARNING: The "wordpress" service is using the custom container name "wordpress". Docker requires each container to have a unique name. Remove the custom name to scale the service

aryan_1234@Asher:/mnt/d/ccvt/sem 6/devops$ docker compose down
WARN[0000] /mnt/d/ccvt/sem 6/devops/docker-compose.yml: the attribute `version` is obsolete, it will be ignored, please remove it to avoid potential confusion
[+] down 3/3
 ✔ Container wordpress              Removed                             1.5s
 ✔ Container mysql                  Removed                             1.8s
 ✔ Network devops_wordpress-network Removed                             0.3s
```