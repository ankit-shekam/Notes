[back to previous file](../README.md)

---

# Docker topics:


### Section-1 : Docker Deep Dive

1. [Docker Basics](./section-1/docker-basics.md) : what/Why Docker, What/Why Container, DockerDesktop
2. [Docker command flow](./section-1/dockercommandflow.md) : basic flow of a docker Command
3. [Docker Container details](./section-1/container.md) : OS resources patitioning techniques and Container, LINUX VM on host OS, behind the scenes of Container creation 

--- 

### Section-2 : Manupulating Container using Docker Client

- [Docker Basic Commands](./section-2/basic-commands.md) : 
   1. create and run containers from Images
   2. overwrite default command in images
   3. list running containers
   4. clean local resources
   5. get LOGS from container
   6. stopping containers
   7. executing commands inside a running container
   8. Get shell access inside a running container 


---

### section-3 :  Building custom Images

- [what is a dockerfile](./section-3/about-dockerfile.md)
- [Building Custom Images](./section-3/building-custom-images.md) : 
   1. Creating Docker Images using dockerfile
   2. internal caching of docker build process
   3. tagging images
   4. creating image from Container

--- 

### section-4 : building a real-world project using Docker

- [simple web app with Docker](./section-4/real-world-docker-project.md)
   1. Dockerfile for creating container with Node inbuilt, `COPY` instruction
   2. port mapping for incoming traffic to containerised server
   3. changing working directory for container, `WORKDIR` instruction
   4. Using docker build caching properly to improve performance

--- 

### section-5 : Docker-compose - multi-container applications