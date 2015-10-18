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
> hsfportal.isis.poly.edu/#login  
> hsfportal.isis.poly.edu/#dropbox  

Both of these are links to a new container, [hsfportal.isis.poly.edu](http://hsfportal.isis.poly.edu).

##### hsfportal.isis.poly.edu (aka Dropbox)
Upon reaching the website, we find that in order to see any interesting files, we need to login first. After looking at the source code, we find that the login credentials are:  
```
Username: jhunkse  
Password: Do Ev1l  
```
What kind of person puts spaces in their password though?
