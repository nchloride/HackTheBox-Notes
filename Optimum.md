#Metasploit 

# NMAP

```shell
# Nmap 7.94SVN scan initiated Fri Sep  6 09:22:21 2024 as: nmap -sC -sV -oA nmap/opt -T5 -p- --min-rate=1000 10.129.7.141
Nmap scan report for 10.129.7.141
Host is up (0.14s latency).
Not shown: 65534 filtered tcp ports (no-response)
PORT   STATE SERVICE VERSION
80/tcp open  http    HttpFileServer httpd 2.3
|_http-server-header: HFS 2.3
|_http-title: HFS /
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Fri Sep  6 09:24:30 2024 -- 1 IP address (1 host up) scanned in 129.32 seconds

```




> http server has [RCE vulnerability](https://www.exploit-db.com/exploits/39161), Used metasploit to gain Foothold 


# Foothold
> found credentials using winpeas

```powershell
 Some AutoLogon credentials were found
    DefaultUserName               :  kostas
    DefaultPassword               :  kdeEjDowkS*

```

> Used `post/multi/recon/local_exploit_suggester` to find privesc exploit on the machine

```shell
1   exploit/windows/local/bypassuac_eventvwr                       Yes                      The target appears to be vulnerable.
 2   exploit/windows/local/bypassuac_sluihijack                     Yes                      The target appears to be vulnerable.
 3   exploit/windows/local/cve_2020_0787_bits_arbitrary_file_move   Yes                      The service is running, but could not be validated. Vulnerable Windows 8.1/Windows Server 2012 R2 build detected!
 4   exploit/windows/local/ms16_032_secondary_logon_handle_privesc  Yes                      The service is running, but could not be validated.
 5   exploit/windows/local/tokenmagic                               Yes                      The target appears to be vulnerable.

```

> the tool found 5 exploit, since we just need to escalate our privileges, I used the fourth payload `exploit/windows/local/ms16_032_secondary_logon_handle_privesc`

> Gained root/admin access, got the flag 