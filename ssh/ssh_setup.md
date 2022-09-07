# SSH guide setup

I've used this process to connect from my virtual machines, but It should work the same with machines connected to the same LAN.

In my case, I've set up the network virtual machine configuration with a bridge adapter. This allow de guest to obtain a IP in the local LAN.

## Server side

```
sudo apt install openssh-server
```

Check status:
```
sudo systemctl status ssh
```

If it's not working:
```
sudo systemctl start ssh
```
or reboot


Get the ip of your machine:
```
ip a
```

Check if there are open ports:
```
nmap <server IP>
```

You should see if everything thing it's ok:

```
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http
```

## Client side

Check ssh connection to server:

```
ssh <user_server>@<hostname_server>
```
or
```
ssh <user_server>@<IP_server>
```
And then enter the user password

To avoid to be asked by the password in the future (you should previously created you ssh key):
```
ssh-copy-id <user_server>@<hostname_server>
```

## Others

The VSCode extension "Remote-SSH (microsoft) facilitates a lot the process and the access to the server files.

## References

https://www.youtube.com/watch?v=5Fcf-8LPvws

https://gist.github.com/slowkow/8798394
