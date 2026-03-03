---
layout: default
title: Docker Networking
---

# Docker Networking (Practical + Concepts)

---

## Overview

Docker containers operate in isolated environments but still need controlled communication with each other, the host system, and external networks. Docker networking provides this connectivity using different drivers and configurations.

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

**Explanation:**
Docker automatically creates three default networks:

* `bridge` → default internal network
* `host` → shares host networking
* `none` → completely isolated

---

## Inspect Default Bridge Network

```bash
docker network inspect bridge
```

**Output (key fields):**

```bash
    json
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

**Explanation:**
The default bridge network uses subnet `172.17.0.0/16`. All containers attached here get IPs from this range. Communication happens via the gateway `172.17.0.1`.

---

## Create Custom Bridge Network

```bash
docker network create my_bridge
```

**Output:**

```
edabda8e9816e73a6e48c82732cfb23f3bef5dfb53c25f27434121f310821baa
```

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

**Explanation:**
Docker assigns a new subnet (`172.18.0.0/16`) to avoid overlap. Custom bridge networks allow containers to communicate using names instead of IPs.

---

## Run Containers on Custom Network

```bash
docker run -dit --name container1 --network my_bridge nginx
```

**Output:**

```
33073f88712c85a53336a0f61d3d4de085d7ea76445654e6081535fd51d4db64
```

```bash
docker run -dit --name container2 --network my_bridge busybox
```

**Output:**

```
Unable to find image 'busybox:latest' locally
latest: Pulling from library/busybox
61dfb50712f5: Pull complete
96cfb76e59bd: Download complete
Digest: sha256:b3255e7dfbcd10cb367af0d409747d511aeb66dfac98cf30e97e87e4207dd76f
Status: Downloaded newer image for busybox:latest
d525cc4a02bbee90c344acd1992c5ef81d6f005ff4c82d76600659792b486862
```

**Explanation:**
Two containers are launched and connected to the same network. Since `busybox` was not present locally, Docker fetched it from Docker Hub.

---

## Test Communication Between Containers

```bash
docker exec -it container2 ping container1
```

**Output:**

```
PING container1 (172.18.0.2): 56 data bytes
64 bytes from 172.18.0.2: seq=0 ttl=64 time=0.397 ms
64 bytes from 172.18.0.2: seq=1 ttl=64 time=0.145 ms
...
44 packets transmitted, 44 packets received, 0% packet loss
round-trip min/avg/max = 0.070/0.112/0.397 ms
```

**Explanation:**
The ping command is executed inside `container2`. Docker resolves `container1` automatically via internal DNS, confirming successful communication.

---

## Inspect Container Details

```bash
docker inspect container1
```

**(Output same as provided)**

**Explanation:**
This command returns complete configuration details of the container, including runtime state and networking information.

---

## Extract IP Address

```bash
docker inspect container1 | grep IP
```

**Output:**

```
"IPAMConfig": null,
"IPAddress": "172.18.0.2",
"IPPrefixLen": 16,
```

**Explanation:**
This filters out only IP-related details, making it easier to identify the container’s assigned address.

---

## Test Network Isolation

```bash
docker exec -it container2 ping 172.17.0.2
```

**Output:**

```
PING 172.17.0.2 (172.17.0.2): 56 data bytes
--- 172.17.0.2 ping statistics ---
13 packets transmitted, 0 packets received, 100% packet loss
```

**Explanation:**
Since the IP belongs to a different network, communication fails. This confirms Docker’s network isolation feature.

---

## Host Network Mode

```bash
docker run -d --network host nginx
```

**Output:**

```
2a2b1405645cab1c4085613d4863b7826526058f45b7212ee0272201f3104b83
```

```bash
ss -tulnp | grep 80
```

**Output:**

```
tcp   LISTEN 0      100           0.0.0.0:6080       0.0.0.0:*    
tcp   LISTEN 0      511                 *:80               *:*
tcp   LISTEN 0      4096                *:2380             *:*
```

**Explanation:**
In host mode, the container directly uses the host’s networking stack. Services run without port mapping and are accessible on host ports.

---

## Key Observations

* Containers on the same custom network can communicate using names
* Different networks remain isolated
* Docker assigns unique subnets automatically
* Host networking removes isolation completely

---

## MACVLAN vs IPVLAN

| Feature     | MACVLAN              | IPVLAN            |
| ----------- | -------------------- | ----------------- |
| MAC Address | Unique per container | Shared            |
| Load        | Higher               | Lower             |
| Scalability | Limited              | High              |
| Use Case    | Small setups         | Large deployments |

---
## WSL Command Execution

### List Available Networks

```bash
aryan_1234@Asher:~$ docker network ls
NETWORK ID     NAME      DRIVER    SCOPE
31616f46d165   bridge    bridge    local
a949c49a1f96   host      host      local
cea44970056d   none      null      local
```

---

### Inspect Default Bridge Network

```bash
aryan_1234@Asher:~$ docker network inspect bridge
[
    {
        "Name": "bridge",
        "Id": "31616f46d165049ee9c62fd82cea0536491313c7a4bd9b63858b1849a333b663",
        "Created": "2026-02-18T14:38:09.64660814+05:30",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv4": true,
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": null,
            "Config": [
                {
                    "Subnet": "172.17.0.0/16",
                    "IPRange": "",
                    "Gateway": "172.17.0.1"
                }
            ]
        },
        ...
    }
]
```

---

### Create a Custom Network

```bash
aryan_1234@Asher:~$ docker network create my_bridge
edabda8e9816e73a6e48c82732cfb23f3bef5dfb53c25f27434121f310821baa
```

---

### Inspect Custom Network

```bash
aryan_1234@Asher:~$ docker network inspect my_bridge
[
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
]
```

---

### Run First Container (nginx)

```bash
aryan_1234@Asher:~$ docker run -dit --name container1 --network my_bridge nginx
33073f88712c85a53336a0f61d3d4de085d7ea76445654e6081535fd51d4db64
```

---

### Run Second Container (busybox)

```bash
aryan_1234@Asher:~$ docker run -dit --name container2 --network my_bridge busybox
Unable to find image 'busybox:latest' locally
latest: Pulling from library/busybox
...
Status: Downloaded newer image for busybox:latest
d525cc4a02bbee90c344acd1992c5ef81d6f005ff4c82d76600659792b486862
```

---

### Test Connectivity Between Containers

```bash
aryan_1234@Asher:~$ docker exec -it container2 ping container1
PING container1 (172.18.0.2): 56 data bytes
64 bytes from 172.18.0.2: seq=0 ttl=64 time=0.397 ms
...
44 packets transmitted, 44 packets received, 0% packet loss
```

---

### Inspect Container Details

```bash
aryan_1234@Asher:~$ docker inspect container1
[ ... full container details ... ]
```

---

### Extract IP Information

```bash
aryan_1234@Asher:~$ docker inspect container1 | grep IP
"IPAddress": "172.18.0.2",
```

---

### Test Network Isolation

```bash
aryan_1234@Asher:~$ docker exec -it container2 ping 172.17.0.2
PING 172.17.0.2 (172.17.0.2): 56 data bytes
--- 172.17.0.2 ping statistics ---
13 packets transmitted, 0 packets received, 100% packet loss
```

---

### Run Container in Host Network Mode

```bash
aryan_1234@Asher:~$ docker run -d --network host nginx
2a2b1405645cab1c4085613d4863b7826526058f45b7212ee0272201f3104b83
```

---

### Check Open Ports

```bash
aryan_1234@Asher:~$ ss -tulnp | grep 80
tcp   LISTEN 0      100           0.0.0.0:6080       0.0.0.0:*
tcp   LISTEN 0      511                 *:80               *:*
tcp   LISTEN 0      4096                *:2380             *:*
```

## Summary

Docker networking enables flexible communication models:

* Bridge → default and most used
* Custom bridge → better DNS and isolation
* Host → no isolation, high performance
* Other drivers → specialized use cases
