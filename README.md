# Containerization and DevOps Lab

This repository contains structured notes, commands, and practical implementations for Containerization and DevOps using Docker.

The content is organized topic-wise with separate markdown files for each concept, including commands, explanations, and outputs.

---

## Student Information

**Name:** Bhargav Das  
**SAP ID:** 500119086  
**Batch:** 4  

---

# Overview

This repository serves as a comprehensive practical lab documentation for learning Docker and containerization concepts in a structured manner.

It covers:

- Docker fundamentals  
- Storage management  
- Networking  
- Multi-container deployments  
- Container orchestration  

Each topic includes:

- Concept explanations  
- Command usage  
- Real execution outputs  
- Troubleshooting cases  
- Best practices  

---

# Repository Structure

## Docker Fundamentals

Core Docker commands used to manage containers and images.

- [Docker Basic Commands](./Basic%20Docker%20Commands.md)

Topics covered:
- Image management  
- Container lifecycle  
- Logs and monitoring  
- Cleanup commands  

---

## Docker Storage

Persistent storage options in Docker.

- [Docker Volumes](./Volumes.md)

Topics covered:
- Named volumes  
- Anonymous volumes  
- Bind mounts  
- tmpfs mounts  
- Volume backup & restore  
- Storage comparison  

---

## Docker Networking

Container communication and isolation mechanisms.

- [Docker Networking](./Networking.md)

Topics covered:
- Bridge network  
- Custom networks  
- Host mode  
- Network isolation  
- Overlay networks  
- MACVLAN & IPVLAN  

---

## Docker Compose

Multi-container application deployment.

- [Docker Compose](./Compose.md)

Topics covered:
- docker run vs docker compose  
- Service configuration  
- Scaling  
- Port conflicts  
- Environment variables  
- Health checks  

---

## Docker Swarm

Container orchestration and clustering.

- [Docker Swarm](./Swarm.md)

Topics covered:
- Manager and worker nodes  
- Swarm initialization  
- Service deployment  
- Scaling  
- Rolling updates  
- Routing mesh  

---

# Learning Outcomes

After completing this lab, you will understand:

- How Docker works internally  
- How to manage container lifecycle  
- How to implement persistent storage  
- How networking isolation works  
- How to deploy multi-container applications  
- How to scale applications using Swarm  

---

# Tools Used

- Docker Engine 29.2.1  
- Ubuntu (WSL2)  
- Docker Compose  
- Docker Swarm  

---

# Practical Approach

All commands in this repository were executed in a real environment.  
Outputs included are actual execution results for better understanding.

This ensures:

- Hands-on clarity  
- Real troubleshooting exposure  
- Practical DevOps workflow experience  

---

# Best Practices Followed

- Use of named volumes for persistence  
- Custom networks for isolation  
- Avoid using `latest` in production examples  
- Proper scaling methods  
- Rolling updates in Swarm  
- Clean teardown of services  

---

# Conclusion

This repository provides a structured and practical approach to learning Docker and DevOps fundamentals.

It serves as:

- A lab manual  
- A revision guide  
- A practical reference  
- A DevOps portfolio project  

---

End of Repository Documentation
