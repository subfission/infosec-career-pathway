# DOCKER CHEAT SHEET
## Create Dockerfiles

To build Docker images, you first need a Dockerfile. A Dockerfile is a text file named Dockerfile and has no file extension.

    FROM <baseimage> # get them from https://hub.docker.com 
    
    # Set the working directory inside the image 
    WORKDIR /app 
    
    # Copy the source code from the host machine to the image (host: your development machine or your server).
    # The first dot (.) is the current working directory (CWD) of the host, the second dot (.) is the CWD of the image.
    COPY . . 
    
    # Run any shell command inside the image 
    RUN <command> # e.g., [npm run build]
    
    # This defines which ports can be accessed outside the docker internel network.
    EXPOSE 80 # we expose ports with the --publish flag
    
    # This command gets executed when the container starts 
    CMD <command> # e.g., [”node”, “server.js”]

## Create Multistage Dockerfiles

Multistage Dockerfiles are used to optimize Dockerfiles. One use case is to create a builder and a serve stage with separate base images. 
This strategy can be used to make the final Image smaller and have a lower attack because it has less system libraries. Each stage starts with FROM. 

    # With "as", you can give the current stage a variable name.
    FROM <baseimage> as builder
    
    # Perform an action, install dependencies, compile the code
    RUN <command> # e.g., g++ main.cpp

    # The second stage could use a smaller image. Smaller images are typically based on alpine or you can build from the scratch docker image if you do not need any system libraries.
    FROM scratch as serve

    # You can now copy files from the builder stage e.g., the binary file that you build in that stage 
    COPY --from=builder ./hello-world ./hello-world

    # This command gets executed when the container starts
    CMD [”./hello-world”]

## Create Docker Images

    # <path> sets the context for the docker build command. If set to dot (.) it will use the CWD of the hostmachine to find the Dockerfile 
    $ docker build <path>

    # If the Dockerfile is located somewhere else or the Dockerfile is named differently use "--file". Remember docker will still use the <path> as context 
    $ docker build --file ./path/to/Dockerfile ./path/to/context

    # name images: <image-name> equals <name>:<tag> where 
    # name is separated into <username>/<repository>:<version> 
    $ docker build --tag user/repo:0.1.0 .

    # list all images
    $ docker image ls 

## Create Docker Containers

    # Docker containers are running images 
    $ docker run <image-name>

    # You can run public images from Docker Hub or images from a private registry.
    $ docker run https://privateregistry.com/<image-name>

    # Containers are started in the foreground. As soon as the process terminates, e.g., the terminal is closed, it will stop the container. To run a container in the background, you need to run it in detached mode.
    $ docker run --detached <image-name>

    # list all containers 
    $ docker container ls

    # List containers using shorthand syntax 
    $ docker ps

    # List all containers, including stopped ones 
    $ docker container ls --all

    # Stop a container 
    $ docker stop <container-id>

    # Remove a container (only stopped containers can be removed).
    $ docker rm <container-id> 
    
    # Start a stopped container
    $ docker start <container-id> 
    
    # Restart a running container
    $ docker restart <container-id>

    # Automatically remove a container when it is stopped
    $ docker run --rm <image-name>

## Access Docker Containers

    # Publish ports, e.g., forward container port to host port 
    $ docker run --publish <host-port>:<container-port> <image-name> 
    
    # Execute shell command in a container 
    $ docker exec --interactive --tty <container-id> <command> 
    
    # Open an interactive shell (like connecting to a server)
    $ docker exec --interactive --tty <container-id> sh 
    
    # Exit the container
    $ exit

## Create Docker Volumes

To persist data from Docker containers between starts of containers you need volumes. When a container is removed all data from the container will be lost if you do not use volumes.

    # Using a named volume (docker handles location on host) 
    $ docker run --volume <volume-name>:/path/in/container <image-name> 
    
    # Using a mounted volume (you handle location on host) 
    $ docker run --volume /path/on/host:/path/in/container <image-name> 
    
    # List all volumes including metadata 
    $ docker volume ls

---

_Adapted from [The Ultimate Docker Cheat Sheet](https://github.com/aichbauer/the-ultimate-docker-cheat-sheet) by Lukas Aichbauel._
