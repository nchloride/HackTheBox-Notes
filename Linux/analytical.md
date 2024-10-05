```shell
 Nmap 7.94SVN scan initiated Thu Oct  3 08:06:17 2024 as: nmap -sC -sV -oN nmap/analytics -T5 -v 10.129.229.224
Nmap scan report for 10.129.229.224
Host is up (0.0026s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.4 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 3e:ea:45:4b:c5:d1:6d:6f:e2:d4:d1:3b:0a:3d:a9:4f (ECDSA)
|_  256 64:cc:75:de:4a:e6:a5:b4:73:eb:3f:1b:cf:b4:e3:94 (ED25519)
80/tcp open  http    nginx 1.18.0 (Ubuntu)
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-title: Did not follow redirect to http://analytical.htb/
|_http-server-header: nginx/1.18.0 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

```

--------
Ports
80 -> metabase 
msfconsole -> linux/http/metabase_setup_token_rce
[CVE](https://github.com/m3m0o/metabase-pre-auth-rce-poc)

> env variables that contains credentials
```shell
 file_env 'MB_DB_USER'
    file_env 'MB_DB_PASS'
    file_env 'MB_DB_CONNECTION_URI'
    file_env 'MB_EMAIL_SMTP_PASSWORD'
    file_env 'MB_EMAIL_SMTP_USERNAME'
    file_env 'MB_LDAP_PASSWORD'
    file_env 'MB_LDAP_BIND_DN'

```

found credentials on environment variables

```shell
META_USER=metalytics
META_PASS=An4lytics_ds20223#

```

> ssh'ed on the machine using the creds, the machine uses a vulnerable linux version
> [exploit](https://github.com/g1vi/CVE-2023-2640-CVE-2023-32629/blob/main/exploit.sh)
> 