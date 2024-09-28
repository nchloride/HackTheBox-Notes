#linux

# NMAP 

```shell
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-09-12 09:52 CDT
Warning: 10.129.196.139 giving up on port because retransmission cap hit (2).
Nmap scan report for 10.129.196.139
Host is up (0.14s latency).
Not shown: 64250 closed tcp ports (reset), 1282 filtered tcp ports (no-response)
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
22/tcp open  ssh     OpenSSH 7.4p1 Debian 10+deb9u7 (protocol 2.0)
| ssh-hostkey: 
|   2048 4a:71:e9:21:63:69:9d:cb:dd:84:02:1a:23:97:e1:b9 (RSA)
|   256 c5:95:b6:21:4d:46:a4:25:55:7a:87:3e:19:a8:e7:02 (ECDSA)
|_  256 d0:2d:dd:d0:5c:42:f8:7b:31:5a:be:57:c4:a9:a7:56 (ED25519)
80/tcp open  http    Apache httpd 2.4.25 ((Debian))
|_http-server-header: Apache/2.4.25 (Debian)
|_http-title: Admirer
| http-robots.txt: 1 disallowed entry 
|_/admin-dir
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

```

# Users found from /robots.txt

```
waldo
```

# files found from /admin-dir
```
credentials.txt
```

# /admin-dir/credentials.txt
```
[Internal mail account]
w.cooper@admirer.htb
fgJr6q#S\W:$P

[FTP account]
ftpuser
%n?4Wz}R$tTF7

[Wordpress account]
admin
w0rdpr3ss01!

```

# /w4ld0s_s3cr3t_d1r/credentials.txt
```shell
[Bank Account]
waldo.11
Ezy]m27}OREc$

```
# /admin-dir/contacts.txt
```
##########
# admins #
##########
# Penny
Email: p.wise@admirer.htb


##############
# developers #
##############
# Rajesh
Email: r.nayyar@admirer.htb

# Amy
Email: a.bialik@admirer.htb

# Leonard
Email: l.galecki@admirer.htb



#############
# designers #
#############
# Howard
Email: h.helberg@admirer.htb

# Bernadette
Email: b.rauch@admirer.htb

```

# db_admin.php
```
 $servername = "localhost";
  $username = "waldo";
  $password = "Wh3r3_1s_w4ld0?";

```

# index.php 
```
           $username = "waldo";
                        $password = "]F7jLHw:*G>UPrTo}~A"d6b";

```

> used hydra to try multiple password, none work

```
]F7jLHw:*G>UPrTo}~A"d6b
Wh3r3_1s_w4ld0?
%n?4Wz}R$tTF7 
w0rdpr3ss01!
fgJr6q#S\W:$P
Ezy]m27}OREc$
```

# Tried enumerating /utility-scripts path from the backup directory

```shell
/.htaccess            (Status: 403) [Size: 276]
/.htaccess.php        (Status: 403) [Size: 276]
/.htpasswd.php        (Status: 403) [Size: 276]
/.htpasswd            (Status: 403) [Size: 276]
/adminer.php          (Status: 200) [Size: 4295]

```
# Found /adminer.php
> uses vulnerable version of adminer Adminer 4.6.2, [CVE 2021-43008](https://github.com/p0dalirius/CVE-2021-43008-AdminerRead) 
> 