---
layout: default
title: Docker Networking
---

# Docker Networking (Practical + Concepts)

---

## Overview

Docker containers operate in isolated environments but still require controlled communication with:

- Other containers
- The host machine
- External networks

Docker networking provides this connectivity using different drivers and configurations.

Docker automatically manages IP addressing, DNS resolution, and routing between containers.

---

# Docker Networking Architecture

Each container gets:

- A virtual network interface
- An IP address
- A network namespace

Docker uses Linux networking components such as:

- Network namespaces
- veth pairs
- Linux bridge
- iptables

Basic flow:

```
Container → veth → Bridge → Host Interface → External Network
```

---

## Listing Available Networks

```bash
docker network ls
```

**Output:**

```bash
NETWORK ID     NAME      DRIVER    SCOPE
31616f46d165   bridge    bridge    local
a949c49a1f96   host      host      local
cea44970056d   none      null      local
```

### Explanation

Docker automatically creates three default networks:

- `bridge` → Default internal network
- `host` → Shares host networking stack
- `none` → Completely isolated (no networking)

---

## Inspect Default Bridge Network

```bash
docker network inspect bridge
```

**Output (key fields):**

```json
{
    "Name": "bridge",
    "Driver": "bridge",
    "IPAM": {
        "Config": [
            {
                "Subnet": "172.17.0.0/16",
                "Gateway": "172.17.0.1"
            }
        ]
    }
}
```

### Explanation

- Subnet: `172.17.0.0/16`
- Gateway: `172.17.0.1`
- IPAM: IP Address Management

All containers attached to this network receive IPs from this range.

---

## Create Custom Bridge Network

```bash
docker network create my_bridge
```

**Output:**

```
edabda8e9816e73a6e48c82732cfb23f3bef5dfb53c25f27434121f310821baa
```

Inspect it:

```bash
docker network inspect my_bridge
```

**Output:**

```json
{
    "Name": "my_bridge",
    "Driver": "bridge",
    "IPAM": {
        "Config": [
            {
                "Subnet": "172.18.0.0/16",
                "Gateway": "172.18.0.1"
            }
        ]
    }
}
```

### Why Use Custom Network?

- Automatic DNS resolution
- Better isolation
- Service name-based communication
- Cleaner architecture

---

## Run Containers on Custom Network

```bash
docker run -dit --name container1 --network my_bridge nginx
```

```bash
docker run -dit --name container2 --network my_bridge busybox
```

If `busybox` is not available locally, Docker downloads it automatically.

### What Happens Internally?

- Both containers connect to the same bridge
- Docker assigns IP addresses
- Internal DNS is enabled

---

## Test Communication Between Containers

```bash
docker exec -it container2 ping container1
```

**Output:**

```
PING container1 (172.18.0.2): 56 data bytes
64 bytes from 172.18.0.2: seq=0 ttl=64 time=0.397 ms
...
0% packet loss
```

### Explanation

Docker provides an embedded DNS server.  
`container1` is resolved automatically without manually using IP.

---

## Inspect Container Details

```bash
docker inspect container1
```

Displays:

- Network settings
- IP address
- MAC address
- Mounts
- Runtime configuration

---

## Extract IP Address

```bash
docker inspect container1 | grep IP
```

**Output:**

```
"IPAddress": "172.18.0.2",
```

---

## Test Network Isolation

```bash
docker exec -it container2 ping 172.17.0.2
```

**Output:**

```
100% packet loss
```

### Explanation

Different networks are isolated.  
Containers cannot communicate across networks unless explicitly connected.

---

## Connect Container to Multiple Networks

```bash
docker network connect bridge container1
```

Now container1 belongs to two networks.

Disconnect:

```bash
docker network disconnect bridge container1
```

---

## Host Network Mode

```bash
docker run -d --network host nginx
```

In host mode:

- No network isolation
- No port mapping required
- Uses host IP directly

Check open ports:

```bash
ss -tulnp | grep 80
```

---

## None Network Mode

```bash
docker run -dit --network none nginx
```

Container has no external connectivity.

Useful for:

- Security testing
- Strict isolation
- Controlled environments

---

# Overlay Network (Swarm Mode)

Used in Docker Swarm.

```bash
docker network create --driver overlay my_overlay
```

Allows containers across multiple hosts to communicate.

---

# MACVLAN vs IPVLAN

| Feature     | MACVLAN              | IPVLAN            |
|-------------|----------------------|-------------------|
| MAC Address | Unique per container | Shared            |
| Performance | Moderate             | High              |
| Scalability | Limited              | Large scale       |
| Use Case    | Small setups         | Enterprise setups |

---

# Advanced Networking Commands

List container IP directly:

```bash
docker inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' container1
```

View detailed network stats:

```bash
docker network inspect my_bridge
```

Remove network:

```bash
docker network rm my_bridge
```

---

# Troubleshooting Networking Issues

Check port usage:

```bash
sudo lsof -i :8080
```

Restart Docker:

```bash
sudo systemctl restart docker
```

Check firewall rules:

```bash
sudo iptables -L
```

---

# WSL Command Execution

### List Available Networks

```bash
bhargav@DESKTOP-QK0KB4I:~$ docker network ls
```

---

### Inspect Default Bridge

```bash
bhargav@DESKTOP-QK0KB4I:~$ docker network inspect bridge
```

---

### Create Custom Network

```bash
bhargav@DESKTOP-QK0KB4I:~$ docker network create my_bridge
```

---

### Run Containers

```bash
bhargav@DESKTOP-QK0KB4I:~$ docker run -dit --name container1 --network my_bridge nginx
bhargav@DESKTOP-QK0KB4I:~$ docker run -dit --name container2 --network my_bridge busybox
```

---

### Test Connectivity

```bash
bhargav@DESKTOP-QK0KB4I:~$ docker exec -it container2 ping container1
```

---

### Test Isolation

```bash
bhargav@DESKTOP-QK0KB4I:~$ docker exec -it container2 ping 172.17.0.2
```

---

### Host Network Mode

```bash
bhargav@DESKTOP-QK0KB4I:~$ docker run -d --network host nginx
```

---

# Best Practices

- Use custom networks for multi-container apps
- Avoid exposing unnecessary ports
- Use bridge for development
- Use overlay for distributed systems
- Avoid host mode in production unless necessary
- Monitor network traffic in production

---

# Summary

Docker networking enables flexible communication models:

- Bridge → default and most used
- Custom bridge → better DNS and isolation
- Host → no isolation, high performance
- Overlay → multi-host communication
- MACVLAN/IPVLAN → advanced enterprise networking

Understanding Docker networking is critical for designing secure, scalable, and production-ready containerized systems.

---

End of File
