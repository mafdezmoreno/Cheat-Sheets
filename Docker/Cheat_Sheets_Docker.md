# Docker CLI Cheat Sheet

Basic command line for a quick start with Docker.

I personally recommend to use the [Docker VSCode extension](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-docker). It allows to do the same of the following work, but whit a gui.

### __Docker File Template__

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

### __To Exec the Bash of Container Previously Created and Started__

__Important:__ The `<name>` has to be the name of the container (or the container id) but not the name of the image

```bash
docker exec -it <name>  bash
```

### __To Start a Container & Execute Bash__

```bash
docker container start <name> 
docker exec -it <name> bash
```

### __Stop a Container__
```bash
docker container stop <name>
```

### __To Check Containers__

```bash
# Containers running
docker container ps

# Containers stopped
docker container ps -a

# List all the images:
docker images
```