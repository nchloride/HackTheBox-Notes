
# /dev directory contains a private ssh key file which the filename is the name of the user on the machine 
----------
> Credentials 
	hype:heartbleedbelievethehype

> the site uses a vulnerable ssl certificate [Heartbleed vuln CVE-2014-0160](https://cisa.gov/news-events/alerts/2014/04/08/openssl-heartbleed-vulnerability-cve-2014-0160) 

> metasploit framework has an auxiliary module that exploits the vulnerability, 

# Found a variable that contains a base64 text which seems like the user password

```shell
ssh -i id_rsa -o PubKeyAccessType ssh-rsa hype@valentine.htb
```

# on the .bash_history file the user uses 
```shell
tmux -S ./dev/dev_sess
``` 

>dev_sess file was owned by root, upon using the command, it open a tmux session on where I gained root access 

