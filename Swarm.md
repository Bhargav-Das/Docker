---
layout: default
title: Docker Swarm
---

# Docker Swarm

---

## Introduction

Docker Swarm is Docker’s native clustering and orchestration tool. It allows multiple Docker hosts (nodes) to work together as a single logical cluster.

Swarm enables:

- Service orchestration
- High availability
- Load balancing
- Rolling updates
- Horizontal scaling

It transforms standalone Docker hosts into a distributed container platform.

---

# Swarm Architecture

Docker Swarm follows a manager-worker architecture.

### Components

- **Node** → A machine participating in the swarm  
- **Manager Node** → Maintains cluster state and schedules tasks  
- **Worker Node** → Executes assigned tasks  
- **Service** → Definition of container deployment  
- **Task** → Individual running container  
- **Overlay Network** → Enables communication across nodes  
- **Ingress Network** → Handles load-balanced external access  

---

## Raft Consensus (Cluster State Management)

Manager nodes use the **Raft consensus algorithm** to maintain cluster state.

Key properties:

- Strong consistency
- Leader election
- Distributed state replication
- Fault tolerance

For production:

- Always use odd number of managers (3, 5, 7)

---

# Environment Details

| Property | Value |
|---|---|
| Host | DESKTOP-QK0KB4I (WSL2 / Ubuntu) |
| Docker Engine Version | 29.2.1 |
| Swarm Advertise Address | 172.22.216.77 |
| Service Port | 8080 → 80 |
| Date | February 28, 2026 |

---

# Step-by-Step Procedure

---

## Initialize Docker Swarm

```bash
bhargav@DESKTOP-QK0KB4I:~$ docker swarm init
```

### Error Output

```bash
Error response from daemon: could not choose an IP address to advertise since this system has multiple addresses on different interfaces
```

### Explanation

When multiple network interfaces exist, Docker cannot automatically determine the correct advertise IP.

---

## Check Available IP Addresses

```bash
bhargav@DESKTOP-QK0KB4I:~$ ip a
```

Identify the correct interface (e.g., `eth0`) and its IP address.

---

## Initialize Swarm with Specific IP

```bash
bhargav@DESKTOP-QK0KB4I:~$ docker swarm init --advertise-addr 172.22.216.77
```

### Output

```bash
Swarm initialized: current node (...) is now a manager.
```

The node becomes the **Leader Manager**.

---

## Verify Node Status

```bash
bhargav@DESKTOP-QK0KB4I:~$ docker node ls
```

### Output

```bash
ID                            HOSTNAME              STATUS    AVAILABILITY   MANAGER STATUS   ENGINE VERSION
rpbqpmd5nhm34e55zy7dwz7qb *   DESKTOP-QK0KB4I      Ready     Active         Leader           29.2.1
```

---

## Retrieve Join Token

```bash
bhargav@DESKTOP-QK0KB4I:~$ docker swarm join-token worker
```

Displays worker join command.

---

## Create Worker Node (Using Docker-in-Docker)

```bash
bhargav@DESKTOP-QK0KB4I:~$ docker run -d --privileged --name worker1 docker:dind
```

Enter container:

```bash
bhargav@DESKTOP-QK0KB4I:~$ docker exec -it worker1 sh
```

Join swarm:

```bash
/ # docker swarm join --token SWMTKN-... 172.22.216.77:2377
```

---

## Verify All Nodes

```bash
bhargav@DESKTOP-QK0KB4I:~$ docker node ls
```

You should see both manager and worker.

---

# Create Service

```bash
bhargav@DESKTOP-QK0KB4I:~$ docker service create \
  --name webapp \
  --replicas 3 \
  -p 8080:80 \
  nginx
```

### What Happens Internally

- Swarm creates 3 tasks
- Tasks distributed across nodes
- Ingress routing mesh enabled
- Load balancing activated

---

## Routing Mesh

Docker Swarm automatically enables **routing mesh**, meaning:

- Every node listens on published port
- Traffic routed to available containers
- Built-in load balancing

---

## List Services

```bash
bhargav@DESKTOP-QK0KB4I:~$ docker service ls
```

---

## Inspect Tasks

```bash
bhargav@DESKTOP-QK0KB4I:~$ docker service ps webapp
```

Shows distribution across nodes.

---

## Inspect Service Configuration

```bash
bhargav@DESKTOP-QK0KB4I:~$ docker service inspect webapp
```

Displays full JSON configuration.

---

# Scaling Service

```bash
bhargav@DESKTOP-QK0KB4I:~$ docker service scale webapp=6
```

Swarm automatically schedules new tasks.

---

# Service Deployment Modes

| Mode | Description |
|------|------------|
| Replicated | Runs defined number of replicas |
| Global | Runs one container per node |

Example (global):

```bash
docker service create --mode global nginx
```

---

# Rolling Update

```bash
bhargav@DESKTOP-QK0KB4I:~$ docker service update \
  --image nginx:latest \
  --update-parallelism 1 \
  --update-delay 10s \
  webapp
```

This updates containers gradually to avoid downtime.

---

# Remove Service

```bash
bhargav@DESKTOP-QK0KB4I:~$ docker service rm webapp
```

---

# Node Availability Management

Drain a node:

```bash
docker node update --availability drain worker1
```

Active mode:

```bash
docker node update --availability active worker1
```

---

# Leave Swarm

Worker:

```bash
docker swarm leave
```

Manager:

```bash
docker swarm leave --force
```

---

# Troubleshooting Swarm

Check swarm status:

```bash
docker info | grep Swarm
```

Restart Docker:

```bash
sudo systemctl restart docker
```

Check logs:

```bash
journalctl -u docker
```

---

# Swarm Networking

Swarm automatically creates:

- ingress network
- docker_gwbridge
- overlay networks

Create custom overlay:

```bash
docker network create --driver overlay my_overlay
```

---

# Advantages of Docker Swarm

- Native Docker integration
- Easy setup
- Automatic load balancing
- Built-in service discovery
- Rolling updates
- Horizontal scaling
- Self-healing containers

---

# Limitations

- Less powerful than Kubernetes
- Smaller ecosystem
- Limited advanced scheduling policies

---

# Best Practices

- Use multiple manager nodes in production
- Use overlay networks
- Enable TLS security
- Monitor service health
- Avoid single-manager production setups
- Regularly backup swarm state

---

# Command Cheat Sheet

| Command | Purpose |
|----------|---------|
| docker swarm init | Initialize swarm |
| docker node ls | List nodes |
| docker service create | Create service |
| docker service ls | List services |
| docker service ps | Inspect tasks |
| docker service scale | Scale service |
| docker service update | Rolling update |
| docker service rm | Remove service |
| docker swarm leave | Leave swarm |

---

# Conclusion

Docker Swarm provides a simple yet powerful orchestration platform for managing distributed containerized applications.

It ensures:

- High availability
- Automatic scaling
- Load balancing
- Efficient resource utilization

Swarm is ideal for small-to-medium scale distributed deployments and is tightly integrated with the Docker ecosystem.

---

End of File
