#cve #linux #activemq #metasploit #rce
```shell
nmap -sC -sV -F -Pn -oN nmap $IP -T5 
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-12-14 04:09 CST
Nmap scan report for 10.129.75.25
Host is up (0.0020s latency).
Not shown: 98 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.4 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 3e:ea:45:4b:c5:d1:6d:6f:e2:d4:d1:3b:0a:3d:a9:4f (ECDSA)
|_  256 64:cc:75:de:4a:e6:a5:b4:73:eb:3f:1b:cf:b4:e3:94 (ED25519)
80/tcp open  http    nginx 1.18.0 (Ubuntu)
| http-auth: 
| HTTP/1.1 401 Unauthorized\x0D
|_  basic realm=ActiveMQRealm
|_http-title: Error 401 Unauthorized
|_http-server-header: nginx/1.18.0 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 8.12 seconds

```

# Found a service running on port 61616 apachemq   ActiveMQ OpenWire transport v5.15.15 which has an RCE [cve 2023 46604](https://www.rapid7.com/blog/post/2023/11/01/etr-suspected-exploitation-of-apache-activemq-cve-2023-46604/) 

# has a metasploit module that establish a remote connection 

module name: apache_activemq_rce_cve_2023_46604

# Modified the target and payload in the module
