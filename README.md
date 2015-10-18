# CSAW High School Forensics Writeups

## CSAW 2013 Qualification Round
#### Given Download to USB Drive (sdcard.zip)
Clicking the download gives a download to sdcard.zip. We begin downloading this supposed SD Card.  
Upon downloading and extracting, we see we are given 'hi.txt' and a .tc file. We open hi.txt and find that super secret 'Password' implies the password to the TrueCrypt volume is 'Password'. We run
```bash
veracrypt -tc --mount sdcard.tc
```
to mount sdcard.tc. We then begin the analysis of the sdcard.

#### SD Card (sdcard.tc)
We've opened up the sdcard. Lots of these files are missing extensions, but since we are using Linux, we don't need to worry about that (sorry, Windows folks!). 

Interesting File (010965):
> Recovery Plan  
> hsfportal.isis.poly.edu/#login - if you know me, you'll be able to figure out my creds.
> hsfportal.isis.poly.edu/#dropbox - recover the **device** and continue the fight.

Both of these are links to a new container, [hsfportal.isis.poly.edu](http://hsfportal.isis.poly.edu).

#### hsfportal.isis.poly.edu (aka Dropbox)
Upon reaching the website, we find that in order to see any interesting files, we need to login first. After looking at the source code, we find that the login credentials are:  
```
Username: jhunkse  
Password: Do Ev1l  
```
What kind of person puts spaces in their password though?  
Anyways, now we can take a look at his Dropbox files.
Files:
 * AVD (Android Virtual Device)
 * Debian Credentials (j:hack4lyfe)
 * Phishing Script (Not yet uploaded...)
 * Database File
 * IDS File (Intrusion Detection System PCAP)

Three of these are new containers. Let's take a look.

#### IDS File?
> Just got my hands on this - now we can figure out how to be more stealthy :) - M

