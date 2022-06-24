# Docker CLI Cheat Sheet

Basic command line for a quick start with Docker.

I personally recommend to use the [Docker VSCode extension](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-docker). It allows to do the same of the following work, but whit a gui.

### Docker File Template

```bash
# Select the image source
FROM ubuntu:18.04

# Update the image and install the 
# required software:
RUN \
apt update && apt upgrade -y &&\
apt install -y build-essential &&\
apt install -y net-tools

# If you need to copy some file to the image:
COPY my_input.txt /home

# Expose ports
EXPOSE 80

# Define default command
CMD ["bash"]
```

### Run the docker file

The next line will create a new image using the previous Dockerfile

``` bash
# The final dot "." run the Dockerfile in the current folder
docker image build -t ub_1804_cpp .
                      #name
```

### Run new container
The next command will create a container named v1 from the previous created image (ub_1804_cpp)
``` bash
                                    #image
docker container run --name v1 -dit ub_1804_cpp
                     #optional 
```

The meaning of `-dit` is: 

`-- deatach`

`-- interactive`

`-- tty`

This optional arguments are important to keep the terminal and container running once it's started.

###  To create the container (with a shared folder):

```bash                 
docker container run -dit --name ub_1804_cpp --mount 'type=bind,source=<source>,target=<target> ubuntu:latest
```

-- `<target>` (ex: `/home/code`): The folder inside the container.

-- `<source>` :The local folder of the host.


This solution allows to work locally and test the program inside de docker container (gcc, gdb, valgrind, etc.)

### To exec the bash of container previously created and started:

__Important:__ The `<name>` has to be the name of the container (or the container id) but not the name of the image

```bash
docker exec -it <name>  bash
```

### To start a container & execute bash

```bash
docker container start <name> 
docker exec -it <name> bash
```

### Stop a container
```bash
docker container stop <name>
```

### To check containers

```bash
# Containers running
docker container ps

# Containers stopped
docker container ps -a

# List all the images:
docker images
```