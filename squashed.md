# NMAP 
```
Nmap scan report for squashed.htb (10.129.214.36)
Host is up (0.24s latency).
Not shown: 996 closed tcp ports (reset)
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 48:ad:d5:b8:3a:9f:bc:be:f7:e8:20:1e:f6:bf:de:ae (RSA)
|   256 b7:89:6c:0b:20:ed:49:b2:c1:86:7c:29:92:74:1c:1f (ECDSA)
|_  256 18:cd:9d:08:a6:21:a8:b8:b6:f7:9f:8d:40:51:54:fb (ED25519)
80/tcp   open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-title: Built Better
|_http-server-header: Apache/2.4.41 (Ubuntu)
111/tcp  open  rpcbind 2-4 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100000  2,3,4        111/tcp   rpcbind
|   100000  2,3,4        111/udp   rpcbind
|   100000  3,4          111/tcp6  rpcbind
|   100000  3,4          111/udp6  rpcbind
|   100003  3           2049/udp   nfs
|   100003  3           2049/udp6  nfs
|   100003  3,4         2049/tcp   nfs
|   100003  3,4         2049/tcp6  nfs
|   100005  1,2,3      33936/udp6  mountd
|   100005  1,2,3      39851/tcp   mountd
|   100005  1,2,3      47586/udp   mountd
|   100005  1,2,3      53197/tcp6  mountd
|   100021  1,3,4      33036/udp   nlockmgr
|   100021  1,3,4      41287/tcp   nlockmgr
|   100021  1,3,4      44493/tcp6  nlockmgr
|   100021  1,3,4      51919/udp6  nlockmgr
|   100227  3           2049/tcp   nfs_acl
|   100227  3           2049/tcp6  nfs_acl
|   100227  3           2049/udp   nfs_acl
|_  100227  3           2049/udp6  nfs_acl
2049/tcp open  nfs     3-4 (RPC #100003)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

```

> has an NFS services running on 2049, enumerated the file using showmount

`showmount -e <IP:PORT>`

`mount -t nfs <ip>:<dir> <local_dir>`

> found 2 directory `/home/ross` and `/var/www/html`

```shell 
Export list for squashed.htb:
/home/ross    *
/var/www/html *

```


`/home/ross/Document` directory has a file named `Passwords.kdbx`

> created a user, modified the UID of the created the same with the UID on the directory to have full access on the directory

> used the commands `usermod, useradd, su`

> since the directory was synced on the server, I added a php reverse shell on the `/var/www/html` directory to gain foot hold


>At this I got stuck and had to look at the walkthrough to proceed with the box

> used the `w` command on the target machine, found that the user ross is on the machine
> 
> ([w command](https://www.geeksforgeeks.org/w-command-in-linux-with-examples/) is a tool used to check all current users on the machine) 

> .Xauthority file in the user's directory is an authorization token used by X11
> X11 can monitor user screen, mouse, and keylogs within the machine

> first I get the .Xauthority token from `/home/ross`, used the same technique that impersonates a user, then used the .Xauthority token on the target machine to take screenshot on ross' window/GUI

`xwd -root -screen -silent -display <TargetIP:0> > screenshot.xwd`

> converted the xwd file to png using `convert`

`convert screenshot.xwd ss.png`

> it displays a screenshot of the keepass interface with the root credentials


