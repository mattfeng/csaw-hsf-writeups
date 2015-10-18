# CSAW High School Forensics Writeups

### CSAW 2013 Qualification Round
##### Given Download to USB Drive (sdcard.zip)
Clicking the download gives a download to sdcard.zip. We begin downloading this supposed SD Card.  
Upon downloading and extracting, we see we are given 'hi.txt' and a .tc file. We open hi.txt and find that super secret 'Password' implies the password to the TrueCrypt volume is 'Password'. We run
```bash
veracrypt -tc --mount sdcard.tc
```
to mount sdcard.tc. We then begin the analysis of the sdcard.

##### SD Card (sdcard.tc)
We've opened up the sdcard. Lots of these files are missing extensions, but since we are using Linux, we don't need to worry about that (sorry, Windows folks!). 

Interesting File (010965):
> Recovery Plan  
> hsfportal.isis.poly.edu/#login - if you know me, you'll be able to figure out my creds.
> hsfportal.isis.poly.edu/#dropbox - recover the **device** and continue the fight.

Both of these are links to a new container, [hsfportal.isis.poly.edu](http://hsfportal.isis.poly.edu).

##### hsfportal.isis.poly.edu (aka Dropbox)
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

##### IDS File?
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

##### DB
> Just incase I need to access this elsewhere...

Clicking the link appears to lead us to a download page (from hsfcdn), but unfortunately it appears broken. We'll keep our eyes out for a DB file throughout the competition, though.

##### Android Virtual Device
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

##### hsfcasino.isis.poly.edu (Gambling Site, yay!)
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


