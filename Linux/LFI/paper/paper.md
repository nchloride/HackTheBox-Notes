# nmap 
```shell
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-10-30 08:58 CDT
Nmap scan report for 10.129.136.31
Host is up (0.24s latency).
Not shown: 997 closed tcp ports (reset)
PORT    STATE SERVICE  VERSION
22/tcp  open  ssh      OpenSSH 8.0 (protocol 2.0)
| ssh-hostkey: 
|   2048 10:05:ea:50:56:a6:00:cb:1c:9c:93:df:5f:83:e0:64 (RSA)
|   256 58:8c:82:1c:c6:63:2a:83:87:5c:2f:2b:4f:4d:c3:79 (ECDSA)
|_  256 31:78:af:d1:3b:c4:2e:9d:60:4e:eb:5d:03:ec:a0:22 (ED25519)
80/tcp  open  http     Apache httpd 2.4.37 ((centos) OpenSSL/1.1.1k mod_fcgid/2.3.9)
|_http-server-header: Apache/2.4.37 (centos) OpenSSL/1.1.1k mod_fcgid/2.3.9
|_http-generator: HTML Tidy for HTML5 for Linux version 5.7.28
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-title: HTTP Server Test Page powered by CentOS
443/tcp open  ssl/http Apache httpd 2.4.37 ((centos) OpenSSL/1.1.1k mod_fcgid/2.3.9)
|_http-generator: HTML Tidy for HTML5 for Linux version 5.7.28
|_ssl-date: TLS randomness does not represent time
|_http-title: HTTP Server Test Page powered by CentOS
|_http-server-header: Apache/2.4.37 (centos) OpenSSL/1.1.1k mod_fcgid/2.3.9
| tls-alpn: 
|_  http/1.1
| ssl-cert: Subject: commonName=localhost.localdomain/organizationName=Unspecified/countryName=US
| Subject Alternative Name: DNS:localhost.localdomain
| Not valid before: 2021-07-03T08:52:34
|_Not valid after:  2022-07-08T10:32:34
| http-methods: 
|_  Potentially risky methods: TRACE

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 27.83 seconds


```

# Found a custom header from the homepage which contains a domain name

> office.paper


![[Pasted image 20241030222706.png]]

- Site uses wordpress v 5.2.3 which has a vulnerability that discloses secret data by adding a parameter ?static=1
- https://www.exploit-db.com/exploits/47690  

# Found a subdomain http://chat.office.paper/register/8qozr226AhkCHZdyY 

> registered an account, it uses rocketchat, has bot that list and reads server files 

# credentials found ../hubot/.env

```shell
recyclops:Queenofblad3s!23
```

# Privesc https://github.com/secnigma/CVE-2021-3560-Polkit-Privilege-Esclation 

```shell
./poc.py -p=test123
# -p=<password>
su - secnigma
#as secnigma 
sudo bash

```