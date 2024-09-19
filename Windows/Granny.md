# The machine uses a vulnerable IIS server v6.0 on which has an exploit [CVE-2017-7269](https://www.trendmicro.com/en_ph/research/17/c/iis-6-0-vulnerability-leads-code-execution.html) on metasploit 

# gained a shell using the exploit, used the post exploitation module named local_exploit_suggester to escalate privileges

# Found  privesc ms15_051_client_copy_image 

> learned the concept of the **migrate** module, it's used to stabilize running processes on windows machine, had an error using the privesc exploit, so I migrated running processes on the machine 