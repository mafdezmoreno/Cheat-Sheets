# Select the image source
FROM ubuntu:18.04

# Update the image and install the 
# required software:

RUN \
apt update && apt upgrade -y && \
apt install -y build-essential && \
apt install -y net-tools && \
sudo apt-get install -y valgrind

# In this example I've included packages for 
# c++ development

# If you need to copy some file to the image:
# COPY my_input.txt /home

# Expose ports
# EXPOSE 80

# Define default command
CMD ["bash"]
