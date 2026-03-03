---
layout: default
title: Docker Swarm
---

# Docker Swarm 

## Introduction

Docker Swarm is Docker’s native clustering and orchestration tool. It allows multiple Docker hosts (nodes) to work together as a single system. Swarm helps in deploying, managing, and scaling containerized applications across multiple machines.

---

## Key Concepts

- **Node**: A machine participating in the swarm (manager or worker)  
- **Manager Node**: Controls the cluster and schedules tasks  
- **Worker Node**: Executes the tasks assigned by the manager  
- **Service**: Defines how containers should run (replicas, image, ports)  
- **Task**: A running container that is part of a service  
- **Overlay Network**: Enables communication between containers across nodes  

---

## Environment Details

| Property | Value |
|---|---|
| Host | Asher (WSL2 / Ubuntu) |
| Docker Engine Version | 29.2.1 |
| Swarm Advertise Address | 172.22.216.77 |
| Service Port | 8080 → 80 |
| Date | February 28, 2026 |

---

## Step-by-Step Procedure

### Initialize Docker Swarm

```bash
aryan_1234@Asher:~$ docker swarm init
```

**Output:**
```bash
Error response from daemon: could not choose an IP address to advertise since this system has multiple addresses on different interfaces (10.255.255.254 on lo and 172.22.216.77 on eth0)
```

**Explanation:**  
Docker could not automatically select the correct IP address due to multiple interfaces.

---

### Check Available IP Addresses

```bash
aryan_1234@Asher:~$ ip a
```

**Output:**
```bash
# Shows all network interfaces including eth0 and docker networks
```

---

### Initialize Swarm with Specific IP

```bash
aryan_1234@Asher:~$ docker swarm init --advertise-addr 172.22.216.77
```

**Output:**
```bash
Swarm initialized: current node (rpbqpmd5nhm34e55zy7dwz7qb) is now a manager.
```

**Explanation:**  
The manager node is successfully created and ready to manage the cluster.

---

### Verify Node Status

```bash
aryan_1234@Asher:~$ docker node ls
```

**Output:**
```bash
ID                            HOSTNAME   STATUS    AVAILABILITY   MANAGER STATUS   ENGINE VERSION
rpbqpmd5nhm34e55zy7dwz7qb *   Asher      Ready     Active         Leader           29.2.1
```

---

### Attempt to Join Swarm (Error Case)

```bash
aryan_1234@Asher:~$ docker swarm join --token <TOKEN> <MANAGER-IP>:2377
```

**Output:**
```bash
-bash: syntax error near unexpected token `<'
```

**Explanation:**  
Placeholders must be replaced with actual token and IP values.

---

### Join Swarm (Already Part of Swarm)

```bash
aryan_1234@Asher:~$ docker swarm join --token SWMTKN-... 172.22.216.77:2377
```

**Output:**
```bash
Error response from daemon: This node is already part of a swarm.
```

---

### Create Worker Node

```bash
aryan_1234@Asher:~$ docker run -d --privileged --name worker1 docker:dind
```

**Output:**
```bash
# Container ID displayed
```

---

### Enter Worker Container

```bash
aryan_1234@Asher:~$ docker exec -it worker1 sh
```

**Output:**
```bash
/ #
```

---

### Join Worker to Swarm

```bash
/ # docker swarm join --token SWMTKN-... 172.22.216.77:2377
```

**Output:**
```bash
This node joined a swarm as a worker.
```

---

### Verify Nodes

```bash
aryan_1234@Asher:~$ docker node ls
```

**Output:**
```bash
ID                            HOSTNAME       STATUS    AVAILABILITY   MANAGER STATUS
wlbfk0fre8qy7l8l6rg6ygh89     worker1        Ready     Active
rpbqpmd5nhm34e55zy7dwz7qb *   Asher          Ready     Active         Leader
```

---

### Create Service

```bash
aryan_1234@Asher:~$ docker service create \
  --name webapp \
  --replicas 3 \
  -p 8080:80 \
  nginx
```

**Output:**
```bash
eefcgssqr6tdjtxv6b9hqa9uz
overall progress: 3 out of 3 tasks
```

**Explanation:**  
Creates a service with 3 replicas. Swarm ensures all replicas remain running.

---

### List Services

```bash
aryan_1234@Asher:~$ docker service ls
```

**Output:**
```bash
ID             NAME      MODE         REPLICAS   IMAGE          PORTS
eefcgssqr6td   webapp    replicated   3/3        nginx:latest   *:8080->80/tcp
```

---

### Inspect Tasks

```bash
aryan_1234@Asher:~$ docker service ps webapp
```

**Output:**
```bash
# Shows container distribution across nodes
```

---

### Inspect Service

```bash
aryan_1234@Asher:~$ docker service inspect webapp
```

**Output:**
```bash
# Detailed JSON configuration
```

---

### Scale Service

```bash
aryan_1234@Asher:~$ docker service scale webapp=6
```

**Output:**
```bash
webapp scaled to 6
overall progress: 6 out of 6 tasks
```

**Explanation:**  
Scaling increases the number of running containers without downtime.

---

### Remove Service

```bash
aryan_1234@Asher:~$ docker service rm webapp
```

**Output:**
```bash
webapp
```

---

### Rolling Update

```bash
aryan_1234@Asher:~$ docker service update \
  --image nginx:latest \
  --update-parallelism 1 \
  --update-delay 10s \
  app
```

**Output:**
```bash
# Rolling update in progress
```

**Explanation:**  
Containers are updated one by one to avoid downtime.

---

## Advantages of Docker Swarm

- Easy to set up compared to other orchestration tools  
- Built-in load balancing  
- Supports scaling and rolling updates  
- High availability with multiple nodes  
- Native Docker integration  

---

## Conclusion

Docker Swarm simplifies container orchestration by managing multiple nodes as a single system. It ensures application availability, scalability, and efficient resource utilization.