Sounds pretty ominous. We download the file (it's called IDS.zip, which has IDS.pcap inside), and open it up in Wireshark.  
Following the TCP stream, we see a couple of things worth noting.

1. The computer is related to, or is, a bank (judging by the user name).
2. The files in the My Documents include:
    * gotcha.-\_-.txt
    * personnel.db (could this be the DB file?)
    * sqlitebrowser\_200\_b1\_win (SQLite Browser, for personnel.db, most likely)
    * vlc-2.0.8-win32.exe (look's like someone isn't doing their job at work >.<)
3. gotcha.-\_-.txt is a "pwned" message:  
    Looks like your systems isnt too secure :P

From these files, we can surmise that someone was able to hack into the bank computer, and plant a file, called gotcha.txt.

#### DB
> Just incase I need to access this elsewhere...

Clicking the link appears to lead us to a download page (from hsfcdn), but unfortunately it appears broken. We'll keep our eyes out for a DB file throughout the competition, though.

#### Android Virtual Device
> HTC Desire C

Well, we see what kind of phone Joel has. We know it's Joel's phone because the downloaded file is called, aptly, "JoelsPhone.zip". This file takes a while to download.
Using AVD Manager by Android, we have to install Google API 17 to open the phone. So, we go ahead and install that using the SDK Manager that comes with the AVD Manager (all these acronyms, man!).

Applications Installed:
 * Terminal Emulator
 * Astro File Manager
 * Superuser


Call History:
 * None (Call to 888-888-8888, but seems like a junk call)

Contacts:
 * None

Browsing History:
 * Many references to hsfportal.isis.poly.edu (nothing new here though)
 * New Container: [hsfcasino.isis.poly.edu](http://hsfcasino.isis.poly.edu)
   Under both browsing history and most visited

Emails (Account: luke.hunkes@gmail.com):
 * Couple of Emails to Note:
   1. Marc Rogers (marc.rogers99@gmail.com)
   2. Jim Dibner (jimdibs7@gmail.com)
   3. Moshe Wunsch (mwunsch3@gmail.com)
   4. Geri Berns (geriberns@gmail.com)
   5. Julian Jacobs (jacobsjulian2@gmail.com)

Interesting email: **Urgent Announcement**
> All, lab has experienced some unexpected behavior in the past 24 hours. Be way this may be an attack...

Supposedly the lab has undergone an attack, and from the bank IDS, we can see that their infrastructure has indeed been hacked.

Interesting email: **Be Careful**
> Joel,
>
> I do not know the extent of your current efforts, but in any case you should worry that you are overstepping your bounds. A project in the lab is great but if that means **hazardly putting the rest of the lab as risk** it will need to be ended there.

So, the lab has some dangerous projects under progress, and some do not agree.

Interesting email: **Status**
> Guys,
>
> I need to put this out there. We are getting pummelled. **All of our services are no longer in my control**. The down time is killing everything. If I do not get this all together soon I may need to go in and physically fix all of this.

The lab is being hacked to the extent where they have no control. Supposedly, Moshe Wunsch is in charge of keeping the lab services running, but they are not in his control anymore.

Suspicious email (From:Marc Rogers / To:Luke Hunkes): **Project Info**
> I know you have testing our system in mind, but I do not see the end goal. How will this remove our adversaries.

One of the lab project's goals is to remove adversaries. That doesn't seem too nice.

Suspicious email (From: Marc Rogers / To: Luke Hunkes): **Re: Final Steps**
> I will take it that you are sure on this one. I will be there to back you up.
>
> > luke.hunkes@gmail.com wrote
> > Jim's actions have played into our hands. Julian will arrive here soon. Meet me in the back of the lab **to deal with him**.

There seems to be tension in the lab. The lab members are ready to "deal with each other", possibly with murder?

#### hsfcasino.isis.poly.edu (Gambling Site, yay!)
We can see that the CyberNutz like playing gambling games. Upon reaching the website, we are greeted with a variety of games:
 * 8 Puzzle
 * Blackjack
 * Snake
 * Quizy
 * Slots

.htaccess, robots.txt, .git:
 * Nothing.

Reading source files, and looking at the blackjack.js source code, we see an interesting snippet of code:
```javascript
/**********************
 * MR: ADDED IN THE BACKDOOR THAT JH ASKED FOR. HOPEFULLY NOBODY READS THE SOURCE!
 **********************/
if (playerScore == 35) {
    setTimeout(function() { $('#result').html("Joel must've sent you - click <a href='http://hsfcdn.isis.poly.edu/dl.php?d4a4bbf1a527791b0693c2dda2d7b4c9' target='_blank'>here</a>."); gameOver("lose") }, 500);
} else {
    setTimeout(function() { $('#result').text("Busted!"); gameOver("lose") }, 500);
}
```

We found a new [container](http://hsfcdn.isis.poly.edu/dl.php?d4a4bbf1a527791b0693c2dda2d7b4c9).  
Furthermore, we are told that this container was planted by MR (Marc Rogers, most likely), for Joel. The rabbit hole keeps getting deeper...

#### Laptop.zip (Joel's Laptop)
Two things we need to do here:
1. Firstly, it's a laptop with a split hard drive, meaning that's really annoying for analysis.
2. Secondly, we can boot it up and see what's on it, but that's just to get an overview of what's going on. We need to use FTK Imager to really see and extract files.
3. Actually, there's a third thing; we can [join](https://vmexpo.wordpress.com/2014/04/15/how-to-merge-multiple-vmdks-into-single-vmdk/comment-page-1/) the split disks together, but I'm not sure if that is necessary.

###### Laptop VM
We boot up the machine, and see that it is a Debian machine. That beings up the our Debian password that we found in the Dropbox (hsfportal). Sure enough, <pre>hack4lyfe</pre> lets us login to the machine.  

Everything that's interesting was already found below, during static analysis with FTK Imager.
We just checked the web history; there was nothing.

###### FTK Imager Files
**twitcher_creds.txt**
> luke.hunkes:hackerallday

What is twitcher, anyways? Perhaps another website? Like (guessed link) [hsftwitcher.isis.poly.edu](http://hsftwitcher.isis.poly.edu). We'll keep this in mind if we run out of containers.

Link to pykeylogger. That's pretty intriguing.  

**cybernutz.ods**  
Some sort of CyberNutz hierarchy. Joel is the boss, Marc is the "Consiglieri", Chris is underboss. There are many Leutenants, and even more Soldiers. Sounds like a cult, to be honest.

**.bash_history**  
> ping hsfbook.isis.poly.edu

Looks like we've discovered our new container. Furthermore, we see that Joel used the Metasploit Framework, maybe to exploit the Windows XP Box (Bank)?

Also to note are the TrueCrypt installation, and the large file **cybernutz**, probably a TrueCrypt volume.

**Documents/phish.csv**  
CSV Document with the details of many phished people.

**Documents/financials.db**  
This is the file that was on the Bank computer... What was Joel doing?

**cybernutz**  
Most likely a TrueCrypt volume, as it is large, and TrueCrypt is installed on his computer (truecrypt installation file found, .TrueCrypt directory). We will see if we can bruteforce the password (or are given it later on), but as of right now, we do not have the means of decrypting the TrueCrypt volume.

#### hsftwitcher.isis.poly.edu
We have to use a proxy (ipv6proxy.net) to tunnel into the website.  
Jacobs Julian posted an interesting twit:
> Nqqeq n arj ercb qhevat Unpx Avtug! Purpx vg bhg

It's a caesarian shift 13, and decodes into the fact there is a new git repo added after Hack Night. But we have no idea where this git repo is. We'll keep our eyes peeled. Let's now take a look at our other container, hsfbook.isis.poly.edu  
There's also other messages that are encrypted using Base64 and other Caesarian shifts, but they do not add anything to the storyline.

#### hsfbook.isis.poly.edu
We also have to use an ipv6 proxy to tunnel into this website.
Geri Berns posted to the wire:
> Just found some odd activity. Can somebody analyze this for me? [http://hsfcdn.isis.poly.edu/dl.php?3c7c3d292c3eb8f74b8a8a28d1f6e4e8](http://hsfcdn.isis.poly.edu/dl.php?3c7c3d292c3eb8f74b8a8a28d1f6e4e8)

We begin downloading, and see that it's a file called bankram.zip. Unzipping, there's a bankram.vmem file. We can use volatility to analyze the memory dump.

#### Final Container: bankram.zip
It's a .vmem file, meaning we will use <pre>volatility</pre> to conduct the proper memory analysis.

<pre>volatility -f bankram.vmem <b>imageinfo</b></pre>  
Suggested Profile: Windows XP Service Pack 2 (Indeed, the bank computer was running Windows XP)  
Image data and time: September 17th, 2013

<pre>volatility -f bankram.vmem <b>pslist</b></pre>  
Suspicious Process: pykeylogger.exe (This was the file that Joel had a link for on his computer. It's possible that he planted the keylogger.)

<pre>volatility -f bankram.vmem <b>psscan</b></pre>  
Again, pykeylogger.exe comes up as a process. That's suspicious.  
Another interesting process is sshd.exe. This means people could SSH into the bank computer. Is that really necessary? Or was that a measure for persistence?

<pre>volatility -f bankram.vmem <b>consoles</b></pre>  
Quite a few commands. Interesting points:  
1. ConsoleProcess csrss.exe PID: 592  
   Original title: Metasploit Courtesy Shell (TM)  
This implies that the bank was hacked by someone using Metasploit. Who had metasploit? That's right, Joel.  
2. Also many references to Mintty, SSH and Bash. I wonder what a bank operator was doing with those commands?

<pre>volatility -f bankram.vmem <b>connections</b></pre>  
None.

<pre>volatility -f bankram.vmem <b>connscan</b></pre>  
Lots of connections using ports 80 (HTTP) and 443 (HTTPS)
[https://en.wikipedia.org/wiki/List_of_TCP_and_UDP_port_numbers](https://en.wikipedia.org/wiki/List_of_TCP_and_UDP_port_numbers)

## CSAW 2013 Finals Round
#### [hsfportal.isis.poly.edu/messages](http://hsfportal.isis.poly.edu/messages)
This is pretty straight-forward - no tunnelling needed. We reach the website, and see there's a weird sounding message.
> There's this really creepy guy who's always lurking around Hack Nights.
> That night, we were all relieved that he wasn't there.
> You know, he's not that great of a student.
> But then it dawned on us, that maybe that maybe meant something.
> [Maybe he was some sort of] relative of Stratoz. (Brackets mean _unsure of what is said_)
> I've done a little bit of my own investigating.

Furthermore, there are two links: one to [hsftwitcher](http://hsftwitcher.isis.poly.edu), and another to [hsfbook](hsfbook.isis.poly.edu). Both websites, recalling from qualification rounds, require [ipv6proxy.net](http://ipv6proxy.net)

#### hsftwitcher.isis.poly.edu (Updated? Or nah...)
We tunnel in, and use Joel's credentials from preliminaries to login. But, we find nothing has changed. So, we continue on to our next container, hoping for something new.

#### hsfbook.isis.poly.edu (This one is updated...)
Yep, it's updated, with a lot more comments, too. Let's look through all of them (that'll take a while, unfortunately).

Marc Rogers uploaded the file Purpx bhg guvf njrfbzr cvpgher:
> (Decrypts to) Check out this awesome picture:

Downloading the image and running <pre>strings -10 doge2.gif</pre> on the file gives the download link to a Nexus phone. That's our next container.

#### Nexus.zip (Android Phone Analysis)
We aren't told straight away who this phone belongs to. After downloading, however, we see that it uses Google API Version 16, signifying a older phone.

**Web History:**  
1. Under "Older":
  * hsftwitcher.isis.poly.edu
  * hsfgit.isis.poly.edu
  * hsfbook.isis.poly.edu
2. Saved Pages:
  * Nothing.
3. Bookmarks:
  * Generic and Default Bookmarks.

The only new container out of all of these is [http://hsfgit.isis.poly.edu](http://hsfgit.isis.poly.edu).

**SMS:**
1. Jim Dibner
> Jim Dibner: Have you seen this awesome website yet? [http://hsfpin.isis.poly.edu](http://hsfpin.isis.poly.edu)  
> Jim Dibner: Don't worry... soon I'll be the leader of CyberNutz and I'll pay for your dinner!  
> Marc Rogers: Chill dude.. anything can happen.. It's CyberNutz  
> Marc Rogers: also.. probably should text about this..  

From the snippet of the conversation, we see that there is a new container. Secondly, we see that Marc Rogers is afraid to talk about leadership changes in the CyberNutz.  
They also had plans to meet for dinner, but Marc bailed, because he had to "take care of important stuff sorry". What important stuff?

2. M W

3. Julian Jacobs

4. Geri Berns

**Calls (Call Log):**
Multiple calls to the following:
1. Jim Dibner
  * Phone Number: 121-425-9394
2. Julian Jacobs
  * Phone Number: 121-736-6486
3. Geri Berns
  * Phone Number: 121-739-6891
4. M W (Moshe Wunsch)
  * Phone Number: 121-893-6927

**Contacts:**
Through the contacts profile "Me", we see that the phone belongs to Marc Rogers.  
Phone Number: 1 121-657-1185  
Email: marc.rogers99@gmail.com

**Applications:**
 * KeePassDroid (Nothing inside, however)

**Emails:**
1. **RE: One Man Army**  
Marc Rogers threatens the other members of the school that if they try to use what they learn in class to harm others in the real world ("practical targets"), he will get back at them. "I'm watching."

2. Drafts: Empty

