
# NMAP 

```shell
nmap -sC -sV -oA nmap/nest $IP -p- -T5 --min-rate=1000
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-09-08 10:20 CDT
Nmap scan report for 10.129.199.217
Host is up (0.076s latency).
Not shown: 65533 filtered tcp ports (no-response)
PORT     STATE SERVICE       VERSION
445/tcp  open  microsoft-ds?
4386/tcp open  unknown
| fingerprint-strings: 
|   DNSStatusRequestTCP, DNSVersionBindReqTCP, Kerberos, LANDesk-RC, LDAPBindReq, LDAPSearchReq, LPDString, NULL, RPCCheck, SMBProgNeg, SSLSessionReq, TLSSessionReq, TerminalServer, TerminalServerCookie, X11Probe: 
|     Reporting Service V1.2
|   FourOhFourRequest, GenericLines, GetRequest, HTTPOptions, RTSPRequest, SIPOptions: 
|     Reporting Service V1.2
|     Unrecognised command
|   Help: 
|     Reporting Service V1.2
|     This service allows users to run queries against databases using the legacy HQK format
|     AVAILABLE COMMANDS ---
|     LIST
|     SETDIR <Directory_Name>
|     RUNQUERY <Query_ID>
|     DEBUG <Password>
|_    HELP <Command>
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port4386-TCP:V=7.94SVN%I=7%D=9/8%Time=66DDC102%P=x86_64-pc-linux-gnu%r(
SF:NULL,21,"\r\nHQK\x20Reporting\x20Service\x20V1\.2\r\n\r\n>")%r(GenericL
SF:ines,3A,"\r\nHQK\x20Reporting\x20Service\x20V1\.2\r\n\r\n>\r\nUnrecogni
SF:sed\x20command\r\n>")%r(GetRequest,3A,"\r\nHQK\x20Reporting\x20Service\
SF:x20V1\.2\r\n\r\n>\r\nUnrecognised\x20command\r\n>")%r(HTTPOptions,3A,"\
SF:r\nHQK\x20Reporting\x20Service\x20V1\.2\r\n\r\n>\r\nUnrecognised\x20com
SF:mand\r\n>")%r(RTSPRequest,3A,"\r\nHQK\x20Reporting\x20Service\x20V1\.2\
SF:r\n\r\n>\r\nUnrecognised\x20command\r\n>")%r(RPCCheck,21,"\r\nHQK\x20Re
SF:porting\x20Service\x20V1\.2\r\n\r\n>")%r(DNSVersionBindReqTCP,21,"\r\nH
SF:QK\x20Reporting\x20Service\x20V1\.2\r\n\r\n>")%r(DNSStatusRequestTCP,21
SF:,"\r\nHQK\x20Reporting\x20Service\x20V1\.2\r\n\r\n>")%r(Help,F2,"\r\nHQ
SF:K\x20Reporting\x20Service\x20V1\.2\r\n\r\n>\r\nThis\x20service\x20allow
SF:s\x20users\x20to\x20run\x20queries\x20against\x20databases\x20using\x20
SF:the\x20legacy\x20HQK\x20format\r\n\r\n---\x20AVAILABLE\x20COMMANDS\x20-
SF:--\r\n\r\nLIST\r\nSETDIR\x20<Directory_Name>\r\nRUNQUERY\x20<Query_ID>\
SF:r\nDEBUG\x20<Password>\r\nHELP\x20<Command>\r\n>")%r(SSLSessionReq,21,"
SF:\r\nHQK\x20Reporting\x20Service\x20V1\.2\r\n\r\n>")%r(TerminalServerCoo
SF:kie,21,"\r\nHQK\x20Reporting\x20Service\x20V1\.2\r\n\r\n>")%r(TLSSessio
SF:nReq,21,"\r\nHQK\x20Reporting\x20Service\x20V1\.2\r\n\r\n>")%r(Kerberos
SF:,21,"\r\nHQK\x20Reporting\x20Service\x20V1\.2\r\n\r\n>")%r(SMBProgNeg,2
SF:1,"\r\nHQK\x20Reporting\x20Service\x20V1\.2\r\n\r\n>")%r(X11Probe,21,"\
SF:r\nHQK\x20Reporting\x20Service\x20V1\.2\r\n\r\n>")%r(FourOhFourRequest,
SF:3A,"\r\nHQK\x20Reporting\x20Service\x20V1\.2\r\n\r\n>\r\nUnrecognised\x
SF:20command\r\n>")%r(LPDString,21,"\r\nHQK\x20Reporting\x20Service\x20V1\
SF:.2\r\n\r\n>")%r(LDAPSearchReq,21,"\r\nHQK\x20Reporting\x20Service\x20V1
SF:\.2\r\n\r\n>")%r(LDAPBindReq,21,"\r\nHQK\x20Reporting\x20Service\x20V1\
SF:.2\r\n\r\n>")%r(SIPOptions,3A,"\r\nHQK\x20Reporting\x20Service\x20V1\.2
SF:\r\n\r\n>\r\nUnrecognised\x20command\r\n>")%r(LANDesk-RC,21,"\r\nHQK\x2
SF:0Reporting\x20Service\x20V1\.2\r\n\r\n>")%r(TerminalServer,21,"\r\nHQK\
SF:x20Reporting\x20Service\x20V1\.2\r\n\r\n>");

Host script results:
| smb2-security-mode: 
|   2:1:0: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2024-09-08T15:24:12
|_  start_date: 2024-09-08T15:09:04

```

> Found credentials on smb /Data share

```
Username: TempUser
Password: welcome2019
```

> Found usernames in /Users share

```shell
 C.Smith                             D        0  Sun Jan 26 01:21:44 2020
  L.Frost                             D        0  Thu Aug  8 12:03:01 2019
  R.Thompson                          D        0  Thu Aug  8 12:02:50 2019

```

> L.Frost and R.Thompson did not change his password L.Frost:welcome2019

## Mounting SMB share on local directory

```shell
sudo mount -t cifs //IP/Share -o username=<user>,password=<pass>,domain=<host> /<local_dir>
```

