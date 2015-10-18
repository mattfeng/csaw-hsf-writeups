# CSAW High School Forensics Writeups

### CSAW 2013 Qualification Round
##### USB Drive (sdcard.zip)
Clicking the download gives a download to sdcard.zip. We begin downloading this supposed SD Card.  
Upon downloading and extracting, we see we are given 'hi.txt' and a .tc file. We open hi.txt and find that super secret 'Password' implies the password to the TrueCrypt volume is 'Password'. We run
> veracrypt -tc --mount sdcard.tc
to mount sdcard.tc. We then begin the analysis of the sdcard.


