# Docker CLI Cheat Sheet

Basic command line for a quick start with Docker.

I personally recommend to use the [Docker VSCode extension](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-docker). It allows to do the same of the following work, but whit a gui.

# Docker Basics

### __Get a new image__

```bash
docker pull <image_name:image_tag>
#Example:
docker pull ubuntu:latest
```


### __Docker File Template__

To automate the process or image creation, updating and installing the packages, exit the `Dockerfiles`.

See the following example and then are some examples of how to exec this script.

More info: [Dockerfile Reference](https://docs.docker.com/engine/reference/builder/)

```bash
# Select the image source
FROM ubuntu:18.04

MAINTAINER: <name> <email>

# Update the image and install the 
# required software:
RUN \
apt update && apt upgrade -y &&\
apt install -y build-essential &&\
apt install -y net-tools

# To add file from host
ADD <local_file> /<path_docker>/

# If you need to copy some file to the image:
COPY my_input.txt /home

# Expose ports
EXPOSE 80

# Define Variables
ENV VAR=var 

# Define default command
CMD ["bash"]
```

### __Run The Docker File__

The next line will create a new image using the previous Dockerfile

``` bash
# The final dot "." run the Dockerfile in the current folder
docker image build -t <new_image_name> .
                      #name
```

* `<new_image_name>`: Name of the new image to be created

### __Create & Run New Container__

The next command will create a container using a previous created image.

* `<new_container_name>`: Name of the new image to be created
* `<name_base_image>`: Name of the image used to create the container

``` bash
docker container run --name <new_container_name> -dit <name_base_image>
```

The meaning of `-dit` is: 

`-- deatach`

`-- interactive`

`-- tty`

This optional arguments are important to keep the terminal and container running once it's started.

### __Creation of Container With a Shared Folder__

```bash                 
docker container run -dit --name <new_container_name> --mount 'type=bind,source=<source>,target=<target> ubuntu:latest
```

* `<target>` (ex: `/home/code`): The folder inside the container.

* `<source>` :The local folder of the host.

* `<new_container_name>`: Name of the new image to be created

This solution allows to work locally and test the program inside de docker container (gcc, gdb, valgrind, etc.)

### __To Create a Container With Limited Resources__

```bash
docker container run -dit --name <name_new_container> <name_base_image>
```
* `<name_new_container>`: Name of the new container created
* `<name_base_image>`: Name of the image used to create the container 

### __Create an Image from a Container__

If you have a container with some specific setup or packages installed it's possible to generate a image to use it in future container creations:

```bash
docker commit <container_id> <new_image_name:new_image_tag>
```

# Exec a Container

__Important:__ The `<name>` has to be the name of the container (or the container id) but not the name of the image

### __Start/Stop a Container__

```bash
# Start
docker container start <name> 
# Stop
docker container stop <name>
docker container kill <name> #Similar to previous
```

### __Run Container Pre. Created and Started__

```bash
# Add process (bash in this case) to a running container:
docker exec -it <name>  bash

# The next command just reattach a running container:
docker attach <name>
```

### __To Check Status/Manage Containers__

```bash
# Containers running
docker container ps

# Containers stopped
docker container ps -a

# List all the images:
docker images

# To check possible errors:
docker logs <name>

# Remove container
docker rm <name>

# To save an image:
docker save -o <backup_name>.tar.gz <image_name:tag>

# To load a previously saved image:
docker load <backup_name>.tar.gz
```

## Docker Networking Basics

Default Networks:
- none:
    - Container no IP
    - `--network=none`
- host:
    - Container attached to host IP
    - Container need a port to be accessed
    - `--network=host`
- bridge:
    - Created by docker by default
    - Local internal network in docker
    - It's need a port redirection file/table to access the containers
    - `--network=bridge`

It's also possible to create private networks inside docker (it's create a network device):
```bash
docker network create <my_docker_network>
```
And then connect any container to the created network:
```bash
docker run -ti --net <my_docker_network> --name <new_container_name> bash
```
It's possible to use `ping <container_name>` to test connections between containers.

__Warning__: ["Unlike Docker on Linux, Docker-for-Mac does not expose container networks directly on the macOS host."](https://github.com/chipmk/docker-mac-net-connect)

### Docker Network None

```bash
docker container run -dit --name <name_new_container> --network none <name_image_base>
# Example:
docker container run -dit --name cont_1 --network none ub_1804_cpp
# To test the container:
docker container exec -it cont_1 bash
ifconfig #Return only 'lo' config
```
### Docker Network Bridge

Bridge is the default configuration, so it's optional to include `--network bridge`

```bash
docker container run -dit --name <name_new_container>  -p 8888:80 --network bridge <name_image_base>

# Example:
docker container run -dit --name cont_2 -p 8888:80 --network bridge ub_1804_cpp

# To test the container
docker container exec -it cont_2 bash  
ifconfig # Now appears `eth0` config    

```

### Docker Network Host

```bash
docker container run -dit --name <name_new_container> --network host <name_image_base>

# Example:
docker container run -dit --name cont_3 --network host ub_1804_cpp

# To test the container
docker container exec -it cont_3 bash  
ifconfig # Now appears `eth0`, `docker0`, `services1`    

```

### Create links (legacy)

To link manually to containers:

* `<container_1>` New container to be created
* `<image_a:latest>` Image Base for new container
* `<image_b:container_2>` Container to be linked

```bash
docker run --name <container_1> -p 8080:80 -p 8443:443 --link <image_b:container_2> <image_a:container_0>
```

# Docker Compose

To automate and escalate previous task, it's better to use `Docker Compose (docker-compose.yml)`

To run `docker-compose.yml`

```bash
docker-compose up
# or specifying a concrete file:
docker-compose -f my-compose.yml up

```

__File Format (example-template)__

```yml
version "3.3" # The last version

services: 
    <container_name_1>: #Contents all the instructions
        image: <image_name:image_tag> # Name of the image
        volumes: 
            - <container_name_1_data>: /var/lib/ # Path to volume (in host)
        restart: always
        links:
            - <container_name_2>
        environment:
            ENV_VAR_1: env_var_1

    <container_name_2>:
        depend_on:  # Automatically creates a network
            - <container_name_1>
        image: <image_name_2:image_tag_2> # Name of the image
        volumes:
            - <container_name_2_data>: /home/ # Path to host volume (in host)
        ports:
            - 8000:80
        restart: always
        environment:
            ENV_VAR_2: env_var_2

volumes:
    <container_name_1_data>{}
    <container_name_2_data>{}
```